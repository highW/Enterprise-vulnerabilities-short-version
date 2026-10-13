# Enterprise Network Attack Surface — Reference Notes

> **Purpose:** Educational reference, modeled on public frameworks like
> MITRE ATT&CK, PTES, and the OWASP Testing Guide. This document describes
> *categories* of attack technique and the matching defenses. It is not a
> walkthrough, exploit code, or an operational playbook, and it is not
> written against any specific organization's infrastructure.
>
> **Scope:** Covers physical, wireless, insider, and host-based attack
> surfaces typically found in a wiring-closet-to-core enterprise network
> (IDF/MDF closets, access switches, core/distribution, workstations).
>
> **Use responsibly:** This material is for security awareness, training,
> and authorized testing only (CTFs, labs you own, or engagements with
> signed authorization). Using any of this against a system you don't own
> or don't have written permission to test is illegal in most
> jurisdictions (e.g. the US Computer Fraud and Abuse Act).

---

## 1. Physical access

| Technique | How it generally works | Primary mitigation |
|---|---|---|
| Tailgating | Following an authorized person through a badge-controlled door | Anti-tailgating policy, mantraps, staff culture of challenging strangers |
| Badge cloning (125kHz prox) | Legacy low-frequency RFID badges broadcast an ID in the clear; can be read and copied from a few inches away | Migrate to encrypted/mutual-auth badge systems; add a second factor (PIN, biometric) |
| Badge relay attack | Two devices bridge a real badge and a real reader in real time, even over an encrypted protocol | Distance-bounding protocols, badge system firmware updates |
| Lock bypass | Bump keys, shims, unlocked doors | Rack-level locks in addition to room locks, regular physical audits |
| Rogue device on an open switch port | Any unused, unsecured port lets a device join the internal VLAN | Port security (MAC limiting), 802.1X, disable unused ports |
| Console/serial access | Unsecured console ports on switches or console servers allow direct admin access | Require auth on every console port, log and alert on console sessions |

## 2. Wireless / RF

| Technique | How it generally works | Primary mitigation |
|---|---|---|
| Passive recon | Monitor-mode capture of SSIDs, BSSIDs, signal strength, vendor OUIs | Minimize unnecessary broadcast, separate guest/IoT SSIDs |
| Deauthentication | Forged 802.11 management frames disconnect clients (unauthenticated by design in WPA2) | WPA3 (protected management frames), wireless IDS alerting on deauth floods |
| Handshake capture + offline cracking | A captured WPA2 handshake can be cracked offline if the passphrase is weak | Strong passphrases, 802.1X enterprise auth instead of PSK, WPA3-SAE |
| Evil twin / rogue AP | A fake AP with a stronger signal and the same SSID lures clients, often paired with a credential-harvesting portal | Certificate-based client auth, user training, wireless IDS |
| RF jamming | Flooding a frequency with noise to deny service | Spectrum monitoring; note this is separately regulated (e.g. FCC) regardless of intent |
| IoT/Bluetooth exposure | Many IoT and badge-reader devices use proprietary RF with weak or no encryption | Network-segment IoT away from core infra, vet device security before deployment |

## 3. Insider / human factor

| Technique | How it generally works | Primary mitigation |
|---|---|---|
| Social engineering (pretexting, phishing, vishing) | Manipulating a person into granting access or information rather than attacking a system | Security awareness training, verify-before-trust culture, phishing simulations |
| Credential sharing / unattended sessions | A logged-in console or KVM session is usable by anyone nearby | Idle timeouts, screen locks, no shared accounts |
| Compromised or malicious insider | Legitimate credentials/access bypass perimeter and physical controls entirely | Least privilege, two-person rule for sensitive changes, full audit logging, anomaly detection, prompt offboarding |

## 4. Host-based (Windows local privilege escalation)

General categories, all well documented in public OSCP/PEWK-style material:

- **Misconfigurations:** unquoted service paths, weak service ACLs, `AlwaysInstallElevated`, writable scheduled tasks running as SYSTEM.
- **Credential exposure:** secrets in config files/scripts, legacy Group Policy Preferences passwords, LSASS memory dumping, plaintext autologon.
- **Token/kernel abuse:** impersonation of privileged service tokens, unpatched kernel vulnerabilities.

**Mitigations:** timely patching, least privilege on service accounts, Credential Guard / LSASS protection, EDR, regular permission audits on services and scheduled tasks.

## 5. Suggested defensive baseline

- Network segmentation: management, staff, guest, IoT, and customer traffic on separate VLANs with no implicit trust between them.
- Centralized logging for badge access, console sessions, and switch config changes, correlated in a SIEM/NMS.
- Out-of-band management (serial console server, dedicated management VLAN) secured as strictly as production.
- Least privilege across the board — human and service accounts alike.
- Regular physical audits paired with regular technical audits; most real incidents combine a human gap with a technical one.

---

## References (public, non-operational)

- MITRE ATT&CK — https://attack.mitre.org
- PTES (Penetration Testing Execution Standard) — http://www.pentest-standard.org
- OWASP Testing Guide — https://owasp.org/www-project-web-security-testing-guide/
- NIST SP 800-53 / insider threat guidance — https://www.nist.gov
- Hak5 documentation (LAN Turtle, Rubber Ducky) — https://hak5.org

---

*Written as general security-awareness material. No specific organization,*
*facility, or individual is referenced or targeted.*
