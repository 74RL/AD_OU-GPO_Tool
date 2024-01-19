# AD_OU-GPO_Tool
Active Directory Tool for creating organizational units (OU) and displaying / adding group policy objects (GPO).

If you want to use this tool like it is, you will need a AD structure like this:
```
Domain (e.g. Company.dom)
    |
    └─ Sites (e.g. London)
          |
          |
          └─ User
          |     └─ Department (e.g. Sales)
          |
          └─ Computer
                └─ Department (e.g. Sales)

DN: OU=Sales,OU=User,OU=Sites,CN=Company,CN=dom
```
Else you should edit it for your purpose.




---
![image](https://github.com/74RL/AD_OU-GPO_Tool/assets/153525115/2062a100-7d05-472b-92d7-711da093c62e)
---


## What can it do?


### 1. Creating organizational units (OU)


**1.1 Create an OU below Sites (User/Computer Level):**
+ choose a Site from the SiteDropdown (e.g. London)
+ let the User/ComputerDropdown untouched/empty
+ name your OU (e.g. Groups)
+ press "Create new OU"
```
Company.dom
    |
    └─ London
          |
          |
          └─ User
          |     └─ Sales
          |
          └─ Computer
          |     └─ Sales
          |
          └─ Groups
```


**1.2 Create an OU below User/Computer (Department Level):**
+ choose a Site from the SiteDropdown (e.g. London)
+ decide if you want to create a User OU or Computer OU or both
+ if you want to create only a user or computer OU, choose the User OU in the UserDropdown or the Computer OU in the ComputerDropdown
+ if you want to create both, fill both dropdown boxes
+ name your OU (e.g. Legal)
+ press "Create new OU" or "Create a new User and Computer OU"
+ if you pressed "Create a new User and Computer OU":
```
Company.dom
    |
    └─ London
          |
          |
          └─ User
          |     └─ Sales
          |     └─ Legal      
          |
          └─ Computer
          |     └─ Sales
          |     └─ Legal
          |
          └─ Groups
```


**1.3 Create an OU below Departments:**
+ choose a Site from the SiteDropdown (e.g. London)
+ decide if you want to create a User OU or Computer OU or both
+ fill the dropdown box(es) for your purpose (e.g. Legal)
+ name your OU (e.g. Win11)
+ press "Create new OU" or "Create a new User and Computer OU"
+ if you pressed "Create a new User and Computer OU" and filled both dropdown boxes:
```
Company.dom
    |
    └─ London
          |
          |
          └─ User
          |     └─ Sales
          |     └─ Legal
          |          └─ Win11      
          |
          └─ Computer
          |     └─ Sales
          |     └─ Legal
          |          └─ Win11
          |
          └─ Groups
```
---

### 2. Adding group policy objects (GPO)

**2.1 Add GPOs with inputbox:**
+ write the DisplayName of your GPOs in the textbox
    + left textbox: user
    + right textbox: computer
+ write the GPOs one below the other and without blank spaces
+ use the dropdown boxes to find the OU where you want to add the GPOs or create a new OU
+ press "Add User GPOs" or "Add Computer GPOs" or "Add both GPOs" concerning your purpose


**2.2 Add GPOs with .txt file:**
+ create a .txt file and write the DisplayName of your GPOs in there
+ write the GPOs one below the other and without blank spaces
+ save the file
+ press the button "Open File" (remember using the right one for your purpose)
+ find your file and choose it
+ now your textbox should be filled like you wrote it
+ use the dropdown boxes to find the OU where you want to add the GPOs or create a new OU
+ press "Add User GPOs" or "Add Computer GPOs" or "Add both GPOs" concerning your purpose


**2.3 Add GPOs with default .txt file:**
+ create a .txt file and write the DisplayName of your GPOs in there
+ write the GPOs one below the other and without blank spaces
+ save the file in a folder named "UserGPOS" or "ComputerGPOs" concerning your purpose
+ move the folder in the folder where the script is saved
+ after that, you should be able to use the  "User-GPOs" and/or "Computer-GPOs" buttons in order to fill the textboxes
---

### 3. Viewing group policy objects (GPO)
+ choose the Site OU
+ choose the OU where you want to see the GPOs from
+ you can only use one Dropdown (User or Computer)
+ press the button "Show GPOs"
+ the outputbox above the button should be now filled if any GPOs are linked to the chosen OU

---
# DISCLAIMER
Please note that the script may require administrative rights to run.
Always ensure you understand what the script does before running it, especially if it requires elevated permissions.
Running scripts with administrative rights can make significant changes to your system or network, and should only be done with a clear understanding of the script’s function.
If you’re unsure, seek advice from a trusted and knowledgeable individual.
---


## Configuration


**1. Edit the AD-Query for your purpose:**
+ line 135:
```ps1
$sites = Get-ADOrganizationalUnit -SearchBase 'OU=Sites,DC=Domainname,DC=dom/com' -Filter * | Where-Object { $_.DistinguishedName -match '^ou=[^,]+,OU=Sites,DC=Domainname,DC=dom/com$' }
```


**2. Decide if you want to block GP-Inheritance on created OUs:**
+ line 288:
```ps1
Set-GPInheritance -Target $newOUDN -IsBlocked Yes
```
+ line 351 + 352:
```ps1
Set-GPInheritance -Target $newUserOUDN -IsBlocked Yes
Set-GPInheritance -Target $newComputerOUDN -IsBlocked Yes
```
+ if you do not need it, simply comment/cut it out


**3. Decide if you want the created OUs to be protected from accidental deletion:**
+ line 285:
```ps1
Get-ADObject -Identity  $newOUDN | Set-ADObject -ProtectedFromAccidentalDeletion:$false
```
+ line 347 + 348:
```ps1
Get-ADObject -Identity  $newUserOUDN | Set-ADObject -ProtectedFromAccidentalDeletion:$false
Get-ADObject -Identity  $newComputerOUDN | Set-ADObject -ProtectedFromAccidentalDeletion:$false