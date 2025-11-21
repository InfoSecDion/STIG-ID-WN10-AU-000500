# STIG-ID: WN10-AU-000500 - Set Event Log Max Size

## Synopsis
This PowerShell script ensures that the maximum size of the Windows Application event log is at least 32768 KB (32 MB).

## Notes
- **Author**: Dion Alexander
- **LinkedIn**: https://www.linkedin.com/in/infosecdion
- **GitHub**: https://github.com/InfoSecDion
- **Date Created**: 2025-11-19
- **Last Modified**: 2025-11-19
- **Version**: 1.0
- **CVEs**: N/A
- **Plugin IDs**: N/A
- **STIG-ID**: WN10-AU-000500

## Tested On
- **Date(s) Tested**: 
- **Tested By**: 
- **Systems Tested**: 
- **PowerShell Ver.**: 

## Usage
Put any usage instructions here.

Example syntax:



````markdown
```powershell
# --- Configuration ---
$LogKey = 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\EventLog\Application'
$PropertyName = 'MaxSize'
$DesiredSizeKB = 32768
$DesiredSizeHex = '{0:X}' -f $DesiredSizeKB

Write-Host "Validating STIG requirement WN10-AU-000500..." -ForegroundColor Cyan

# --- Ensure registry path exists ---
if (-not (Test-Path $LogKey)) {
    Write-Host "Registry path not found. Creating: $LogKey"
    New-Item -Path $LogKey -Force | Out-Null
}

# --- Retrieve current value (if any) ---
$current = (Get-ItemProperty -Path $LogKey -Name $PropertyName -ErrorAction SilentlyContinue).$PropertyName

if ($null -eq $current) {
    Write-Host "No existing MaxSize value found. Creating new value..."
    New-ItemProperty -Path $LogKey -Name $PropertyName -Value $DesiredSizeKB -PropertyType DWORD -Force | Out-Null
}
else {
    Write-Host "Current MaxSize: $current KB"
    if ($current -lt $DesiredSizeKB) {
        Write-Host "Current value is below STIG minimum. Updating to $DesiredSizeKB KB..."
        Set-ItemProperty -Path $LogKey -Name $PropertyName -Value $DesiredSizeKB
    }
    else {
        Write-Host "MaxSize already meets or exceeds STIG requirement. No change needed."
    }
}

Write-Host "Completed. Application Event Log MaxSize is set to $DesiredSizeKB KB (0x$DesiredSizeHex)."
