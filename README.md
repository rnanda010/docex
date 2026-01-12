[2026-01-12T18:50:03.774677Z] Running validation via Windows PowerShell script…
Run ID: 8ec79859-0f0f-491a-8805-1dbacaa6e5cc
Pull request: 21256
Script: D:\prism\powershell\runvalidation.ps1
Repo path: D:\Users\RXN0ODA\Review History\prism\Cloned
Source: feature/xdcrm-case-SFCASE-3788_Case360_WO_RemoveNewButton
Target: XDCRMTEST

=== STEP 1: Verify git remote exists (force origin) - START ===
Using git remote: origin
Normalized branches: Source=[feature/xdcrm-case-SFCASE-3788_Case360_WO_RemoveNewButton] Target=[XDCRMTEST] Remote=[origin]
=== STEP 1: Verify git remote exists (force origin) - DONE ===

=== STEP 2: Checkout + reset TARGET branch to origin/XDCRMTEST - START ===

D:\prism\powershell\runvalidation.ps1 : FAILED at STEP 2: Checkout + reset TARGET branch to origin/XDCRMTEST
Reason: From https://github.com/NextEraEnergy/bv527-fpls-salesforce
At D:\prism\powershell\runvalidation.ps1:286 char:5
+     Write-Error ("FAILED at STEP {0}: {1}`nReason: {2}" -f $Global:St ...
+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,runvalidation.ps1

[2026-01-12T18:50:26.674158Z] Script completed (exit 1, 22.91s)

Validation errored out. Check the console for details.
