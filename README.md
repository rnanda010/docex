/*------------------------------------------------------------
* @Author             :   Reena Gupta
* @Company            :   PwC
* @Description        :   Test class for XDFieldUpdatePriority_BatchSchedule
* @Last Modified Date :   08/05/2025
* @Last Modified By   :   Reena Gupta
------------------------------------------------------------*/
@isTest
public with sharing class XDFieldUpdatePriority_BatchSchedule_Test {
    @isTest (seeAllData=true)

    // Test method for update priority batch class execution
    static void testBatchExecution() 
    {
  
        // Create Admin user for RunAs purposes to create users and assign permission sets and setup data
        Id adminProfileId = [select Id from Profile where Name = 'System Administrator'].Id;
        User adminUser = new User(
            ProfileId = adminProfileId,
            FirstName = 'System',
            LastName = 'Administrator',
            UserName = System.now().getTime() + '@invalid.com',
            Alias = 'sadmin',
            Email = 'sysadmin@invalid.com',
            EmailEncodingKey = 'UTF-8',
            LanguageLocaleKey = 'en_US',
            LocaleSidKey = 'nl_NL',
            TimeZoneSidKey = UserInfo.getTimeZone().getID(),
            UserPermissionsSupportUser = true
        );  
        insert adminUser; 
        
        PermissionSetGroup managerGroup = [select Id, Status from PermissionSetGroup where DeveloperName='FieldForce_Manager'];
         // force calculation of the PSG if it is not already Updated
        if (managerGroup.Status != 'Updated') {
            Test.calculatePermissionSetGroup(managerGroup.Id);
        }
        
      // assign PSG to current user (this fails if PSG is Outdated)
        insert new PermissionSetAssignment(PermissionSetGroupId = managerGroup.Id, AssigneeId = adminUser.Id);
        
      System.runas(adminUser)
      {
 
        //Fetch record type of work type and work order
        Id workTypeRecordTypeId = Schema.SObjectType.WorkType.getRecordTypeInfosByName().get('FieldForce').getRecordTypeId();
        Id workOrderRecordTypeId = Schema.SObjectType.WorkOrder.getRecordTypeInfosByDeveloperName().get('Field_Meter_Operations').getRecordTypeId();
        // Create WorkType with correct classification (used by formula field)
        WorkType wt1 = new WorkType(
            Name = 'Test WT1',
            Work_Type_Classification__c = 'Collections',
            EstimatedDuration = 30,
            RecordTypeId=workTypeRecordTypeId,
            Priority__c=1
            
        );
        WorkType wt2 = new WorkType(
            Name = 'Test WT2',
            Work_Type_Classification__c = 'Revenue Protection',
            EstimatedDuration = 60,
            RecordTypeId=workTypeRecordTypeId,
            Priority__c=20
             
        );
 
        insert wt1;
        insert wt2;
 
        // Create WorkOrders with Work_Type_Classification__c = 'Collections'
        List<WorkOrder> workOrders = new List<WorkOrder>();
 
        WorkOrder wo1 = new WorkOrder(
            WorkTypeId = wt1.Id,
            External_Priority_Ranking__c = 30000,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
 
        WorkOrder wo2 = new WorkOrder(
            WorkTypeId = wt1.Id,
            External_Priority_Ranking__c = 2,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
        WorkOrder wo3 = new WorkOrder(
            WorkTypeId = wt1.Id,
            External_Priority_Ranking__c = 100000,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
 
        WorkOrder wo4 = new WorkOrder(
            WorkTypeId = wt1.Id,
            External_Priority_Ranking__c = null,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
 
        WorkOrder wo5 = new WorkOrder(
            WorkTypeId = wt1.Id,
            External_Priority_Ranking__c = 10000,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
 
        WorkOrder wo6 = new WorkOrder(
            WorkTypeId = wt1.Id,
            External_Priority_Ranking__c = 0,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
		// Create WorkOrders with Work_Type_Classification__c = 'Revenue Protection'
        WorkOrder wo7 = new WorkOrder(
            WorkTypeId = wt2.Id,
            External_Priority_Ranking__c =1,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
        WorkOrder wo8 = new WorkOrder(
            WorkTypeId = wt2.Id,
            External_Priority_Ranking__c =100000,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
        WorkOrder wo9 = new WorkOrder(
            WorkTypeId = wt2.Id,
            External_Priority_Ranking__c =1000000,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
        WorkOrder wo10 = new WorkOrder(
            WorkTypeId = wt2.Id,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
        WorkOrder wo11 = new WorkOrder(
            WorkTypeId = wt2.Id,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
        WorkOrder wo12 = new WorkOrder(
            WorkTypeId = wt2.Id,
            RecordTypeId=workOrderRecordTypeId,
            Comment_Line__c ='Update Priority'
        );
        workOrders.add(wo1);
        workOrders.add(wo2);
        workOrders.add(wo3);
        workOrders.add(wo4);
        workOrders.add(wo5);
        workOrders.add(wo6);
        workOrders.add(wo7);
        workOrders.add(wo8);
        workOrders.add(wo9);
        workOrders.add(wo10);
        workOrders.add(wo11);
        insert workOrders;
        Test.startTest();
        
        XDFieldUpdatePriority_BatchSchedule  batch = new XDFieldUpdatePriority_BatchSchedule ();
        ID batchId = Database.executeBatch(batch, 200);
 
        insert wo12;
        String cron = '0 0 0 1 1 ? 2099'; 
        System.schedule('XDFieldUpdatePriority_BatchSchedule_SchedulableTest', 
                        cron, 
                        new XDFieldUpdatePriority_BatchSchedule());
        
        Test.stopTest();
 
        // Validate all the WorkOrders are updated with correct Dynamic_Priority__c
        List<WorkOrder> updatedWOs = [
            SELECT Id, Dynamic_Priority__c FROM WorkOrder
            WHERE Id in :workOrders and Dynamic_Priority__c != null
        ];
        System.assertEquals(workOrders.size(), updatedWOs.size(), '11 work orders should be processed.');
        // Assert ServiceAppointments updated
        List<ServiceAppointment> updatedSAs = [
            SELECT Id, FSSK__FSK_Work_Order__c, Dynamic_Priority__c FROM ServiceAppointment WHERE FSSK__FSK_Work_Order__c IN :updatedWOs
        ];
        System.assertEquals(updatedWOs.size(), updatedSAs.size(), 'All related ServiceAppointments should be updated');
        
        // Assert the WO was processed by the schedulable path 
        WorkOrder wo = [SELECT Dynamic_Priority__c FROM WorkOrder WHERE Id = :wo12.Id]; 
        System.assertNotEquals(null, wo.Dynamic_Priority__c, 
                             'WO priority should be set via schedulable execute'); 
      }
        
    }
    
    // Test method for calculation matrix
    @isTest
    static void testCalculationMatrix(){
      
        // Create Admin user for RunAs purposes to create users and assign permission sets and setup data
        Id adminProfileId = [select Id from Profile where Name = 'System Administrator'].Id;
        User adminUser = new User(
            ProfileId = adminProfileId,
            FirstName = 'System',
            LastName = 'Administrator',
            UserName = System.now().getTime() + '@invalid.com',
            Alias = 'sadmin',
            Email = 'sysadmin@invalid.com',
            EmailEncodingKey = 'UTF-8',
            LanguageLocaleKey = 'en_US',
            LocaleSidKey = 'nl_NL',
            TimeZoneSidKey = UserInfo.getTimeZone().getID(),
            UserPermissionsSupportUser = true
        );  
        insert adminUser; 
        
        PermissionSetGroup managerGroup = [select Id, Status from PermissionSetGroup where DeveloperName='FieldForce_Manager'];
         // force calculation of the PSG if it is not already Updated
        if (managerGroup.Status != 'Updated') {
            Test.calculatePermissionSetGroup(managerGroup.Id);
        }
        
      // assign PSG to current user (this fails if PSG is Outdated)
        insert new PermissionSetAssignment(PermissionSetGroupId = managerGroup.Id, AssigneeId = adminUser.Id);
        
      System.runas(adminUser)
      {
        Test.startTest();
        XDFieldUpdatePriority_BatchSchedule  batch = new XDFieldUpdatePriority_BatchSchedule ();
        ID batchId = Database.executeBatch(batch, 200);
        String cron = '0 0 0 1 1 ? 2099'; 
        System.schedule('XDFieldUpdatePriority_BatchSchedule_SchedulableTest', 
                        cron, 
                        new XDFieldUpdatePriority_BatchSchedule());
        
        Test.stopTest();
    }
    }
}
