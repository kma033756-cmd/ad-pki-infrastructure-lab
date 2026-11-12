# Active Directory PKI Hierarchy & Advanced Certificate Services Lab (`ETHA.com`)

## 📌 Project Overview
This repository documents the implementation, configuration, and management of a complete two-tier Public Key Infrastructure (PKI) hierarchy in an Active Directory domain (`ETHA.com`). The lab covers Stand-Alone Root CAs, Enterprise Subordinate CAs, custom certificate templates, Group Policy-driven autoenrollment, Online Responder (OCSP) services, Key Recovery Agents (KRA), and private key archival/recovery operations.

## 🏗️ Environment Architecture
* **Root CA:** `STO-CORE.ETHA.com` (`172.16.0.13` - Stand-Alone Root CA)
* **Subordinate / Issuing CA:** `STO-DC1.ETHA.com` (`172.16.0.10` - Enterprise Subordinate CA & DC)
* **Client Workstation:** `STO-CL1` (`172.16.0.11`)
* **Core Services:** Active Directory Certificate Services (AD CS), Internet Information Services (IIS), Group Policy Management, Certification Authority MMC, CertUtil.
* **Domain Functional Level:** Windows Server 2025

---

## ⚙️ Key Practices & Technical Implementation

### Practice 1: Deploying a Stand-Alone Root CA (`STO-CORE`)
* **Task 1:** Installed the **Certification Authority** role service on `STO-CORE` as a Stand-Alone Root CA with a 4096-bit key length and SHA256 hash algorithm.
* **Task 2:** Configured DNS host records and opened firewall rule groups for **File and Printer Sharing** using PowerShell (`netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes`) to support CDP/AIA publishing.

### Practice 2: Deploying an Enterprise Subordinate CA (`STO-DC1`)
* **Task 3:** Installed AD CS and **Certification Authority Web Enrollment** on `STO-DC1` as an Enterprise Subordinate CA, generating the offline certificate request file (`STO-DC1.ETHA.com_ETHA-STO-DC1-CA.req`).
* **Task 4:** Submitted the request to the Root CA (`STO-CORE`), issued the subordinate certificate, imported it into `STO-DC1`, and successfully started the Issuing CA service (`ETHA-IssuingCA`).

### Practice 3: Configuring Certificate Templates
* **Task 5 & 6:** Created custom duplicate templates (`ETHA User` and `ETHA WebServer`) enabling smart card logon, client/server authentication, exportable private keys, and customized security permission ACLs for security groups and users (e.g., Allie Bellew).
* **Task 7 & 8:** Published templates on `ETHA-IssuingCA` and generated/updated web server certificates for IIS on `STO-SVR1`.

### Practice 4: Configuring Certificate Enrollment
* **Task 9 & 10:** Configured **Certificate Services Client - Auto-Enrollment** within Group Policy (`Default Domain Policy`) and verified automatic certificate issuance using `gpupdate /force` and user MMC certificate stores.
* **Task 11:** Configured CA enrollment agent restrictions, restricting designated enrollment agents (`ETHA\Allie`) to specific templates (`ETHA User`) for targeted user deployment.

### Practice 5: Configuring Certificate Revocation
* **Task 12 & 13:** Configured Certificate Revocation List (CRL) and Delta CRL distribution intervals, and installed/configured an **Online Responder (OCSP)** service (`ETHA_CA Online Responder`) for real-time certificate validation checking.

### Practice 6: Configuring Key Recovery & Archival
* **Task 14 & 15:** Configured the **Key Recovery Agent** template, assigned enrollment permissions to Domain Admins, and successfully enrolled for a KRA certificate.
* **Task 16 & 17:** Configured `ETHA-IssuingCA` to allow key recovery and created a custom template (`Archive User`) with **Private Key Archival** enabled.
* **Task 18:** Verified key archival and recovery by generating an encrypted user certificate, deleting it locally (triggering an EFS/data loss warning), retrieving the key blob via `certutil -getkey`, and successfully recovering the private key into a `.pfx` file using `certutil -recoverkey`.
