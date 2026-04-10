# 1.3 — Enrollment des appareils dans Microsoft Intune

> **Domaine MD-102 :** 1. Prepare infrastructure for devices (25–30%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/mem/intune/enrollment/

---

## 1.3.1 — Configurer l'enrollment Windows

### Étape 59 — Page Enrollment Windows : Automatic Enrollment

**Portail :** Intune admin center → Devices → Device onboarding → Enrollment → onglet Windows

Options disponibles sur cette page :

| Option | Description |
|---|---|
| **Automatic Enrollment** ⭐ | Configure Windows devices to enroll when they join or register with Entra ID |
| CNAME Validation | Vérifie la configuration DNS pour l'enrollment |
| Co-management Settings | Configuration de la coexistence SCCM + Intune |
| Device platform restriction | Limiter les types d'appareils autorisés |
| Device limit restriction | Limiter le nombre d'appareils par utilisateur |
| Enrollment notifications | Notifications envoyées lors de l'enrôlement |
| Windows Hello for Business | Configuration WHfB au niveau du tenant |
| Windows Backup and Restore | Sauvegarde Windows via Intune |

> 💡 **Automatic Enrollment** est le point d'entrée principal pour
> configurer l'auto-enrollment MDM des appareils Windows.

---

### Étape 60 — Configuration MDM user scope : Some + WIP

**Portail :** Intune → Devices | Enrollment → Microsoft Intune (Automatic Enrollment settings)

#### MDM User Scope

| Valeur | Comportement |
|---|---|
| **None** | Aucun auto-enrollment |
| **Some** ✅ *(configuré dans le lab)* | Seuls les utilisateurs des groupes spécifiés bénéficient de l'auto-enrollment |
| **All** | Tous les utilisateurs du tenant |

Configuration du lab :
- MDM user scope : **Some**
- Groups : **No groups selected** *(à configurer avec le groupe pilote)*

#### MDM URLs (auto-renseignées par Intune)

| URL | Rôle |
|---|---|
| Terms of use | `https://portal.manage.microsoft.com/TermsofUse.aspx` |
| Discovery | `https://enrollment.manage.microsoft.com/enrollmentserver/discovery.svc` |
| Compliance | `https://portal.manage.microsoft.com/?portalAction=Compliance` |

> ⚠ Ces URLs sont générées automatiquement — ne pas les modifier manuellement.

#### WIP User Scope (Windows Information Protection)

| Champ | Valeur |
|---|---|
| WIP user scope | Some |
| Groups | No groups selected |

> ⚠ **Note importante :** Creating new WIP without enrollment policies
> (WIP-WE) is **no longer supported**.

---

## Bonne pratique — Déploiement progressif

> 💡 **Point clé MD-102 :** MDM scope = **Some** + groupe ciblé est la
> configuration recommandée pour un déploiement progressif.

| Phase | MDM Scope | Groupe cible |
|---|---|---|
| **Pilote** | Some | Groupe IT / testeurs |
| **Déploiement progressif** | Some | Groupes par département |
| **Généralisation** | All | Tout le tenant |

---

## 1.3.2 — Enrollment profiles pour appareils Android

| Mode | Description | Cas d'usage |
|---|---|---|
| **Fully Managed** | Appareil 100% géré par l'entreprise | Appareils corporate, usage unique |
| **Dedicated** | Appareil dédié à une tâche (kiosk) | Tablettes partagées, bornes |
| **Corporate-owned with Work Profile** | Profil pro séparé sur appareil corporate | Employés avec appareil entreprise |
| **Work Profile (BYOD)** | Conteneur pro sur appareil personnel | BYOD — séparation pro/perso |

> 💡 **Point clé MD-102 :** Choisir le bon mode Android selon le scénario :
> - "appareil personnel de l'employé" → **Work Profile (BYOD)**
> - "appareil de l'entreprise, usage unique" → **Dedicated**
> - "appareil de l'entreprise, usage général" → **Fully Managed ou COPE**

---

## Bulk Enrollment

### iOS et Android

| Méthode | Plateforme | Description |
|---|---|---|
| **ADE (Automated Device Enrollment)** | iOS/iPadOS | Via Apple Business Manager — enrollment automatique sans intervention utilisateur |
| **ABM (Apple Business Manager)** | iOS/iPadOS | Portail Apple pour gérer les appareils en volume |
| **Android Zero-touch** | Android Enterprise | Enrollment automatique pour appareils Android corporate |
| **QR Code / NFC** | Android Enterprise | Enrollment en masse pour Fully Managed et Dedicated |

---

## ⚡ Résumé — Points clés exam

| Concept | À retenir |
|---|---|
| MDM scope = Some | Déploiement progressif recommandé — pilote avant généralisation |
| MDM scope = All | Enrollment pour tous les utilisateurs du tenant |
| MDM URLs | Auto-générées par Intune — ne pas modifier |
| WIP-WE | Ne plus créer de nouvelles politiques WIP sans enrollment |
| Automatic Enrollment | Déclenché quand l'appareil rejoint ou s'enregistre dans Entra ID |
| Work Profile | BYOD Android — séparation pro/perso |
| Fully Managed | Corporate Android — contrôle total |
| Dedicated | Kiosk Android — usage unique |
| ADE/ABM | Bulk enrollment iOS via Apple Business Manager |
| CNAME Validation | Vérification DNS pour l'enrollment — pas lié au MDM scope |