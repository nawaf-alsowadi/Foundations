Enforce a rule of behavior for users to more compliant behavior. We can use System security and User Acount Control (UAC) policies windows register:
`HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\System`

To add a guide to the users when they login to the machine.

Steps:
- Open PS as admin
- Assign the following var the guidance: 
```powershell
$BannerText = "This computer system is the property of Structureality Inc. It is for authorized use only. By using this system, all users acknowledge notice of and agree to comply with the Acceptable Use Policy (AUP). Unauthorized or improper use of this system may result in administrative disciplinary action, civil charges/criminal penalties, and/or other sanctions set forth in the AUP. By continuing to use this system, you indicate your awareness of and consent to these terms and conditions. If you are physically located in the European Union, you may have additional rights per the GDPR. Visit the website gdpr-info.eu for more information."
```
- Create a registry value for the title of the message: 
```powershell
New-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\System" `
  -Name "legalnoticecaption" `
  -Value "Authorized Use Only" `
  -PropertyType "String" `
  -Force | Out-Null
```
**-New-ItemProperty** → Creates a registry value
**-Path** → Specifies the registry key
**-Name "legalnoticecaption"** → Sets the _title_ of the logon message box
**-Value "Authorized Use Only"** → Text shown in the title bar
**-PropertyType "String"** → Creates a REG_SZ (string) value
**-Force** → Overwrites it if it already exists
**`| Out-Null`** → Suppresses output (keeps console clean)

- Create a registry value for the message:
```powershell
New-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\System" `
  -Name "legalnoticetext" `
  -Value $BannerText `
  -PropertyType "String" `
  -Force | Out-Null
```
**-Name "legalnoticetext"** → Sets the _message_ of the logon message box

Example when login: 
![[Pasted image 20260225023732.png]]