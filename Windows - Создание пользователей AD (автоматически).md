```powershell
$groups = @("IT", "Sales")

foreach ($groups in $groups) {
for ($i=1; $i -le 30; $i++)
     $username = "${groups}_${i}"	
     $password = ConvertTo-SecureString =String "P@ssw0rd{i}" -AsPlainText -Force 
     New-ADUser -Name $username -Path "OU=${groups},DC=ivanov,DC=company" -AccountPassword $password -Enabled $true -CannotChangePassword $true 
Add-ADGroupMember - Identity $groups -Members $username
}
```