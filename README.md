# WN10-AC-000005-mitigation-with-Powershell.

# Requires running as an administrator
# Define the path for the temporary security policy file
$policyFile = "C:\Temp\policy.inf"

# Define the new policy setting
$policyContent = "[System Access]
MinimumPasswordLength = 14"

# Check if the temporary directory exists, if not, create it
if (-not (Test-Path -Path C:\Temp)) {
    New-Item -Path C:\Temp -ItemType Directory
}

try {
    # Write the policy content to the file
    Set-Content -Path $policyFile -Value $policyContent -Force
    
    # Apply the policy
    Write-Host "Applying new password policy..."
    secedit.exe /configure /db "C:\Windows\temp\temp.sdb" /cfg $policyFile /areas SYSTEMACCESS /overwrite
    
    # Confirm the policy has been applied
    Write-Host "Password policy applied successfully. Minimum password length is now 14."
}
catch {
    Write-Error "Failed to apply the password policy. Please ensure you are running as an administrator."
    Write-Error $_.Exception.Message
}
finally {
    # Clean up the temporary file
    if (Test-Path -Path $policyFile) {
        Remove-Item -Path $policyFile
    }
}
