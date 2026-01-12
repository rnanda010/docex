"c87f96ab-bab9-4e06-a02a-1475e07f6832": {
    "id": "c87f96ab-bab9-4e06-a02a-1475e07f6832",
    "pr_number": 21166,
    "source_branch": "feature/xdcrm-field_SFFIELD-3806-Helper_Ticket",
    "target_branch": "XDCRMTEST",
    "status": "error",
    "created_at": 1768188775.446527,
    "started_at": 1768188775.4533746,
    "completed_at": 1768188786.1745882,
    "duration": 10.719000000040978,
    "log": "[2026-01-12T03:32:55.461303Z] Running validation via Windows PowerShell script\u2026\nRun ID: c87f96ab-bab9-4e06-a02a-1475e07f6832\nPull request: 21166\nScript: D:\\prism\\powershell\\runvalidation.ps1\nRepo path: D:\\Users\\RXN0ODA\\Review History\\prism\\Cloned\nSource: feature/xdcrm-field_SFFIELD-3806-Helper_Ticket\nTarget: XDCRMTEST\n\n=== STEP 1: Verify git remote exists (force origin) - START ===\nUsing git remote: origin\n=== STEP 1: Verify git remote exists (force origin) - DONE ===\n\n=== STEP 2: Checkout + reset TARGET branch to origin/XDCRMTEST - START ===\n\nD:\\prism\\powershell\\runvalidation.ps1 : FAILED at STEP 2: Checkout + reset TARGET branch to origin/XDCRMTEST\nReason: From https://github.com/NextEraEnergy/bv527-fpls-salesforce\nAt D:\\prism\\powershell\\runvalidation.ps1:266 char:5\n+     Write-Error (\"FAILED at STEP {0}: {1}`nReason: {2}\" -f $Global:St ...\n+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n    + CategoryInfo          : NotSpecified: (:) [Write-Error], WriteErrorException\n    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,runvalidation.ps1\n\n[2026-01-12T03:33:06.155122Z] Script completed (exit 1, 10.69s)\n\nValidation errored out. Check the console for details.",
    "error": "D:\\prism\\powershell\\runvalidation.ps1 : FAILED at STEP 2: Checkout + reset TARGET branch to origin/XDCRMTEST\nReason: From https://github.com/NextEraEnergy/bv527-fpls-salesforce\nAt D:\\prism\\powershell\\runvalidation.ps1:266 char:5\n+     Write-Error (\"FAILED at STEP {0}: {1}`nReason: {2}\" -f $Global:St ...\n+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n    + CategoryInfo          : NotSpecified: (:) [Write-Error], WriteErrorException\n    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,runvalidation.ps1",
    "_started_monotonic": 696875.343
  }
}
