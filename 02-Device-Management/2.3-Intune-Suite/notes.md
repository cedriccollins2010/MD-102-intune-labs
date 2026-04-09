# 2.3 — Intune Suite Add-on Capabilities

> **Domaine MD-102 :** 2. Manage and maintain devices (30–35%)
> **Tenant de lab :** CedricTanekeuSomkwe.onmicrosoft.com
> **Référence officielle :** https://learn.microsoft.com/en-us/mem/intune/fundamentals/intune-add-ons

---

## Vue d'ensemble — Intune Suite

| Fonctionnalité | Description |
|---|---|
| **Endpoint Privilege Management (EPM)** | Élévation temporaire des droits sans compte admin |
| **Enterprise App Catalog** | Catalogue d'apps pré-packagées pour déploiement simplifié |
| **Remote Help** | Assistance à distance sécurisée via Intune |
| **Cloud PKI** | Infrastructure PKI managée dans le cloud |
| **Microsoft Tunnel for MAM** | VPN pour apps MAM sans enrollment complet |

> 💡 **Point clé MD-102 :** Les fonctionnalités Intune Suite
> nécessitent une **licence add-on** en plus de la licence Intune de base.

---

## 2.3.1 — Endpoint Privilege Management (EPM)

**Portail :** Intune → Endpoint security → Endpoint Privilege Management

### Concept

EPM permet aux utilisateurs standard d'élever temporairement leurs
droits pour des tâches spécifiques **sans avoir besoin d'un compte
administrateur local permanent**.

| Sans EPM | Avec EPM |
|---|---|
| Compte admin local permanent | Élévation temporaire à la demande |
| Risque de sécurité élevé | Principe du moindre privilège respecté |
| Gestion manuelle | Géré centralement via Intune |
| Pas de traçabilité | Logs d'élévation dans Intune |

---

### Créer une Elevation Settings Policy

**Portail :** Intune → Endpoint security → Endpoint Privilege Management
→ Policies → Create policy → Elevation settings policy

#### Paramètres clés

| Paramètre | Description | Valeur recommandée |
|---|---|---|
| **Default elevation response** | Comportement par défaut pour les demandes d'élévation non couvertes par une règle | Deny all |
| **Send elevation data for reporting** | Envoyer les données d'élévation pour le reporting | Enabled |
| **Windows elevation settings** | Activer EPM sur les appareils | Enabled |
| **Validation** | Demander une justification ou approbation | Require justification |

#### Modes d'élévation

| Mode | Description | Cas d'usage |
|---|---|---|
| **Automatic** | Élévation automatique sans interaction utilisateur | Apps internes approuvées |
| **User confirmed** | L'utilisateur confirme l'élévation | Apps spécifiques approuvées |
| **Support approved** | Approbation requise par le support IT | Apps non testées |
| **Denied** | Élévation refusée | Apps non autorisées |

---

### Création des Elevation Rules

**Portail :** Intune → Endpoint security → EPM → Policies
→ Create policy → Elevation rules policy

#### Paramètres d'une règle d'élévation

| Paramètre | Description |
|---|---|
| **Rule name** | Nom descriptif de la règle |
| **Description** | Contexte et justification |
| **Elevation type** | Automatic / User confirmed / Support approved |
| **File path** | Chemin de l'exécutable à élever |
| **File hash** | Hash SHA-256 du fichier — vérification d'intégrité |
| **Certificate** | Certificat de signature de l'exécutable |
| **Product name** | Nom du produit dans les métadonnées du fichier |

#### Exemple de règle — Installer une imprimante

---

## 2.3.4 — Microsoft Intune Remote Help

**Portail :** Intune → Tenant admin → Remote Help

### Concept

Remote Help permet au support IT de se connecter à distance aux
appareils des utilisateurs de manière **sécurisée et auditée**,
directement depuis Intune.

### Modes de connexion

| Mode | Description | Cas d'usage |
|---|---|---|
| **View screen** | Voir l'écran de l'utilisateur sans contrôle | Diagnostic visuel |
| **Full control** | Contrôle complet de l'appareil | Résolution de problème |
| **Elevation** | Élévation des droits pendant la session | Tâches admin à distance |

### Avantages vs outils traditionnels

