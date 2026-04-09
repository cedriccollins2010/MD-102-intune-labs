# Compliance Policies — Windows, Android, iOS/iPadOS

> **Domaine MD-102 :** 1. Prepare infrastructure for devices (25–30%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/mem/intune/protect/device-compliance-get-started

---

## Windows Compliance Policy

### Étape 69 — Détail complet de la politique de conformité Windows

**Portail :** Intune → Devices → Windows → Compliance

Configuration complète de la politique :

| Champ | Valeur |
|---|---|
| Name | Compliance for windows devices in CedricTanekeuSomkwe |
| Description | Enforce complexity, length, and expiration password requirements. Firewall: Ensure it's enabled and active |
| Platform | Windows 10 and later |
| Profile type | Windows 10/11 compliance policy |

#### Paramètres configurés

| Catégorie | Paramètre | Valeur |
|---|---|---|
| **Device Properties** | Minimum OS version | 10.0.1 |
| **System Security** | Firewall | Required ✅ |
| **System Security** | Antivirus | Required ✅ |
| **Device Security** | Require password to unlock | Required ✅ |

#### Actions en cas de non-conformité

| Action | Délai |
|---|---|
| Mark device noncompliant | **Immediately** |
| Send email to end user | **3 Days** |

#### Assignments

| Champ | Valeur |
|---|---|
| Assigned to | All Devices + All Users |
| Filter | None |

> 💡 S'applique à **tout le tenant** sans exception.

---

### Étape 70 — Inventaire Windows : 1 appareil Compliant

**Portail :** Intune → Devices → Windows → Windows devices

| Champ | Valeur |
|---|---|
| Device name | DESKTOP-769... |
| Managed by | Intune |
| Ownership | Corporate |
| Compliance | ✅ **Compliant** |
| OS | Windows |
| OS version | 10.0.19045.6456 |
| Primary user | Test-Autopilo... |

Actions disponibles depuis cette vue :
`Restart` / `Sync` / `Wipe` / `Retire` / `Remote lock`

---

## Android Compliance Policy

### Étape 71 — Créer une politique de conformité Android Enterprise

**Portail :** Intune → Devices → Android → Compliance → Create policy

| Plateforme | Description | Statut |
|---|---|---|
| Android device administrator | Mode legacy | ⚠ Déconseillé |
| Android (AOSP) | Pour appareils sans Google Services | Cas spécifiques |
| **Android Enterprise** | Work Profile / Fully Managed | ✅ Sélectionné |

---

### Étape 72 — Devices Overview : inventaire multi-plateforme

**Portail :** Intune → Devices → Overview

| Plateforme | Appareils enrôlés |
|---|---|
| Windows | 1 ✅ |
| iOS/iPadOS | 0 |
| macOS | 0 |
| **Android** | 0 *(cible de la prochaine étape)* |
| Linux | 0 |

---

### Étape 73 — Paramètres de sécurité Android Enterprise

**Portail :** Android | Compliance → Fully managed, dedicated, and corporate-owned work profile

| Catégorie | Paramètre | Valeur |
|---|---|---|
| **Google Play Protect** | Play Integrity Verdict | Check basic integrity ✅ |
| **Device Properties** | Maximum OS version | 10.0 |
| **System Security** | Require password to unlock | Require ✅ |
| **System Security** | Required password type | Numeric |
| **System Security** | Minimum password length | 4 |
| **Encryption** | Require encryption of data storage | Require ✅ |
| **Intune app runtime integrity** | | Not configured |

---

### Étape 74 — Actions en cas de non-conformité Android

**Portail :** Fully managed → Étape 3/5 → Actions for noncompliance

| Action | Délai |
|---|---|
| Mark device noncompliant | **Immediately** |
| Send push notification to end user | **2 days** |
| Remotely lock the noncompliant device | **Immediately** |
| Add device to retire list | **Immediately** |

> 💡 **Point clé MD-102 :** Ces actions sont plus agressives que Windows
> (lock + retire immédiats) car les appareils mobiles représentent un
> risque plus élevé de perte/vol.

---

### Étape 75 — Résumé final de la politique Android Enterprise

**Portail :** Fully managed → Review + create

| Champ | Valeur |
|---|---|
| Name | Android – Compliance – Standard Security |
| Description | Policy de conformité Android : PIN, chiffrement, OS, root |
| Platform | Android Enterprise |
| Profile type | Fully managed, dedicated, and corporate-owned work profile |
| Play Integrity Verdict | Check basic integrity |
| Max OS | 10.0 |
| Password | Require / Length min : 4 |
| Encryption | Require |
| Actions | Mark noncompliant immediately + Push 2 days + Remote lock immediately + Retire immediately |
| Assignments | No results *(aucun groupe assigné encore)* |

---

### Étape 76 — Politique Android créée : monitoring (0 appareils)

**Portail :** Android – Compliance – Standard Security → Monitor tab

| Statut | Nombre |
|---|---|
| Compliant | 0 |
| Noncompliant | 0 |
| Others | 0 |
| Total | 0 |

> Comportement attendu : les compteurs évolueront au fur et à mesure
> que des appareils Android seront enrôlés.

---

## Paramètres recommandés Android Enterprise (référence complète)

| Catégorie | Paramètre | Valeur | Pourquoi |
|---|---|---|---|
| **Password** | Require password | Yes | Base de la sécurité mobile |
| **Password** | Password type | Numeric | Équilibre sécurité / UX |
| **Password** | Min length | 6 digits | Évite les codes trop simples |
| **Password** | Max inactivity | 5 minutes | Verrouillage automatique |
| **Encryption** | Data storage encryption | Required | Protège les données si perte/vol |
| **Integrity** | Rooted/Jailbroken | Blocked | Bloque les OS compromis |
| **Integrity** | Device Integrity | Required | Vérifie le hardware et le boot |
| **Dev Tools** | Developer options | Blocked | Empêche le sideloading d'APK |
| **Dev Tools** | USB debugging | Blocked | Empêche l'extraction de données par câble |
| **OS Version** | Minimum OS version | 11.0 | Patches de sécurité modernes |
| **Play Protect** | Google Play Protect | Required | Scan antimalware temps réel |

---

## iOS/iPadOS Compliance Policy

### Étape 77 — Créer une politique de conformité iOS/iPadOS

**Portail :** Intune → Devices → iOS/iPadOS → Compliance → Create policy

| Champ | Valeur |
|---|---|
| Platform | iOS/iPadOS *(verrouillé automatiquement)* |
| Profile type | iOS compliance policy *(unique type disponible)* |

Paramètres typiques à configurer :

| Paramètre | Valeur recommandée |
|---|---|
| Minimum iOS version | 16.0 |
| Jailbroken devices | Block |
| Require password | Required |
| Minimum password length | 6 |
| Encryption | Required |

> 💡 La politique iOS/iPadOS est plus simple que Android :
> une seule plateforme, un seul profil type.

---

## Comparaison des actions de non-conformité par plateforme

| Action | Windows | Android | iOS |
|---|---|---|---|
| Mark noncompliant | Immediately | Immediately | Immediately |
| Send email | 3 days | ❌ | 3 days |
| Send push notification | ❌ | 2 days | 2 days |
| Remote lock | ❌ | Immediately | Immediately |
| Add to retire list | ❌ | Immediately | Configurable |

> 💡 **Point clé MD-102 :** Android et iOS ont des actions plus
> agressives que Windows — risque de perte/vol plus élevé sur mobile.

---

## ⚡ Résumé — Points clés exam

| Concept | À retenir |
|---|---|
| Mark noncompliant immediately | Action par défaut sur toutes les plateformes |
| Send email 3 days | Windows — notifier l'utilisateur après 3 jours |
| Remote lock immediately | Android/iOS — verrouillage immédiat si non-conforme |
| Add to retire list | Android — mise en liste de retrait immédiate |
| Play Integrity | Vérifie l'intégrité du boot Android |
| Rooted/Jailbroken | Toujours bloquer — OS compromis |
| Android Enterprise | Mode recommandé — éviter Android device administrator (legacy) |
| All Devices + All Users | Assignment le plus large — s'applique à tout le tenant |
| Compliant device | Prérequis pour Conditional Access — Require compliant device |
| Per-setting status | Vue détaillée du statut par paramètre dans le Monitor tab |