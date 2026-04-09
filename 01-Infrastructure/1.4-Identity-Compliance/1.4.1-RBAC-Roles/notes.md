# 1.4.1 — Gestion des rôles dans Intune (RBAC)

> **Domaine MD-102 :** 1. Prepare infrastructure for devices (25–30%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/mem/intune/fundamentals/role-based-access-control

---

## Étape 62 — Tenant Admin : vue d'ensemble

**Portail :** Intune admin center → Tenant admin → Tenant status

État du tenant de lab :

| Métrique | Valeur |
|---|---|
| Tenant name | CedricTanekeuSomkwe.onmicrosoft.com |
| Location | North America |
| MDM authority | Microsoft Intune ✅ |
| Account status | Active |
| Total licensed users | 7 |
| Total enrolled devices | 1 ✅ |

Outils admin disponibles :

| Outil | Rôle |
|---|---|
| Remote Help | Assistance à distance sur les appareils |
| Microsoft Tunnel Gateway | VPN pour appareils mobiles |
| Cloud PKI | Gestion des certificats |
| Connectors and tokens | Connexions tierces (Apple, Android, etc.) |
| Assignment filters | Ciblage granulaire des politiques |
| **Roles** ⭐ | Point d'entrée RBAC Intune |
| Microsoft Entra PIM | Élévation Just-In-Time des droits |
| Audit logs | Traçabilité des actions admin |

**Navigation :** Tenant administration → Roles

---

## Étape 63 — Privileged Identity Management (PIM)

**Portail :** Microsoft Entra ID → Identity Governance → Privileged Identity Management

### Deux méthodes d'élévation JIT (Just-In-Time)

| Méthode | Description | Délai d'élévation |
|---|---|---|
| **Méthode 1** | PIM pour le rôle built-in **Intune Administrator** → assigné à un compte admin | ~10 secondes |
| **Méthode 2** | PIM for Groups avec assignation de rôle Intune RBAC personnalisé | Jusqu'à 15 minutes |

> 💡 **Point clé MD-102 :** PIM évite d'avoir des comptes admin avec
> des droits permanents — principe du **moindre privilège** et accès
> à la demande seulement.

---

## Étape 64 — Créer un rôle personnalisé : Managed Apps (Read only)

**Portail :** Intune → Tenant admin → Roles → Add Custom Role → Managed apps

| Permission | Valeur |
|---|---|
| Create | No |
| Update | No |
| Delete | No |
| Assign | No |
| Wipe | No |
| **Read** | ✅ **Yes** |

> Ce pattern **Read = Yes + tout le reste = No** est le modèle
> d'un rôle Support Technique.

---

## Étape 65 — Rôles Intune built-in (All roles)

**Portail :** Intune → Tenant admin → Roles → All roles

11 rôles disponibles :

| Rôle | Permissions principales |
|---|---|
| **Policy and Profile Manager** | Politiques de conformité, profils de configuration, enrollment Apple/Android |
| **School Administrator** | Apps et paramètres pour ses groupes + actions à distance (lock, restart, retire) |
| **Endpoint Privilege Reader** | Lecture seule des politiques EPM |
| **Help Desk Operator** | Tâches à distance sur appareils/utilisateurs + assignation apps/politiques |
| **Application Manager** | Gère les apps mobiles, lit les infos d'appareils et profils de configuration |
| **Endpoint Security Manager** | Sécurité, baselines de conformité, Conditional Access |

Navigation disponible :
`All roles` / `Scope tags` / `Administrator Licensing` /
`My permissions` / `Roles by permission` / `Admin permissions`

---

## Étape 66 — Créer un rôle personnalisé (Create dropdown)

**Portail :** Intune roles → Create (dropdown)

| Type de rôle | Usage |
|---|---|
| **Intune role** ✅ | Rôle personnalisé pour la gestion des appareils Intune |
| Windows 365 role | Rôle pour les Cloud PCs |
| Windows Autopatch role | Rôle pour la gestion des mises à jour automatiques |

---

## Étape 67 — Custom Role : Device configurations (Read only)

**Portail :** Add Custom Role → Device configurations

| Permission | Valeur |
|---|---|
| Assign | No |
| Create | No |
| Delete | No |
| View Reports | No |
| Update | No |
| Update Windows Backup and Restore | No |
| **Read** | ✅ **Yes** |

> Le support technique peut **voir** les profils de configuration
> (Wi-Fi, VPN, restrictions) pour diagnostiquer mais ne peut
> **PAS les modifier**.

---

## Étape 68 — Custom Role : Endpoint Detection and Response

**Portail :** Add Custom Role → Endpoint Detection and Response

| Permission | Valeur |
|---|---|
| View Reports | No |
| Delete | No |
| Assign | No |
| Update | No |
| Create | No |
| **Read** | ✅ **Yes** |

> Le support voit les alertes Defender mais ne peut ni les fermer
> ni créer de politiques EDR.

---

## Modèle complet — Rôle Support Technique

| Catégorie | Permission | Actions autorisées | Actions interdites |
|---|---|---|---|
| **Appareils** | Read, Restart, Lock, Wipe | Consulter l'inventaire, Redémarrer, Verrouiller, Effacer | Delete (suppression définitive) |
| **Applications** | Managed apps (Read) | Voir la liste des apps et leur état | Modifier ou ajouter des apps |
| **Conformité** | Compliance policies (Read) | Consulter les règles de conformité | Créer ou modifier des règles |
| **Configuration** | Device configuration (Read) | Voir les profils Wi-Fi, VPN, restrictions | CUD (Create, Update, Delete) |
| **Sécurité** | Endpoint security (Read) | État Defender, BitLocker, Antivirus, Pare-feu | Modification des politiques |
| **Reporting** | Reports (Read) | Générer et consulter tous les rapports | Aucune restriction sur la lecture |

---

## ⚡ Résumé — Points clés exam

| Concept | À retenir |
|---|---|
| RBAC Intune | Contrôle granulaire des permissions par rôle |
| Rôle built-in | 11 rôles prédéfinis dans Intune |
| Custom role | Créer un rôle sur mesure avec permissions granulaires |
| Read = Yes | Pattern du rôle Support Technique |
| PIM Méthode 1 | Élévation ~10 sec via rôle Intune Administrator |
| PIM Méthode 2 | Élévation ~15 min via PIM for Groups |
| Moindre privilège | Principe fondamental — droits permanents à éviter |
| Help Desk Operator | Rôle built-in pour le support — actions à distance |
| Endpoint Security Manager | Rôle built-in pour la sécurité et CA |
| Scope tags | Limiter la visibilité d'un admin à un sous-ensemble d'appareils |