| Critère | Remote Help | TeamViewer / RDP |
|---|---|---|
| Intégration Intune | ✅ Native | ❌ Externe |
| Audit trail | ✅ Logs dans Intune | ❌ Limité |
| Authentification | ✅ Entra ID | Variable |
| Licence requise | Intune Suite add-on | Licence tierce |
| Conditional Access | ✅ Compatible | ❌ |

### Prérequis

| Prérequis | Détail |
|---|---|
| Licence | Intune Suite ou Remote Help add-on |
| OS supportés | Windows 10/11, Android (preview) |
| Agent | Remote Help app installée sur l'appareil |
| Rôle Intune | Help Desk Operator ou supérieur |

---

## 2.3.5 — Cloud PKI

**Portail :** Intune → Tenant admin → Cloud PKI

### Concept

Cloud PKI est une infrastructure PKI entièrement managée dans le cloud
— **sans serveur NDES ou ADCS on-prem**.

### Composants

| Composant | Description |
|---|---|
| **Root CA** | Autorité de certification racine — créée dans Intune |
| **Issuing CA** | Autorité de certification émettrice — émet les certificats |
| **SCEP** | Protocol d'enrollment des certificats — utilisé par Intune |
| **PKCS** | Certificats avec clé privée — pour l'authentification |

### Cas d'usage Cloud PKI

| Scénario | Solution |
|---|---|
| Wi-Fi d'entreprise avec certificat | SCEP profile → Cloud PKI |
| VPN avec authentification par certificat | PKCS profile → Cloud PKI |
| S/MIME pour emails signés/chiffrés | PKCS profile → Cloud PKI |
| Authentification sans mot de passe | Certificat + WHfB |

### Avantages vs PKI on-prem

| Critère | Cloud PKI | PKI on-prem (ADCS) |
|---|---|---|
| Infrastructure serveur | ❌ Aucune | ✅ Requise |
| Maintenance | Microsoft | IT interne |
| Scalabilité | Automatique | Manuelle |
| Coût | Licence add-on | Serveurs + licences |
| Intégration Intune | Native | Via NDES connector |

> 💡 **Point clé MD-102 :** Cloud PKI élimine le besoin de
> serveurs NDES/ADCS on-prem pour les environnements cloud-first.

---

## 2.3.6 — Microsoft Tunnel for MAM

**Portail :** Intune → Tenant admin → Microsoft Tunnel Gateway

### Concept

Microsoft Tunnel for MAM permet aux apps mobiles protégées par
des **App Protection Policies (MAM)** d'accéder aux ressources
internes **sans enrollment complet de l'appareil**.

### Tunnel standard vs Tunnel for MAM

| Critère | Microsoft Tunnel (MDM) | Microsoft Tunnel for MAM |
|---|---|---|
| Enrollment requis | ✅ Oui | ❌ Non |
| Cible | Appareils enrollés | Apps MAM (BYOD) |
| Plateforme | iOS, Android, Windows | iOS, Android |
| Cas d'usage | Appareils corporate | BYOD — accès ressources internes |

### Architecture

### Prérequis

| Prérequis | Détail |
|---|---|
| Serveur Linux | Ubuntu 18.04/20.04 ou RHEL 8+ |
| Licence | Intune Suite ou Microsoft Tunnel add-on |
| Certificat TLS | Pour sécuriser la connexion |
| App Defender | Microsoft Defender doit être installé |

---

## ⚡ Résumé — Points clés exam

| Concept | À retenir |
|---|---|
| EPM | Élévation temporaire sans compte admin permanent |
| File hash | Garantit l'intégrité de l'exécutable élevé |
| Automatic elevation | Pas d'interaction utilisateur |
| User confirmed | Utilisateur confirme l'élévation |
| Support approved | Approbation IT requise |
| Enterprise App Catalog | Apps pré-packagées — pas besoin de .intunewin |
| Remote Help | Assistance à distance native Intune — auditée |
| Full control | Mode Remote Help avec contrôle complet |
| Cloud PKI | PKI managée cloud — sans NDES/ADCS on-prem |
| SCEP | Protocol d'enrollment des certificats |
| PKCS | Certificats avec clé privée |
| Tunnel for MAM | VPN pour apps MAM sans enrollment — BYOD |
| Tunnel MDM | VPN pour appareils enrollés |
| Linux server | Requis pour Microsoft Tunnel Gateway |