/**
 * This class contains unit tests for validating the behavior of Apex classes
 * and triggers.
 *
 * Unit tests are class methods that verify whether a particular piece
 * of code is working properly. Unit test methods take no arguments,
 * commit no data to the database, and are flagged with the testMethod
 * keyword in the method definition.
 *
 * All test methods in an org are executed whenever Apex code is deployed
 * to a production org to confirm correctness, ensure code
 * coverage, and prevent regressions. All Apex classes are
 * required to have at least 75% code coverage in order to be deployed
 * to a production org. In addition, all triggers must have some code coverage.
 * 
 * The @isTest class annotation indicates this class only contains test
 * methods. Classes defined with the @isTest annotation do not count against
 * the org size limit for all Apex scripts.
 *
 * See the Apex Language Reference for more information about Testing and Code Coverage.
 */
	@isTest
	private class UnifiedCRMCaseTriggerTest {
    @testSetup static void setup(){
        
        Profile testProfile = [Select id from Profile where Name='Customer Service Users' LIMIT 1];
        user testUser = new User(lastname='test',firstname='testfirstname', alias='tetst', username='test@fpl.com.xddev', email='test@fpl.com',profileid=testProfile.Id,
                                 timezonesidkey='GMT', languageLocaleKey='en_US', EmailEncodingKey='UTF-8',LocaleSidKey='en_US');
        
        INSERT testUser;
        recordtype caseRecordType = [Select id, name from recordtype where name = :UnifiedCRM_GlobalConstants.caseRecordTypeName LIMIT 1];
       
        
	Case newcase = new Case(Sub_Category__c= 'Light Removal', Origin='User',Category__c='Move Out', type='Unified CRM Case', recordtypeid = caseRecordType.id );
     INSERT newcase;
	}
        
    @isTest
    static void insertCasesTest() {
        Case myCase = new Case(Sub_Category__c= 'Light Removal', Origin='User',Category__c='Move Out', type='Unified CRM Case');        
        Test.startTest();
        INSERT myCase;
        Test.stopTest();
        Case newCase = [SELECT ID,Sub_Category__c FROM Case Where Id=:myCase.Id];
        System.assertEquals('Light Removal',myCase.Sub_Category__c);
    }
        
     @isTest
    static void updateCasesTest() {
        Case myCase = new Case(Sub_Category__c= 'Light Removal', Origin='User',Category__c='Move Out', type='Unified CRM Case');
        INSERT myCase;
        UnifiedCRMCaseHandler.runOnce=false;
        myCase.Sub_Category__c ='FPL.com / Mobile App Issue';
        Test.startTest();
        UPDATE myCase;
        Test.stopTest();
        Case newCase = [SELECT ID,Sub_Category__c FROM Case Where Id=:myCase.Id];
        System.assertEquals('FPL.com / Mobile App Issue',myCase.Sub_Category__c);
        
    }
        
    @isTest
    static void afterUpdateCasesTest() { 
       case myCase=  [SELECT ID,Sub_Category__c FROM Case Where Sub_Category__c='Light Removal' limit 1];
        myCase.Sub_Category__c ='FPL.com / Mobile App Issue';
         UnifiedCRMCaseHandler.runOnce=false;
        Test.startTest();
        UPDATE myCase;
        Test.stopTest();
        Case newCase = [SELECT ID,Sub_Category__c FROM Case Where Id=:myCase.Id];
        System.assertEquals('FPL.com / Mobile App Issue',myCase.Sub_Category__c);
    }  
        
     //test exception
     @isTest
     static void updateCasesTestErrorHandling() {
         
         user testUser = [select id from user where username='test@fpl.com.xddev' LIMIT 1];
         
         Case myCase = new Case(Sub_Category__c= 'Light Removal', Origin='User', type='Unified CRM Case');
         INSERT myCase;
                 
         system.runAs(testUser){
             UnifiedCRMCaseHandler.runOnce=false;
             myCase.Sub_Category__c ='test';
             myCase.Priority ='Critical';
             myCase.ownerid =testUser.id;
             Test.startTest();
             try{
                 UPDATE myCase;
             }catch(DMLException ex){
                 system.Debug('INSUFFICIENT_ACCESS_ON_CROSS_REFERENCE_ENTITY   '+ex.getMessage());
             }
             
             Test.stopTest();
         }
        
         Case newCase = [SELECT ID,Sub_Category__c FROM Case Where Id=:myCase.Id];
         System.assertEquals('test',myCase.Sub_Category__c);
     }
        
        @isTest
        static void UpdateStatusTest() {
           case myCase=  [SELECT ID,Sub_Category__c FROM Case Where Sub_Category__c='Light Removal' limit 1];
           Profile testProfile = [Select id from Profile where Name='Customer Service Users' LIMIT 1];
           user newUser = new User(lastname='test',firstname='testfirstname', alias='tetst', username='test1@fpl.com.xddev', email='test@fpl.com',profileid=testProfile.Id,
                                 timezonesidkey='GMT', languageLocaleKey='en_US', EmailEncodingKey='UTF-8',LocaleSidKey='en_US');
            insert newUser;
            myCase.Sub_category__c='Billing Programs - Fixed Rate';
            myCase.Category__c='Programs';
            myCase.ownerid =newUser.id;
            Test.startTest();
            UPDATE myCase;
            Test.stopTest();
            case updatedCase=  [SELECT ID,Sub_Category__c,status FROM Case Where Sub_Category__c='Billing Programs - Fixed Rate' limit 1];
            System.assertEquals('New',updatedCase.Status);

          }
        
}
