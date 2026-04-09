# 2.1 — Windows Autopilot

> **Domaine MD-102 :** 2. Manage and maintain devices (30–35%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/autopilot/

---

## 2.1.1 — Autopilot vs Provisioning Packages

| Critère | Windows Autopilot | Provisioning Packages |
|---|---|---|
| **Outil** | Intune / Microsoft 365 admin center | Windows Configuration Designer |
| **Connectivité** | Requiert internet | Fonctionne hors ligne |
| **Cas d'usage** | Déploiement cloud moderne | Environnements isolés / air-gapped |
| **Interaction IT** | Zéro touch possible | Clé USB nécessaire |
| **Maintenance** | Profils gérés dans Intune | Packages à recréer manuellement |
| **Recommandé pour** | Entreprises modernes cloud-first | Cas spécifiques sans connectivité |

> 💡 **Point clé MD-102 :** Autopilot est la méthode recommandée
> pour les environnements cloud. Les provisioning packages restent
> utiles pour les déploiements hors ligne.

---

## 2.1.2 — Modes de déploiement Autopilot

### User-Driven

| Champ | Valeur |
|---|---|
| Deployment mode | User-Driven |
| Join type | Entra joined ou Hybrid Entra joined |
| Interaction utilisateur | L'utilisateur s'authentifie pendant l'OOBE |
| Cas d'usage | PC assigné à un employé qui se connecte lui-même |

### Self-Deploying

| Champ | Valeur |
|---|---|
| Deployment mode | Self-Deploying |
| Join type | Entra joined uniquement |
| Interaction utilisateur | **Aucune** — déploiement entièrement automatisé |
| Cas d'usage | Kiosk / appareil partagé / appareil sans utilisateur assigné |

> ⚠ Self-Deploying requiert un TPM 2.0 — pas d'authentification utilisateur.

### Pre-provisioned (White Glove)

| Champ | Valeur |
|---|---|
| Allow pre-provisioned deployment | Yes |
| Interaction IT | IT prépare l'appareil avant livraison |
| Interaction utilisateur | OOBE réduite — configuration déjà appliquée |
| Cas d'usage | IT configure l'appareil en avance pour l'utilisateur final |

---

## Decision Table — Windows Autopilot (OOBE)

| Scénario dans la question | Choix correct | Paramètre Autopilot | Logique exam |
|---|---|---|---|
| PC pour un employé qui se connecte | User-Driven | Deployment mode = User-Driven | Authentification utilisateur requise |
| Kiosk / partagé / sans utilisateur | Self-Deploying | Deployment mode = Self-Deploying | Aucune interaction utilisateur |
| Appareil cloud-only | Entra joined | Join to Microsoft Entra ID = Entra joined | Pas d'AD on-prem |
| Expérience utilisateur fluide | Masquer les écrans | License terms = Hide / Privacy settings = Hide | Réduire les prompts OOBE |
| Bloquer les comptes personnels | Bloquer | Hide change account options = Hide | Sécurité entreprise |
| Moindre privilège | Utilisateur standard | User account type = Standard | Bonne pratique sécurité |
| IT prépare avant livraison | Pre-provisioning | Allow pre-provisioned deployment = Yes | OOBE plus rapide |
| Pas de contact IT avant livraison | Config utilisateur à domicile | Allow pre-provisioned deployment = No | Déploiement distant |
| Nommage automatique requis | Template de nom | Apply device name template = Yes | Nommage centralisé |
| Noms uniques aléatoires | Suffixe aléatoire | %RAND:x% | Éviter les conflits de noms |
| Nommage par numéro de série | Numéro de série | %SERIAL% | Suivi des assets |
| Plusieurs régions | Clavier auto | Automatically configure keyboard = Yes | Confort utilisateur |
| Langue standard entreprise | Défaut OS | Language = Operating system default | Cohérence |
| Windows Hello requis | Connexion utilisateur | User-Driven + Entra joined | WHfB requiert un utilisateur |
| Conditional Access par utilisateur | Authentification utilisateur | User-Driven | CA évalue l'identité |

---

## 2.1.3 — Device Name Template

**Portail :** Intune → Devices → Enrollment → Windows → Deployment profiles → Properties

### Variables disponibles

| Variable | Description | Exemple |
|---|---|---|
| `%SERIAL%` | Numéro de série de l'appareil | PC-SN123456 |
| `%RAND:x%` | Nombre aléatoire de x chiffres | PC-4872 |

### Exemples de templates

| Template | Résultat |
|---|---|
| `CORP-%SERIAL%` | CORP-SN123456 |
| `WIN-%RAND:4%` | WIN-4872 |
| `IT-%SERIAL%-CA` | IT-SN123456-CA |

> 💡 **Point clé MD-102 :** Le template de nom s'applique
> automatiquement pendant l'OOBE — pas d'intervention manuelle.

---

## 2.1.4 — Déploiement via Autopilot

### Portails de création des profils Autopilot

| Portail | Chemin |
|---|---|
| **Intune admin center** ⭐ | Devices → Enrollment → Windows → Deployment profiles |
| Microsoft 365 admin center | Devices → Autopilot |
| Intune Graph API | Programmatique |

### Prérequis Autopilot

| Prérequis | Détail |
|---|---|
| Hardware Hash | Extrait et uploadé dans Intune |
| Licence | Microsoft 365 Business Premium / Intune |
| Profil Autopilot | Créé et assigné au groupe d'appareils |
| Connectivité internet | Requise pendant l'OOBE |
| TPM 2.0 | Requis pour Self-Deploying |

### Extraire le Hardware Hash

```powershell
# Installer le module
Install-Script -Name Get-WindowsAutoPilotInfo

# Extraire et exporter le Hardware Hash
Get-WindowsAutoPilotInfo -OutputFile AutoPilotHWID.csv
```

### Uploader le Hardware Hash dans Intune

**Portail :** Intune → Devices → Enrollment → Windows → Devices → Import

---

## 2.1.5 — Enrollment Status Page (ESP)

**Portail :** Intune → Devices → Enrollment → Windows → Enrollment Status Page

### Phases de l'ESP

| Phase | Description |
|---|---|
| **1. Device preparation** | Enregistrement de l'appareil dans Intune |
| **2. Device setup** | Installation des apps et politiques au niveau appareil |
| **3. Account setup** | Installation des apps et politiques au niveau utilisateur |

### Paramètres clés ESP

| Paramètre | Valeur recommandée | Description |
|---|---|---|
| Show app and profile configuration progress | Yes | Afficher la progression à l'utilisateur |
| Show error when installation takes longer than | 60 minutes | Timeout avant erreur |
| Allow users to collect logs | Yes | Facilite le dépannage |
| Block device use until required apps are installed | Yes | Garantir la conformité avant utilisation |
| Allow users to reset device if error occurs | No | Contrôle IT |

> 💡 **Point clé MD-102 :** L'ESP bloque l'accès au bureau
> jusqu'à ce que toutes les apps requises soient installées —
> garantit la conformité dès le premier démarrage.

### Dépannage ESP

| Problème | Cause probable | Solution |
|---|---|---|
| ESP bloqué à "Device setup" | App Win32 en échec | Vérifier les logs dans `%TEMP%\ESPLogs` |
| Timeout ESP | App trop longue à installer | Augmenter le timeout ou exclure l'app de l'ESP |
| ESP ne s'affiche pas | Profil ESP non assigné | Vérifier l'assignation du profil ESP |

---

## 2.1.6 — Provisioning Packages

**Outil :** Windows Configuration Designer ✅ (téléchargé dans le lab)

### Cas d'usage

| Scénario | Justification |
|---|---|
| Environnement air-gapped | Pas d'accès internet |
| Déploiement en masse rapide | Clé USB + NFC |
| Configuration pré-enrollment | Avant la connexion au réseau |

### Étapes de création