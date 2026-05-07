
# Active Directory Home Lab — Windows Server 2025

A hands-on home lab simulating a full enterprise Active Directory environment built from scratch on Windows Server 2025. This project covers everything from domain controller promotion and DNS configuration to OU structure, security group creation, user provisioning, Group Policy enforcement, and client machine domain-joining — mirroring real-world identity and access management workflows. I already did a version of this last year (2025) but this is more updated!

---

## Environment

| Component | Details |
|---|---|
| Domain Controller OS | Windows Server 2025 |
| Client Machine OS | Windows 11 Pro |
| Domain Name | `lab.local` |
| NetBIOS Name | `LAB` |
| DC Hostname | `DC1` |
| Client Hostname | `DESKTOP-15Q3OG7` |
| DC IP Address | `192.168.20.5` |
| Forest / Domain Functional Level | Windows Server 2025 |

---

## What Was Built

### 1. Initial Server Manager Setup
Opened Server Manager on the fresh Windows Server 2025 install. At this stage only File and Storage Services is present — no AD DS or DNS yet.

![Server Manager initial state](01_server-manager-initial.png)

---

### 2. Static IP & Network Configuration
Assigned a static IPv4 address (`192.168.20.5`) to the domain controller with a `/24` subnet mask before promoting it.

![Static IP configuration](02_static-ip-configuration.png)

Verified network connectivity from the client machine with a successful ping to the DC — 4 packets sent, 0% packet loss.

![Ping verification from client](03_ping-verification.png)

---

### 3. AD DS & DNS Role Installation
Used the **Add Roles and Features Wizard** in Server Manager to install Active Directory Domain Services and DNS Server together on the same server.

![Selecting AD DS and DNS roles](04_role-installation-adds-dns.png)

---

### 4. Domain Controller Promotion
Ran the **AD DS Configuration Wizard** to promote the server to a domain controller. Selected **Add a new forest** with root domain name `lab.local`.

![Deployment configuration - new forest](05_dc-promotion-new-forest.png)

Set the Forest and Domain Functional Level to **Windows Server 2025**, enabled DNS Server and Global Catalog (GC), and configured the DSRM password.

![Domain Controller options](06_dc-options-functional-level.png)

Acknowledged the DNS delegation warning (expected for a new isolated lab forest) and left DNS delegation unchecked.

![DNS Options](07_dns-options.png)

NetBIOS name auto-resolved to `LAB`.

![NetBIOS name](08_netbios-name.png)

Confirmed default AD DS database, log, and SYSVOL paths under `C:\Windows\`.

![AD DS paths](09_ad-ds-paths.png)

Reviewed final configuration summary before install.

![Review options](10_review-options.png)

All prerequisite checks passed successfully.

![Prerequisites check passed](11_prerequisites-check-passed.png)

After reboot, Server Manager confirmed **AD DS** and **DNS** roles both active and healthy (Roles: 3).

![Server Manager post-promotion with AD DS and DNS](12_server-manager-adds-dns-active.png)

---

### 5. Organizational Unit (OU) Structure
Opened **Active Directory Users and Computers** and verified the `lab.local` domain was live.

![ADUC - lab.local domain](13_aduc-lab-local-domain.png)

Created a department-based OU structure under `lab.local`:

```
lab.local
├── IT
├── HR
├── Finance
└── Workstations
```

![OU structure - all departments](14_ou-structure.png)

---

### 6. Security Groups
Created a dedicated security group inside each department OU.

**IT_Group** in the IT OU:

![IT_Group security group](15_it-group-security-group.png)

**HR_Group** in the HR OU:

![HR_Group security group](16_hr-group-security-group.png)

**Finance_Group** in the Finance OU:

![Finance_Group security group](17_finance-group-security-group.png)

---

### 7. User Provisioning
Created a new domain user **Alice Doe** (`adoe@lab.local`) inside the HR OU.

![New user - Alice Doe details](18_new-user-alice-doe.png)

Set account password and configured password options.

![New user - password setup](19_new-user-password-setup.png)

Confirmed user creation summary before finishing.

![New user - confirmation](20_new-user-confirmation.png)

Added Alice Doe to `HR_Group` — confirmed via the group's Members tab.

![HR_Group members - Alice Doe](21_hr-group-members-alice-doe.png)

---

### 8. Group Policy Objects (GPO)
Opened **Group Policy Management** showing the full `lab.local` forest with all OUs visible.

![Group Policy Management console](22_group-policy-management-console.png)

Created a new GPO named **`HR_Restrictions`** linked to the HR OU.

![New GPO - HR_Restrictions](23_new-gpo-hr-restrictions.png)

Edited the GPO in the **Group Policy Management Editor**. Navigated to `User Configuration > Policies > Administrative Templates > Control Panel` and selected **"Prohibit access to Control Panel and PC settings"**.

![GPO editor - Control Panel policy](24_gpo-editor-control-panel-policy.png)

Enabled the policy to block all Control Panel and PC Settings access for HR users.

![Policy enabled](25_gpo-policy-enabled.png)

---

### 9. Client Machine Domain Join
On the Windows 11 Pro client, opened **System Properties > Computer Name/Domain Changes** and set membership to domain `lab.local`.

![Domain join - entering lab.local](26_domain-join-lab-local.png)

Authenticated with domain admin credentials to complete the join.

![Domain join - authentication](27_domain-join-authentication.png)

After restart, the full device name resolved to `DESKTOP-15Q3OG7.lab.local` confirming successful domain membership.

![Domain joined - full device name confirmed](28_domain-joined-confirmed.png)

---

### 10. Domain User Login & GPO Verification
Signed into the domain-joined client as `LAB\adoe` (Alice Doe).

![Login screen - LAB\adoe](29_login-lab-adoe.png)

Searched for **Control Panel** on the domain-joined machine.

![Searching for Control Panel](30_searching-control-panel.png)

Attempted to open it — the `HR_Restrictions` GPO blocked access and displayed the restriction error, confirming end-to-end policy enforcement.

![GPO enforcement - restriction error](31_gpo-restriction-error.png)

---

## Skills Demonstrated

- Windows Server 2025 setup and role management
- Active Directory Domain Services configuration from scratch
- DNS server deployment co-located with AD DS
- Static IP assignment and network connectivity verification
- OU design mirroring enterprise department structures
- Security group creation and user membership management
- Domain user provisioning with UPN and legacy logon name formats
- Group Policy Object creation, editing, and OU-level linking
- Windows 11 client domain join and domain authentication
- End-to-end GPO enforcement validation via live user testing

---

## Tools Used

`Windows Server 2025` `Active Directory Users and Computers` `Group Policy Management` `Group Policy Management Editor` `Server Manager` `DNS Manager` `CMD`
