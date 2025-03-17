# Partie 1 : Gestion des utilisateurs
**Q.1.1.1** 
Get-ADUser -Identity "Kelly Rhameur" | New-ADUser -Name "Lionel Lemarchand" -SamAccountName "llemarchand" -UserPrincipalName "llemarchand@HS.dom" -Path "OU=LabUsers,DC=HS,DC=dom" -PassThru | Enable-ADAccount

![Capture d'écran 2025-03-17 093258](https://github.com/user-attachments/assets/1e9c5dd8-13f1-4b5e-9748-641f9d8698c5)

**Q.1.1.2**
New-ADOrganizationalUnit -Name "DeactivatedUsers" -Path "DC=HS,DC=dom"

Move-ADObject -Identity "CN=Kelly Rhameur,OU=LabUsers,DC=HS,DC=dom" -TargetPath "OU=DeactivatedUsers,DC=domaine,DC=local"

![Capture d'écran 2025-03-11 104533](https://github.com/user-attachments/assets/a78338c4-2ea3-45fc-9f8c-ef42bf4ac6cf)

**Q.1.1.3**
Remove-ADGroupMember -Identity "LabUsers" -Members "krhameur" -Confirm:$false  

Add-ADGroupMember -Identity "DeactivatedGroup" -Members "krhameur"

![Capture d'écran 2025-03-17 094026](https://github.com/user-attachments/assets/a7c38796-9a39-4e6f-9670-6b10f7e24a58)

**Q.1.1.4**
New-Item -Path "C:\Archive" -ItemType Directory

New-SmbShare -Name "Archive" -Path "C:\Archive" -FullAccess "Everyone"

$source = "c:\Users\krhameur"

$archive = "c:\Archive\krhameur.zip"

Compress-Archive -Path $source -DestinationPath $archive -Force

New-Item -ItemType Directory -Path "C:\Users\llemarchand"

# Partie 2 : Restriction utilisateurs
**Q.1.2.1**

New-ADUser -Name "Gabriel Ghul" -SamAccountName "gguhl" -UserPrincipalName "gguhl@HS.dom" -Path "OU=LabUsers,DC=HS,DC=dom" -GivenName "Gabriel" -Surname "Ghul" -AccountPassword (ConvertTo-SecureString "TempPassword123" -AsPlainText -Force) -Enabled $true -PassThru

$hours = @(
    "Mon:7-17",
    "Tue:7-17",
    "Wed:7-17",
    "Thu:7-17",
    "Fri:7-17"
)

$binaryHours = ConvertTo-ADLogonHours -Hours $logonHours

Set-ADUser -Identity "Gabriel Ghul" -LogonHours $hours

**Q.1.2.2**
Set-ADUser -Identity "Gabriel Ghul" -LogonWorkstations "CLIENT01"

![Capture d'écran 2025-03-17 095000](https://github.com/user-attachments/assets/c23d7330-3350-4342-8f68-18329d935942)

**Q.1.2.3**

✔️$GPO = Get-GPO -Name "LabUsers Password Policy"

✔️Set-GPRegistryValue -Name $GPO.DisplayName -Key "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -ValueName "MinimumPasswordLength" -Type DWord -Value 8

✔️Set-GPRegistryValue -Name $GPO.DisplayName -Key "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -ValueName "PasswordComplexity" -Type DWord -Value 1

✔️New-GPLink -Name "LabUsers Password Policy" -Target "OU=LabUsers,DC=HS,DC=dom"
![Capture d'écran 2025-03-17 101301](https://github.com/user-attachments/assets/7a0203cd-d5e3-4e5e-8e29-6c3d2686a80c)
![Capture d'écran 2025-03-17 101841](https://github.com/user-attachments/assets/a17f40bf-3a3e-4b94-bd7b-35aa8732d6f9)

# Partie 3 : Lecteurs réseaux
**Q.1.3.1**
New-GPO -Name "Drive-Mount" -Comment "GPO pour monter les lecteurs E: et F: sur les clients"

New-GPLink -Name "Drive-Mount" -Target "OU=LabUsers,DC=HS,DC=dom"

***via l'interface graphique :***

✔️Ouvre la console de gestion des stratégies de groupe (gpmc.msc).

✔️Navigue vers GPOs > Drive-Mount.

✔️Fais un clic droit dessus et sélectionne Modifier.

Dans l'éditeur de GPO :

✔️Va dans Configuration utilisateur > Préférences > Paramètres Windows > Lecteurs mappés.

✔️Fais un clic droit, puis Nouveau > Lecteur mappé.

✔️Ajoute les lecteurs :

✔️Lettre du lecteur : E:

✔️Chemin UNC : \\serveur\partage

✔️action : Créer

Fais pareil pour F:

✔️Ferme et applique la GPO.

![Capture d'écran 2025-03-17 102517](https://github.com/user-attachments/assets/10768922-9fbf-411c-a35c-14b405572ee5)

gpupdate /force







