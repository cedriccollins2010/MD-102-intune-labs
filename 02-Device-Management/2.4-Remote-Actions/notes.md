# 2.4 — Remote Actions sur les appareils

> **Domaine MD-102 :** 2. Manage and maintain devices (30–35%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/mem/intune/remote-actions/

---

## Vue d'ensemble — Actions à distance disponibles par plateforme

| Action | Windows | iOS/iPadOS | Android | macOS |
|---|---|---|---|---|
| Retire | ✅ | ✅ | ✅ | ✅ |
| Wipe | ✅ | ✅ | ✅ | ✅ |
| Delete | ✅ | ✅ | ✅ | ✅ |
| Remote lock | ✅ | ✅ | ✅ | ✅ |
| Reset passcode | ❌ | ✅ | ✅ | ❌ |
| Restart | ✅ | ❌ | ❌ | ✅ |
| Sync | ✅ | ✅ | ✅ | ✅ |
| Rename | ✅ | ✅ | ❌ | ✅ |
| Collect diagnostics | ✅ | ❌ | ❌ | ❌ |
| BitLocker key rotation | ✅ | ❌ | ❌ | ❌ |
| Update Defender intelligence | ✅ | ❌ | ❌ | ❌ |

---

## Actions individuelles — Détail

### Retire (Désinscription)

| Critère | Détail |
|---|---|
| **Effet** | Supprime les données et apps d'entreprise — garde les données personnelles |
| **MDM profile** | Supprimé |
| **Données personnelles** | Conservées |
| **Cas d'usage** | Départ d'un employé / BYOD / fin de contrat |

### Wipe (Réinitialisation complète)

| Critère | Détail |
|---|---|
| **Effet** | Réinitialisation usine complète — supprime tout |
| **MDM profile** | Supprimé |
| **Données personnelles** | **Supprimées** |
| **Cas d'usage** | Appareil perdu / volé / réaffectation |

> ⚠ **Point clé MD-102 :** Retire conserve les données
> personnelles. Wipe supprime tout — utiliser avec précaution.

### Différence Retire vs Wipe vs Delete

| Action | Données entreprise | Données personnelles | Appareil réutilisable |
|---|---|---|---|
| **Retire** | Supprimées | Conservées | ✅ Oui |
| **Wipe** | Supprimées | Supprimées | ✅ Oui (réinitialisé) |
| **Delete** | Supprimées | Supprimées | ✅ Oui (retiré d'Intune) |

### Remote Lock

| Plateforme | Comportement |
|---|---|
| **Windows** | Verrouille l'écran — nécessite le PIN/mot de passe pour déverrouiller |
| **iOS/iPadOS** | Verrouille l'appareil immédiatement |
| **Android** | Verrouille + peut définir un PIN temporaire |
| **macOS** | Verrouille + affiche un message personnalisé |

### Reset Passcode

| Plateforme | Comportement |
|---|---|
| **iOS/iPadOS** | Supprime le code PIN — l'utilisateur doit en créer un nouveau |
| **Android** | Réinitialise le PIN selon la version Android |

> ⚠ Non disponible sur Windows — utiliser Remote Lock à la place.

### Sync

> 💡 **Point clé MD-102 :** Les bulk actions sont mises en
> **file d'attente** — elles s'exécutent quand l'appareil
> contacte Intune (sync ou check-in).

---

## 2.4.3 — Mettre à jour Defender Security Intelligence

**Portail :** Intune → Devices → [Appareil Windows] → Update Microsoft Defender security intelligence

### Concept

Force la mise à jour des **définitions antivirus** de Microsoft
Defender sur un appareil spécifique, sans attendre la mise à
jour automatique planifiée.

| Critère | Détail |
|---|---|
| **Plateforme** | Windows uniquement |
| **Effet** | Télécharge les dernières définitions de virus |
| **Délai** | Quelques minutes |
| **Cas d'usage** | Après une alerte de sécurité / avant un scan |

### Via PowerShell (alternative)

```powershell

---

## 2.4.4 — Rotation des clés BitLocker

**Portail :** Intune → Devices → [Appareil Windows] → BitLocker key rotation

### Concept

Force la rotation de la **clé de récupération BitLocker** et
sauvegarde la nouvelle clé dans Entra ID.

| Critère | Détail |
|---|---|
| **Plateforme** | Windows uniquement |
| **Prérequis** | BitLocker activé + appareil Entra Joined ou Hybrid |
| **Effet** | Génère une nouvelle clé de récupération |
| **Stockage** | Nouvelle clé sauvegardée dans Entra ID automatiquement |

### Où trouver la clé de récupération BitLocker

### Cas d'usage rotation BitLocker

| Scénario | Justification |
|---|---|
| Clé compromise / partagée | Sécurité — invalider l'ancienne clé |
| Après réinitialisation du mot de passe admin | Bonne pratique sécurité |
| Audit de sécurité | Rotation périodique recommandée |
| Appareil réaffecté | Nouvelle clé pour le nouvel utilisateur |

### Via PowerShell

```powershell
# Voir le statut BitLocker
Get-BitLockerVolume

# Sauvegarder la clé dans Entra ID manuellement
$BLV = Get-BitLockerVolume -MountPoint "C:"
BackupToAAD-BitLockerKeyProtector -MountPoint "C:" `
    -KeyProtectorId $BLV.KeyProtector[1].KeyProtectorId
```

---

## 2.4.5 — Device Queries avec KQL

**Portail :** Intune → Devices → [Appareil Windows] → Device query

### Concept

Device query permet d'interroger un appareil Windows en temps
réel avec des requêtes **KQL (Kusto Query Language)** pour
obtenir des informations sur son état.

> 💡 **Point clé MD-102 :** KQL dans Intune = requêtes temps
> réel sur un appareil spécifique. À ne pas confondre avec
> Azure Log Analytics (hors scope MD-102).

### Tables disponibles dans Device query

| Table | Données disponibles |
|---|---|
| `BatteryInfo` | Niveau batterie, état de charge |
| `CertificateInfo` | Certificats installés |
| `DeviceInfo` | Nom, OS, version, fabricant |
| `DiskInfo` | Espace disque, partitions |
| `EventLogs` | Événements Windows |
| `FileInfo` | Informations sur des fichiers spécifiques |
| `InstalledApps` | Applications installées |
| `LocalAdmins` | Membres du groupe Administrateurs local |
| `LocalGroups` | Groupes locaux |
| `LocalUsers` | Utilisateurs locaux |
| `NetworkInfo` | Interfaces réseau, IP, MAC |
| `ProcessInfo` | Processus en cours d'exécution |
| `RegistryInfo` | Valeurs de registre |
| `ServiceInfo` | Services Windows |
| `StartupApps` | Applications au démarrage |
| `WindowsQFE` | Mises à jour Windows installées |

### Exemples de requêtes KQL

```kql
// Lister toutes les applications installées
InstalledApps
| project AppName, AppVersion, Publisher
| sort by AppName asc

// Vérifier les administrateurs locaux
LocalAdmins
| project Name, Domain, AccountType

// Vérifier l'espace disque
DiskInfo
| project DriveLetter, TotalSpace, FreeSpace
| extend UsedPercent = (TotalSpace - FreeSpace) * 100 / TotalSpace

// Vérifier les mises à jour Windows installées
WindowsQFE
| project HotFixID, InstalledOn, Description
| sort by InstalledOn desc

// Vérifier les processus en cours
ProcessInfo
| where ProcessName contains "defender"
| project ProcessName, ProcessId, CPUUsage

// Vérifier les certificats expirés
CertificateInfo
| where ExpirationDate < now()
| project SubjectName, ExpirationDate, Issuer

// Vérifier les services arrêtés
ServiceInfo
| where StartType == "Automatic" and State != "Running"
| project ServiceName, DisplayName, State
```

### Prérequis Device query

| Prérequis | Détail |
|---|---|
| **OS** | Windows 10 21H2+ / Windows 11 |
| **Licence** | Intune Plan 2 ou Intune Suite |
| **Connectivité** | Appareil doit être en ligne |
| **Rôle Intune** | Help Desk Operator ou supérieur |

---

## Dépannage — Actions à distance qui échouent

| Problème | Cause probable | Solution |
|---|---|---|
| Action en attente depuis longtemps | Appareil hors ligne | Attendre que l'appareil se connecte |
| Wipe échoue | Appareil déjà réinitialisé | Utiliser Delete pour retirer d'Intune |
| BitLocker key rotation échoue | BitLocker non activé | Vérifier la politique BitLocker |
| Collect diagnostics échoue | Agent Intune non à jour | Forcer une sync puis réessayer |
| Remote lock sans effet | PIN non configuré (Windows) | Vérifier la politique de mot de passe |

---

## ⚡ Résumé — Points clés exam

| Concept | À retenir |
|---|---|
| Retire | Supprime données entreprise — garde données personnelles |
| Wipe | Réinitialisation complète — supprime tout |
| Delete | Retire l'appareil d'Intune seulement |
| Remote lock | Verrouillage à distance — toutes plateformes |
| Reset passcode | iOS et Android uniquement — pas Windows |
| Sync | Force le check-in avec Intune |
| Collect diagnostics | Windows uniquement — logs MDM, Defender, EventLogs |
| Bulk actions | File d'attente — exécutées au prochain check-in |
| BitLocker rotation | Génère nouvelle clé — sauvegardée dans Entra ID |
| KQL Device query | Requêtes temps réel sur appareil — Intune Plan 2 requis |
| InstalledApps | Table KQL pour lister les apps installées |
| LocalAdmins | Table KQL pour vérifier les admins locaux |
| Update Defender | Force mise à jour définitions antivirus — Windows only |
| Autopilot Reset | Réinitialise et réapplique le profil Autopilot |