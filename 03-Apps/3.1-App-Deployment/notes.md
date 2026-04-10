# 3.1 — Déploiement et mise à jour des applications

> **Domaine MD-102 :** 3. Manage applications (15–20%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/mem/intune/apps/

---

## 3.1.1 — Préparer les applications pour le déploiement

### Étape 1 — Configurer Intune

| Tâche | Portail |
|---|---|
| Configure a custom domain name | Entra ID → Custom domain names |
| Add users to Intune | Entra ID → Users → + New user |
| Create groups in Intune | Entra ID → Groups → + New group |
| Manage licenses | Microsoft 365 admin center → Billing → Licenses |
| Manage Roles and admin permissions | Intune → Tenant admin → Roles |
| Set the MDM authority | Intune → Tenant admin → Tenant status |
| Customize the Company Portal | Intune → Tenant admin → Customization |

### Étape 2 — Ajouter et protéger les applications

| Tâche | Description |
|---|---|
| Déployer Microsoft 365 Apps | Suite Office via Intune |
| Configurer les App Protection Policies | MAM — protéger les données sans enrollment |
| Déployer les apps Win32 | Package .intunewin |
| Déployer MSI / MSIX / APPX | Packages Windows natifs |

### Étape 3 — Conformité et Conditional Access

### Étape 4 — Configurer les fonctionnalités des appareils

| Fonctionnalité | Description |
|---|---|
| Device configuration profiles | Wi-Fi, VPN, certificats |
| Security baselines | Paramètres sécurité Microsoft |
| Endpoint security policies | Defender, BitLocker, Firewall |

### Étape 5 — Enrôler les appareils

| Méthode | Plateforme |
|---|---|
| Autopilot | Windows |
| ADE / ABM | iOS/iPadOS |
| Android Enterprise | Android |
| Company Portal | Toutes plateformes (BYOD) |

---

## 3.1.2 — Déployer des apps via Intune

### Types d'applications disponibles dans Intune

| Type | Description | Plateforme |
|---|---|---|
| **Win32 app** | Package .intunewin — apps complexes | Windows |
| **Line-of-business (LOB)** | MSI / MSIX / APPX — apps internes | Windows, iOS, Android |
| **Microsoft Store app** | Apps du Microsoft Store (WinGet) | Windows |
| **Web app** | Lien vers une app web | Toutes |
| **Built-in app** | Apps Microsoft préintégrées (Office, Edge) | iOS, Android |
| **Microsoft 365 Apps** | Suite Office complète | Windows, macOS |
| **Android Enterprise app** | Apps Google Play managé | Android |
| **iOS store app** | Apps App Store Apple | iOS/iPadOS |
| **macOS app** | PKG / DMG | macOS |

### Types d'assignation

| Type | Comportement | Cas d'usage |
|---|---|---|
| **Required** | Installation forcée — pas de choix utilisateur | Apps obligatoires entreprise |
| **Available** | Disponible dans Company Portal — optionnel | Apps recommandées |
| **Uninstall** | Désinstallation forcée | Retrait d'une app non autorisée |
| **Available without enrollment** | Disponible via Company Portal web — sans MDM | MAM BYOD |

> 💡 **Point clé MD-102 :** Required = forcé. Available = optionnel
> dans Company Portal. Uninstall = suppression forcée.

---

## 3.1.3 — Déployer Microsoft 365 Apps via Intune

**Portail :** Intune → Apps → Windows → + Add → Microsoft 365 Apps for Windows 10 and later

### Configuration de la suite M365 Apps

| Paramètre | Options disponibles |
|---|---|
| **Suite apps** | Word, Excel, PowerPoint, Outlook, Teams, OneNote, Access, Publisher |
| **Update channel** | Current / Monthly Enterprise / Semi-Annual Enterprise |
| **Version** | Latest / Specific version |
| **Architecture** | 32-bit / 64-bit |
| **Languages** | Français, Anglais, + autres |
| **Remove other versions** | Yes / No |
| **Use shared computer activation** | Yes (pour RDS/AVD) / No |

### Canaux de mise à jour M365 Apps

| Canal | Fréquence | Cas d'usage |
|---|---|---|
| **Current Channel** | Mensuel | Utilisateurs early adopters |
| **Monthly Enterprise Channel** | Mensuel (2e mardi) | Équilibre nouveautés / stabilité |
| **Semi-Annual Enterprise Channel** | Semestriel (janvier + juillet) | Environnements stables / régulés |

> 💡 **Point clé MD-102 :** Semi-Annual Enterprise Channel =
> environnements qui nécessitent une stabilité maximale
> (banques, hôpitaux, secteur public).

---

## 3.1.4 — Configurer les politiques pour les apps Office

**Portail :** Intune → Apps → Policies for Office apps

### Types de politiques Office

| Type | Description |
|---|---|
| **App configuration policies** | Préconfigurer les paramètres des apps Office |
| **App protection policies** | Protéger les données dans les apps Office |
| **Office cloud policy** | Politiques Office via le cloud — s'applique à l'utilisateur |

### Paramètres configurables via politiques Office

| Paramètre | Description |
|---|---|
| Macro settings | Bloquer / autoriser les macros VBA |
| Add-ins | Contrôler les add-ins autorisés |
| File blocking | Bloquer certains types de fichiers |
| Privacy settings | Contrôler les données de diagnostic |
| Connected experiences | Activer / désactiver les services cloud Office |

---

## 3.1.5 — Déployer M365 Apps pendant Autopilot

### Office Deployment Tool (ODT)

| Critère | Détail |
|---|---|
| **Outil** | setup.exe + configuration XML |
| **Cas d'usage** | Déploiement personnalisé d'Office |
| **Avantage** | Contrôle total sur les apps, canaux, langues |
| **Fichier config** | configuration.xml |

#### Exemple de configuration ODT

```xml

> ⚠ **Point clé MD-102 :** Pour qu'Office s'installe pendant
> l'Autopilot, l'app doit être assignée en **Required** et
> bloquée dans l'ESP jusqu'à installation complète.

---

## 3.1.6 — Microsoft 365 Apps Admin Center

**URL :** https://config.office.com

### Fonctionnalités disponibles

| Fonctionnalité | Description |
|---|---|
| **Office Customization Tool** | Générer configuration.xml |
| **Inventory** | Inventaire des installations Office dans le tenant |
| **Health** | Santé des installations Office — crashs, add-ins |
| **Security** | Politiques de sécurité Office |
| **Servicing** | Gestion des canaux de mise à jour |

---

## 3.1.7 — Déployer des apps depuis les stores

### Microsoft Store (WinGet)

**Portail :** Intune → Apps → Windows → + Add → Microsoft Store app (new)

| Critère | Détail |
|---|---|
| **Backend** | WinGet (Windows Package Manager) |
| **Avantage** | Mises à jour automatiques gérées par Microsoft |
| **Recherche** | Directement dans Intune |
| **Cas d'usage** | Apps populaires : 7-Zip, VLC, Zoom, Chrome |

### Apple App Store (iOS)

**Portail :** Intune → Apps → iOS/iPadOS → + Add → iOS store app

| Critère | Détail |
|---|---|
| **VPP (Volume Purchase Program)** | Acheter des licences en volume via ABM |
| **Managed distribution** | Distribution sans Apple ID personnel |
| **Silent install** | Installation sans interaction sur appareils supervisés |

### Google Play (Android)

**Portail :** Intune → Apps → Android → + Add → Managed Google Play app

| Critère | Détail |
|---|---|
| **Managed Google Play** | Store Google approuvé pour l'entreprise |
| **Approbation** | IT approuve les apps avant déploiement |
| **Silent install** | Sur appareils Fully Managed / Dedicated |

---

## Win32 App — Packaging et déploiement

### Étapes de packaging

### Commande de packaging

```powershell
# Télécharger l'outil
# https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool

# Packager l'app
.\IntuneWinAppUtil.exe `
    -c "C:\Source\MyApp" `
    -s "setup.exe" `
    -o "C:\Output"
# Résultat : MyApp.intunewin
```

### Configuration Win32 dans Intune

| Champ | Description | Exemple |
|---|---|---|
| **Install command** | Commande d'installation silencieuse | `setup.exe /S /quiet` |
| **Uninstall command** | Commande de désinstallation | `setup.exe /uninstall /quiet` |
| **Install behavior** | System ou User | System (recommandé) |
| **Detection rule** | Comment Intune vérifie si l'app est installée | Fichier / Registre / MSI |
| **Return codes** | Codes de retour de l'installeur | 0 = succès, 1707 = succès, 3010 = reboot |

### Detection rules

| Type | Description | Exemple |
|---|---|---|
| **MSI product code** | GUID du package MSI | `{12345678-...}` |
| **File** | Existence d'un fichier | `C:\Program Files\MyApp\app.exe` |
| **Registry** | Clé ou valeur de registre | `HKLM\Software\MyApp` |
| **Script** | Script PowerShell personnalisé | Retourne 0 si installé |

---

## ⚡ Résumé — Points clés exam

| Concept | À retenir |
|---|---|
| Win32 app | Package .intunewin — apps complexes Windows |
| LOB app | MSI/MSIX/APPX — apps internes |
| Required | Installation forcée |
| Available | Optionnel dans Company Portal |
| Uninstall | Désinstallation forcée |
| M365 Apps | Déployées via Intune — canal configurable |
| Current Channel | Mises à jour mensuelles fréquentes |
| Semi-Annual | Mises à jour semestrielles — environnements stables |
| ODT | Office Deployment Tool — configuration XML |
| OCT | Office Customization Tool — interface web config.office.com |
| ESP + Required | Obligatoire pour installer Office pendant Autopilot |
| WinGet | Backend Microsoft Store apps dans Intune |
| VPP | Volume Purchase Program — licences Apple en volume |
| Managed Google Play | Store Android approuvé pour entreprise |
| .intunewin | Format de package Win32 pour Intune |
| Detection rule | Vérifie si l'app est installée — fichier/registre/MSI/script |
| Install behavior System | Installation pour tous les utilisateurs de l'appareil |