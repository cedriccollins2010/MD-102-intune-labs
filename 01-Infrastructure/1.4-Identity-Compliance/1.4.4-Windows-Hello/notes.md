# 1.4.4 — Windows Hello for Business (WHfB)

> **Domaine MD-102 :** 1. Prepare infrastructure for devices (25–30%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/

---

## Étape 87 — Options de configuration WHfB

**Source :** Documentation Microsoft — Configuration options for Windows Hello for Business

### Deux méthodes de configuration

| Méthode | Protocole | Quand l'utiliser |
|---|---|---|
| **CSP (Configuration Service Provider)** | MDM (Intune) | Appareils **Entra Joined** gérés par Intune |
| **Group Policy (GPO)** | AD on-prem | Appareils **AD domain joined** ou **Hybrid Entra Joined** non gérés par MDM |

> 💡 **Point clé MD-102 :**
> - Entra ID join + Intune → **CSP (PassportForWork CSP)**
> - AD domain join → **GPO**

---

## Étape 88 — Configurer WHfB via Intune

**Source :** Documentation Microsoft — Configure Windows Hello for Business using Intune

### Deux approches disponibles

| Approche | Portée | Comportement |
|---|---|---|
| **Tenant-level policy** | Tous les appareils | Appliquée à l'enrôlement uniquement — souvent désactivée au profit d'une politique ciblée |
| **Device configuration policy** | Groupe spécifique | Mise à jour lors des policy refresh intervals — approche recommandée |

### Types de politiques pour WHfB (Device configuration)

| Type | Description |
|---|---|
| Settings catalog | Configuration granulaire via catalogue de paramètres |
| Security baselines | Baseline Microsoft avec paramètres WHfB préconfigurés |
| Custom policy | PassportForWork CSP — configuration manuelle en OMA-URI |
| Account protection policy | ⭐ Recommandée — politique ciblée sur un groupe spécifique |
| Identity protection policy template | Template dédié WHfB |

> 💡 **Recommandation Microsoft :** Utiliser une **Account protection
> policy** ciblée sur un groupe spécifique plutôt que la tenant-level policy.

---

## Étape 89 — Windows Enrollment : Windows Hello for Business

**Portail :** Intune → Devices → Windows | Enrollment → Windows Hello for Business

**Navigation :**
`Devices` → `Windows` → `Enrollment` → `Windows Hello for Business` ⭐

Options disponibles sur la page Enrollment :

| Option | Description |
|---|---|
| Automatic Enrollment | Auto-enrollment MDM |
| CNAME Validation | Vérification DNS |
| Co-management Settings | Coexistence SCCM + Intune |
| Device platform restriction | Limiter les types d'appareils |
| Device limit restriction | Limiter le nombre d'appareils |
| Enrollment notifications | Notifications lors de l'enrôlement |
| **Windows Hello for Business** ⭐ | Configuration WHfB au niveau tenant |
| Windows Backup and Restore | Sauvegarde Windows |

---

## PassportForWork CSP — Paramètres clés

| Paramètre OMA-URI | Description | Valeur recommandée |
|---|---|---|
| `./Device/Vendor/MSFT/PassportForWork/{TenantId}/Policies/UsePassportForWork` | Activer WHfB | True |
| `./Device/Vendor/MSFT/PassportForWork/{TenantId}/Policies/RequireSecurityDevice` | Exiger TPM | True |
| `./Device/Vendor/MSFT/PassportForWork/{TenantId}/Policies/PINComplexity/MinimumPINLength` | Longueur PIN minimum | 6 |
| `./Device/Vendor/MSFT/PassportForWork/{TenantId}/Policies/PINComplexity/MaximumPINLength` | Longueur PIN maximum | 127 |
| `./Device/Vendor/MSFT/PassportForWork/{TenantId}/Policies/PINComplexity/UppercaseLetters` | Majuscules dans le PIN | 1 (Requis) |

---

## Cloud Kerberos Trust vs Key Trust vs Certificate Trust

| Mode | Description | Prérequis |
|---|---|---|
| **Cloud Kerberos Trust** ⭐ | Authentification Kerberos via Entra ID — le plus simple | Entra ID + DC Windows Server 2016+ |
| **Key Trust** | Clé cryptographique stockée dans Entra ID | AD on-prem + ADFS ou Entra Connect |
| **Certificate Trust** | Certificat PKI pour l'authentification | PKI on-prem + ADFS |

> 💡 **Point clé MD-102 :** Pour un environnement **cloud-only** ou
> **Hybrid**, **Cloud Kerberos Trust** est le modèle recommandé —
> plus simple à déployer que Key Trust ou Certificate Trust.

---

## Méthodes d'authentification WHfB

| Méthode | Description |
|---|---|
| **PIN** | Code PIN lié à l'appareil — pas transmis sur le réseau |
| **Biométrie (visage)** | Windows Hello Face — via caméra IR |
| **Biométrie (empreinte)** | Windows Hello Fingerprint — via lecteur d'empreintes |
| **Clé de sécurité FIDO2** | Clé physique (ex: YubiKey) |

> ⚠ **PIN WHfB ≠ mot de passe :**
> Le PIN est lié à l'appareil et ne peut pas être utilisé sur un autre appareil.
> Il est protégé par le TPM (Trusted Platform Module).

---

## Provisioning packages (alternative à MDM)

**Outil :** Windows Configuration Designer (téléchargé dans le lab ✅)

Cas d'usage :
- Appareils sans connectivité internet lors de l'enrôlement
- Déploiement en masse sans Autopilot
- Environnements isolés (air-gapped)

---

## ⚡ Résumé — Points clés exam

| Concept | À retenir |
|---|---|
| CSP | Méthode MDM pour configurer WHfB via Intune |
| GPO | Méthode pour AD domain joined non gérés par MDM |
| PassportForWork CSP | CSP utilisé pour configurer WHfB |
| Account protection policy | Approche recommandée — ciblée sur un groupe |
| Tenant-level policy | S'applique à l'enrollment — souvent désactivée |
| Cloud Kerberos Trust | Mode recommandé — plus simple que Key/Certificate Trust |
| PIN WHfB | Lié à l'appareil — protégé par TPM — ≠ mot de passe réseau |
| TPM | Requis pour stocker les clés WHfB de manière sécurisée |
| Biométrie | Face + Fingerprint — méthodes d'auth alternatives au PIN |
| FIDO2 | Clé de sécurité physique compatible WHfB |
| Windows Configuration Designer | Outil pour créer des provisioning packages |