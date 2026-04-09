# 1.4.3 — Conditional Access Policies

> **Domaine MD-102 :** 1. Prepare infrastructure for devices (25–30%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/entra/identity/conditional-access/

---

## Étape 78 — Entra ID : Permissions Management (retrait nov. 2025)

**Portail :** Microsoft Entra admin center → Permissions Management

> ⚠ **Notice :** Effective November 1, 2025, Microsoft Entra Permissions
> Management will be retired and support will be discontinued.
> Les clients existants gardent l'accès jusqu'au 31 octobre 2025.

| Concept | Détail |
|---|---|
| Permissions Management | CIEM (Cloud Infrastructure Entitlement Management) |
| Surveille | Permissions excessives sur Azure / AWS / GCP |
| Impact sur Intune | **Aucun** — son retrait n'affecte pas Intune |

---

## Étape 79 — Nouvelle politique CA : appareils conformes requis

**Portail :** Entra ID → Conditional Access → New policy

Configuration de la politique :

| Champ | Valeur |
|---|---|
| Name | CA – Require compliant device |
| Users | Specific users included |
| Utilisateurs ciblés | Analyste / Ange Ruth / Controleur-gestion |
| Target resources | *(pas encore configuré)* |
| Enable policy | **Report-only** *(mode audit)* |

> 💡 **Report-only = tester sans risque.**
> Une fois validé, passer en **On** pour bloquer les appareils non-conformes.

> ⚠ **Prérequis :** Désactiver les Security Defaults avant d'activer
> une politique CA.

---

## Étape 80 — Edit filter (attributs de sécurité personnalisés)

**Portail :** New policy → Edit filter

Permet de cibler la politique CA sur des appareils avec des attributs
Entra spécifiques (ex: département, rôle).

> ⚠ Requires **Attribute Definition Reader** ou
> **Attribute Assignment Reader** dans Entra ID.

---

## Étapes 81-82 — Target resources : Exclude tab

**Portail :** CA → New policy → Target resources → onglet Exclude

| Champ | Valeur |
|---|---|
| Ressource exclue | **Office 365** ✅ |
| Raison | Éviter de bloquer mails/Teams pendant la phase Report-only |

> 💡 En production, on cible souvent **All cloud apps** ou des apps
> spécifiques selon le besoin.

---

## Étape 83 — Catalogue des apps disponibles pour CA

**Portail :** Resources → Chooseable Applications

102 résultats disponibles. Exemples :

`Microsoft Admin Portals` / `Office 365` / `AAD App Management` /
`AADReporting` / `Azure AD Application Proxy` /
`Azure AD Identity Governance` / `Azure ESTS Service` /
`Azure MFA StrongAuthenticationService` /
`Azure Multi-Factor Auth Client` / `Azure Virtual Desktop`

Onglets disponibles : `All` / `Enterprise applications` / `Agent blueprints`

---

## Étape 84 — CA Report-only : avertissement plateformes mobiles

**Portail :** Enable policy → Report-only

> ⚠ Policies requiring compliant devices may prompt users on
> **macOS, iOS, Android, and Linux** to select a device certificate.

Option choisie dans le lab :
✅ **Exclude device platforms macOS, iOS, Android, and Linux**
from this policy

> 💡 Exclure les mobiles évite les faux positifs pendant les tests.

---

## Étape 85 — Grant controls (Require compliant device)

**Portail :** CA → Access controls → Grant panel

| Contrôle | Valeur |
|---|---|
| Grant access | ✅ Sélectionné |
| Require authentication strength | ✅ Coché |
| **Require device to be marked as compliant** | ✅ *(contrôle central)* |
| Require app protection policy | ✅ *(pour MAM BYOD)* |
| For multiple controls | **Require all** |

> 💡 **Point clé MD-102 :** Require device compliance = seuls les
> appareils Intune **Compliant** pourront accéder aux ressources.

---

## Modes d'une Conditional Access policy

| Mode | Icône | Comportement |
|---|---|---|
| **Report-only** | 🔵 | Ne bloque rien — simule l'impact et écrit des logs |
| **On** | 🟢 | Active et bloquante |
| **Off** | ⚫ | Désactivée |

---

## Étape 86 — Enable policy → On : erreur Security Defaults

**Portail :** Enable policy → On

Tentative d'activation complète :

> ⚠ Warning : You must first disable security defaults before
> enabling a CA policy.

> ❌ Erreur : Security defaults must be disabled to enable
> Conditional Access policy.

**Solution :**
Entra ID → Properties → Manage security defaults → **Disabled**

---

## Security Defaults vs Conditional Access

| Concept | Security Defaults | Conditional Access |
|---|---|---|
| Configuration | Automatique, pas de personnalisation | Entièrement personnalisable |
| Coexistence | ❌ Mutuellement exclusifs | ❌ Mutuellement exclusifs |
| MFA | Forcé pour tous | Configurable par groupe/app |
| Recommandé pour | Petites orgs sans licence P1/P2 | Entreprises avec Entra ID P1/P2 |

> ⚠ **Point clé exam :** Security Defaults et Conditional Access
> personnalisé sont **mutuellement exclusifs** — désactiver l'un
> avant d'activer l'autre.

---

## Workflow recommandé pour déployer une CA policy