# 2.2 — Device Configuration Profiles

> **Domaine MD-102 :** 2. Manage and maintain devices (30–35%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/mem/intune/configuration/

---

## 2.2.1 — Windows Devices

### Trois méthodes de configuration

| Méthode | Description | Cas d'usage |
|---|---|---|
| **Security Baseline** | Paramètres préconfigurés par Microsoft — bonne pratique sécurité | Déploiement rapide avec paramètres recommandés |
| **Settings Catalog** | Catalogue exhaustif de paramètres — granularité maximale | Configuration précise et personnalisée |
| **Templates** | Modèles prédéfinis pour des scénarios spécifiques | Wi-Fi, VPN, certificats, ADMX |

---

### Via Security Baseline

**Portail :** Intune → Endpoint security → Security baselines

#### Baselines disponibles

| Baseline | Description |
|---|---|
| **Microsoft Defender for Endpoint** | Paramètres sécurité Defender |
| **Microsoft 365 Apps for Enterprise** | Paramètres sécurité Office |
| **Windows 365 Cloud PC** | Paramètres pour Cloud PCs |
| **Windows** ⭐ | Baseline sécurité Windows — la plus utilisée |

#### Avantages des Security Baselines

| Avantage | Détail |
|---|---|
| Préconfigurées par Microsoft | Paramètres recommandés testés en production |
| Mises à jour automatiques | Nouvelles versions publiées régulièrement |
| Comparaison de versions | Voir les différences entre versions de baseline |
| Conflits détectés | Intune signale les conflits avec d'autres politiques |

> ⚠ **Point clé MD-102 :** Les Security Baselines peuvent entrer
> en conflit avec les Settings Catalog — toujours vérifier les
> conflits dans le rapport de conformité.

---

> 💡 **Point clé MD-102 :** ADMX Import permet de gérer via Intune
> des paramètres qui n'existaient qu'en GPO on-prem.

---

### Cibler les profils avec des Filters

**Portail :** Intune → Tenant admin → Filters → Create

#### Propriétés disponibles pour les filtres Windows

| Propriété | Exemple |
|---|---|
| `deviceName` | `contains "DESKTOP"` |
| `deviceOSVersion` | `startsWith "10.0.22"` (Windows 11) |
| `manufacturer` | `equals "Microsoft"` |
| `model` | `contains "Surface"` |
| `enrollmentProfileName` | `equals "Autopilot-Corp"` |

#### Modes d'application des filtres

| Mode | Comportement |
|---|---|
| **Include** | Appliquer la politique uniquement aux appareils qui correspondent |
| **Exclude** | Exclure les appareils qui correspondent |

> 💡 Les filtres permettent un ciblage granulaire **sans créer
> de groupes supplémentaires**.

---

## 2.2.2 — Android Devices

### Paramètres de configuration Android Enterprise

**Portail :** Intune → Devices → Configuration → Create → Android Enterprise

#### A. Device Restrictions — Sécurité

| Paramètre | Valeur recommandée |
|---|---|
| Camera | **Blocked** |
| Screen capture | **Blocked** |
| USB file transfer | **Blocked** |
| Factory reset | **Blocked** |
| Unknown sources | **Blocked** |

> Ces restrictions préviennent les fuites de données et établissent
> une baseline sécurité entreprise.

#### B. Password & Lock Screen

| Paramètre | Valeur |
|---|---|
| Password type | Complex |
| Minimum length | 6–8 |
| Auto-lock | 5 minutes |
| Biometrics | Allowed |

---

## 2.2.3 — iOS/iPadOS Devices

**Portail :** Intune → Devices → Configuration → Create → iOS/iPadOS

### Types de profils iOS disponibles

| Type | Description |
|---|---|
| **Device restrictions** | Caméra, AirDrop, iCloud, App Store |
| **Wi-Fi** | Profils Wi-Fi d'entreprise |
| **VPN** | Profils VPN (Per-app VPN disponible) |
| **Email** | Exchange ActiveSync |
| **Certificate** | PKCS / SCEP |
| **Custom** | Profils .mobileconfig personnalisés |

### Paramètres clés iOS

| Catégorie | Paramètre | Valeur recommandée |
|---|---|---|
| **Security** | Require password | Yes |
| **Security** | Minimum password length | 6 |
| **Security** | Jailbroken devices | Block |
| **iCloud** | iCloud backup | Blocked (corporate data) |
| **App Store** | Installing apps | Blocked (si COBO) |
| **AirDrop** | AirDrop | Blocked |

---

## 2.2.4 — macOS Devices

**Portail :** Intune → Devices → Configuration → Create → macOS

### Types de profils macOS disponibles

| Type | Description |
|---|---|
| **Settings catalog** | Paramètres macOS granulaires |
| **Templates** | Certificate, VPN, Wi-Fi, Device restrictions |
| **Shell scripts** | Scripts bash déployés via Intune |
| **Custom** | Profils .mobileconfig |

---

## 2.2.5 — Enterprise Multi-Session Devices

| Concept | Détail |
|---|---|
| **Plateforme** | Windows 10/11 Enterprise multi-session |
| **Cas d'usage** | Azure Virtual Desktop (AVD) |
| **Gestion** | Via Intune — profils de configuration dédiés |
| **Particularité** | Plusieurs utilisateurs simultanément sur la même VM |

---

## Conflits entre politiques — Résolution

| Scénario | Comportement Intune |
|---|---|
| Deux politiques configurent le même paramètre avec des valeurs différentes | **Conflit** — le paramètre passe en état "Conflict" |
| Security Baseline + Settings Catalog sur le même paramètre | **Conflit** — Security Baseline a priorité |
| Politique assignée à un groupe + exclusion d'un sous-groupe | L'exclusion a priorité |

> 💡 **Point clé MD-102 :** En cas de conflit, Intune ne choisit
> pas une valeur — il marque le paramètre en **Conflict** et
> n'applique aucune des deux valeurs.

---

## ⚡ Résumé — Points clés exam

| Concept | À retenir |
|---|---|
| Settings Catalog | Catalogue exhaustif — granularité maximale |
| Security Baseline | Paramètres Microsoft préconfigurés — déploiement rapide |
| Templates | Scénarios spécifiques — Wi-Fi, VPN, ADMX, Kiosk |
| ADMX Import | GPO on-prem → Intune MDM |
| Filters | Ciblage granulaire sans groupes supplémentaires |
| Include / Exclude | Modes d'application des filtres |
| Conflict | Intune marque en Conflict — aucune valeur appliquée |
| Camera Blocked | Restriction Android/iOS de base |
| Per-app VPN | Disponible sur iOS — VPN uniquement pour certaines apps |
| Multi-session | Windows Enterprise pour Azure Virtual Desktop |
| OMA-URI | Custom template — CSP non disponibles dans Settings Catalog |
| Policy refresh | Toutes les 8 heures environ — ou sync manuelle |

### Via Settings Catalog

**Portail :** Intune → Devices → Configuration → Create → New policy → Settings catalog

#### Catégories principales disponibles

| Catégorie | Exemples de paramètres |
|---|---|
| **Authentication** | Windows Hello, FIDO2 |
| **BitLocker** | Chiffrement, méthode, PIN |
| **Defender** | Antivirus, ASR, Cloud protection |
| **Delivery Optimization** | Mode téléchargement, bande passante |
| **Edge** | Paramètres navigateur Edge |
| **Firewall** | Règles entrantes/sortantes |
| **Microsoft Office** | Paramètres Office via Intune |
| **Password** | Complexité, longueur, expiration |
| **Privacy** | Télémétrie, diagnostics |
| **Start** | Personnalisation du menu Démarrer |
| **Update** | Windows Update for Business |
| **Wi-Fi** | Profils Wi-Fi d'entreprise |

#### Workflow de création