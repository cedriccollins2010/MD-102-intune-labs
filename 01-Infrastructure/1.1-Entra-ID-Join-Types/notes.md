# 1.1 — Entra ID Join Types & Device Groups

> **Domaine MD-102 :** 1. Prepare infrastructure for devices (25–30%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/entra/identity/devices/

---

## 1.1.1 — Les 3 types de jointure Entra ID

| Type | Description | Cas d'usage |
|---|---|---|
| **Entra Joined** | Appareil cloud-only, pas d'AD on-prem | Poste de travail moderne, full cloud |
| **Hybrid Entra Joined** | Joint à l'AD on-prem ET à Entra ID | Migration progressive, coexistence AD |
| **Entra Registered** | BYOD — appareil personnel, accès sans gestion complète | Téléphone perso, tablette personnelle |

> ⚠ **Point clé MD-102 :** Choisir le bon type selon le scénario :
> - "cloud-only" → **Entra Joined**
> - "on-prem AD existant" → **Hybrid Entra Joined**
> - "appareil personnel de l'employé" → **Entra Registered**

---

## 1.1.2 — Vérifier le statut de jointure

```powershell
dsregcmd /status
```

Champs clés dans la sortie :

| Champ | Valeur attendue | Signification |
|---|---|---|
| `AzureAdJoined` | YES | Appareil Entra Joined ✅ |
| `DomainJoined` | YES | Appareil joint à l'AD on-prem |
| `WorkplaceJoined` | YES | Appareil Entra Registered (BYOD) |

---

## 1.2 — Groupes d'appareils dans Microsoft Entra ID

### Étape 52 — Vue d'ensemble des groupes (Groups Overview)

**Portail :** Microsoft Entra ID → Groups → Overview

État du tenant de lab :

| Métrique | Valeur |
|---|---|
| Total groups | 8 |
| Dynamic groups | 1 |
| M365 groups | 3 |
| Cloud groups | 8 |
| Security groups | 5 |
| On-premises groups | 0 |

> 💡 **On-premises groups = 0** confirme l'architecture **100% cloud**
> Aucun AD on-prem synchronisé dans ce tenant.

---

### Étape 53 — Créer un groupe de sécurité Assigned

**Portail :** Entra ID → Groups → New Group

| Champ | Valeur configurée |
|---|---|
| Group type | Security |
| Group name | intune-Test-Lab |
| Group description | Groupe de tests Intune |
| Entra roles assignable | **No** |
| Membership type | **Assigned** |

> Ce groupe sert de cible pour les politiques Intune en mode test.

---

### Étape 54 — Les 3 types de Membership

**Portail :** New Group → Membership type (dropdown)

| Type | Comportement | Idéal pour |
|---|---|---|
| **Assigned** | Membres ajoutés manuellement | Labs, pilotes, dépannage |
| **Dynamic User** | Règle automatique sur attributs utilisateur | Ex : `user.department -eq "IT"` |
| **Dynamic Device** | Règle automatique sur attributs appareil | Ex : `device.deviceOSType -eq "Windows"` |

> 💡 **Point clé MD-102 :** Les Dynamic Groups éliminent la gestion
> manuelle — dès qu'un utilisateur ou appareil correspond à la règle,
> il rejoint automatiquement le groupe.

---

### Étape 55 — Ajouter des Owners au groupe

**Portail :** New Group → Add owners

Utilisateurs disponibles dans le tenant (12 comptes) :

`Admin.it` / `Analyste` / `Ange Ruth` / `Cedric Somkwe` ✅ /
`Cedric Tanekeu` / `Cedric Tanekeu Somkwe` ✅ / `Controleur-gestion` /
`Directeur-financier` / `Support Technique` /
`Support-Utilisateur-Autopilot` / `Test-Autopilot2` /
`Utilisateur Test Intune`

> **Owners sélectionnés :** Cedric Somkwe + Cedric Tanekeu Somkwe
> Les owners gèrent les membres **sans avoir besoin d'être Global Admin**.

---

### Étape 56 — Groupe Dynamic User : Add dynamic query

**Portail :** New Group → Membership type : Dynamic User

Quand Dynamic User est sélectionné :
- Le champ **Members** disparaît
- Remplacé par **Dynamic user members** + lien **Add dynamic query**
- Ce lien ouvre le **Rule Builder**

---

### Étape 57 — Rule Builder : interface visuelle

**Portail :** Dynamic membership rules → Configure Rules

| Colonne | Rôle |
|---|---|
| And / Or | Connecteur logique entre les conditions |
| Property | Attribut Entra (ex: `displayName`, `department`) |
| Operator | `contains`, `eq`, `startsWith`... |
| Value | Valeur cible |

Boutons importants :

| Bouton | Action |
|---|---|
| **+ Add expression** | Ajoute une ligne de condition |
| **Edit** | Bascule en mode syntaxe directe |
| **Validate Rules** | Teste la règle sur les utilisateurs existants |

> ⚠ **MemberOf** est en preview — ne pas utiliser en production.

---

### Étape 58 — Syntaxe de règle dynamique

**Portail :** Rule Builder → Edit rule syntax

Règle configurée dans le lab :