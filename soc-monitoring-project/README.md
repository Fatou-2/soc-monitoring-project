# 🛡️ Mise en place d'un SOC – Supervision, Détection & Réponse aux Incidents

Projet académique de mise en place d'un mini-SOC (Security Operations Center) permettant de superviser une infrastructure réseau, détecter des attaques courantes et mettre en œuvre des contre-mesures via un firewall FortiGate et une réponse active Wazuh.

## 🎯 Objectifs du projet

- Déployer un environnement de supervision centralisant les logs Windows/Linux
- Configurer un IDS/IPS (Suricata) et un SIEM (Wazuh) pour la détection d'incidents
- Simuler des scénarios d'attaque réels (SQL Injection, Brute Force, scan de vulnérabilités, exploitation de CVE)
- Analyser les alertes et les cartographier avec le framework **MITRE ATT&CK**
- Mettre en place des contre-mesures (règles de firewall FortiGate, réponse active Wazuh) et valider leur efficacité

## 🏗️ Architecture du lab

| Composant | Rôle | Outil |
|---|---|---|
| Firewall / Segmentation réseau | Filtrage LAN / DMZ / WAN, VPN | FortiGate (FortiOS 7.6.7) |
| Serveur applicatif (DMZ) | Hébergement de l'application vulnérable | DVWA (Damn Vulnerable Web Application) sur Apache/Ubuntu |
| IDS/IPS | Détection d'intrusion réseau | Suricata 7.0.8 |
| SIEM | Centralisation des logs, corrélation, alerting | Wazuh 4.x (Manager + Indexer + Dashboard) |
| Machine attaquante | Simulation d'attaques | Kali Linux (Nmap, Nikto, sqlmap, hydra/ssh brute force) |
| Virtualisation | Hébergement des VMs du lab | VMware |

> Environnement entièrement réalisé en réseau privé isolé (adressage 10.0.x.x) à des fins pédagogiques.

## 🔍 Scénarios réalisés

### 1. Reconnaissance & scan de vulnérabilités
- Scan de ports et services avec **Nmap** (détection OpenSSH, Apache, versions)
- Audit de sécurité web avec **Nikto** (headers manquants, fichiers exposés)
- Détection par Suricata des règles **ET EXPLOIT** correspondant à des CVE connues (Fortinet SSL VPN, Cisco RV320, D-Link, etc.)

### 2. Attaque par force brute (Web – DVWA)
- Simulation de tentatives de connexion répétées sur le formulaire de login DVWA
- Détection par Wazuh : règle personnalisée *"Brute Force DVWA detected – multiple logon failures"* (niveau 14)
- Cartographie MITRE ATT&CK : **T1110 – Brute Force**

### 3. Attaque par force brute SSH
- Tentatives de connexion SSH répétées (compte root) depuis la machine attaquante
- Détection croisée : logs `sshd`/PAM (authentication failed) + alertes Suricata (SSH invalid banner)
- Cartographie MITRE ATT&CK : **T1110.001 – Password Guessing**, **T1021.004 – Remote Services: SSH**

### 4. Injection SQL
- Exploitation du module SQL Injection de DVWA avec **sqlmap** (union-based, boolean-based, time-based)
- Détection par la règle Wazuh *"SQL injection attempt"* (niveau 7) sur les logs Apache
- Cartographie MITRE ATT&CK : tactique **Initial Access / Exploit Public-Facing Application**

### 5. Attaque avancée multi-vecteurs
- Corrélation d'alertes sur plusieurs tactiques MITRE (Reconnaissance, Initial Access, Privilege Escalation, Defense Evasion, Discovery)
- Visualisation via le module MITRE ATT&CK du dashboard Wazuh (répartition par tactique, top techniques, timeline)

### 6. Défense & remédiation
- Mise en place de règles de **firewall FortiGate** bloquant explicitement la source malveillante (`Kali-Attacker`) entre les zones LAN/DMZ
- Configuration d'une **réponse active Wazuh** (`active-response` → `firewall-drop`) déclenchée automatiquement sur la règle de brute force (timeout 300s)
- Blocage complémentaire via `iptables` sur le serveur applicatif
- **Validation post-remédiation** : nouvelle tentative d'attaque → connexion refusée (`Connection timed out`), confirmant l'efficacité des contre-mesures

## 📊 Résultats

- Détection en temps réel de 5 familles d'attaques différentes
- Alertes corrélées et enrichies avec le contexte MITRE ATT&CK (tactique, technique, ID)
- Boucle complète **Détection → Analyse → Remédiation → Validation**
- Réduction du délai de réponse grâce à l'automatisation (active response Wazuh)

## 🖼️ Captures d'écran

Les captures illustrant chaque étape sont disponibles dans le dossier [`/images`](./images) :

| Dossier | Contenu |
|---|---|
| `01-infrastructure/` | Dashboard FortiGate, interfaces réseau, règles de politique |
| `02-suricata/` | Configuration Suricata (`suricata.yaml`), statut du service |
| `03-siem-wazuh/` | Dashboards Wazuh (Threat Hunting, vue d'ensemble, MITRE ATT&CK) |
| `04-attaques/` | Brute force web, brute force SSH, SQL injection, scan Nmap/Nikto |
| `05-defense/` | Règles FortiGate de blocage, réponse active Wazuh, tests post-remédiation |

## 🛠️ Compétences mobilisées

`Windows Server` `Linux` `FortiGate / FortiOS` `Suricata` `Wazuh` `SIEM` `IDS/IPS` `MITRE ATT&CK` `Pentesting (Nmap, Nikto, sqlmap)` `Analyse de logs` `Réponse à incident`

## ⚠️ Avertissement

Ce projet a été réalisé exclusivement dans un environnement de laboratoire isolé, à des fins pédagogiques et de montée en compétences en cybersécurité défensive. Les adresses IP, configurations et outils utilisés (DVWA, Kali Linux) sont destinés à un usage strictement académique et ne doivent jamais être déployés sur des systèmes de production ou exposés sur Internet.

## 👤 Auteur

**Fatou Faty** – Étudiante en MSc Cybersécurité, Cloud, Systèmes & Réseaux (ESTIAM)
📧 Fatou.faty@estiam.com
