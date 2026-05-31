# 🛡️ Portfolio — Réseau & Cybersécurité

![Status](https://img.shields.io/badge/Status-En_cours-brightgreen)
![NetAcad](https://img.shields.io/badge/Cisco_NetAcad-Networking_Basics_✅-blue)
![CCNA](https://img.shields.io/badge/CCNA_200--301-En_préparation-orange)
![Python](https://img.shields.io/badge/Python-Initiation_✅-yellow)

> **Cosme OBOLI** — Analyste Développeur & Support IT | Formation Réseau & Cybersécurité  
> 📍 Bénin | 🎯 Objectif : Master Cybersécurité — Paris 2027

---

## 👤 À propos

Titulaire d'une **Licence en Systèmes Informatiques et Logiciels** (mention Très Bien — Gasa-Formation, Bénin), je mène depuis février 2026 un programme intensif d'auto-formation en réseaux et cybersécurité, en parallèle de mon activité professionnelle au Cabinet d'Étude DOSSOU (CED) où je participe à des projets d'installation, d'administration et de maintenance réseau.

Ce repository documente mes apprentissages, projets pratiques et progressions dans le cadre d'une feuille de route de 12 mois (Février 2026 – Janvier 2027).

---

## 🏅 Certifications & Badges

| Badge / Certification | Organisme | Statut | Date |
|---|---|---|---|
| **Networking Basics** | Cisco NetAcad | ✅ Obtenu | Mai 2026 |
| **Network Technician Career Path** | Cisco NetAcad | 🔄 En cours | 2026 |
| **CCNA 200-301** | Cisco / Pearson VUE | ⏳ Préparation | Janvier 2027 |
| **CompTIA Security+** | CompTIA | ⏳ Vision | 2027 |

---

## 🧪 Lab Virtuel — Infrastructure

### Environnement de travail

```
Machine hôte : Windows 11 (16 Go RAM)
Hyperviseur   : VirtualBox
```

### Architecture du lab

```
┌─────────────────────────────────────────────────┐
│                  LAB VIRTUEL                     │
│                                                  │
│  ┌──────────┐    ┌──────────┐    ┌───────────┐  │
│  │ Parrot OS │    │  Ubuntu  │    │ Windows   │  │
│  │ Security  │    │  Server  │    │    10     │  │
│  │(Attaquant)│    │(Serveur) │    │ (Client)  │  │
│  └────┬─────┘    └────┬─────┘    └─────┬─────┘  │
│       │               │                │         │
│       └───────────────┴────────────────┘         │
│              Réseau Interne : lab-network         │
│              192.168.56.0/24                      │
│                                                  │
│  Parrot OS  → 192.168.56.20                      │
│  Ubuntu Srv → 192.168.56.10 (IP statique)        │
└─────────────────────────────────────────────────┘
```

### Configuration réseau
- **Adaptateur 1** : NAT (accès internet)
- **Adaptateur 2** : Réseau interne `lab-network` (communication inter-VMs)
- **IP statique Ubuntu Server** : configurée via Netplan (`/etc/netplan/50-cloud-init.yaml`)

---

## 📁 Projets Réalisés

---

### 🔵 Projet 1 — Lab VirtualBox Multi-VM
**Date :** Février 2026 | **Difficulté :** ⭐

**Objectif :** Créer un environnement d'apprentissage isolé avec 3 VMs communicantes.

**Réalisations :**
- Installation de Parrot OS Security (attaquant)
- Installation d'Ubuntu Server 24.04 LTS (serveur cible)
- Installation de Windows 10 (client)
- Configuration du réseau interne `lab-network`
- Test de communication inter-VMs (ping)

**Technologies :** VirtualBox, Parrot OS, Ubuntu Server, Windows 10

---

### 🔵 Projet 2 — Subnetting & Adressage IP
**Date :** Mars 2026 | **Difficulté :** ⭐⭐

**Objectif :** Maîtriser le découpage en sous-réseaux IPv4.

**Compétences acquises :**
- Calcul de masques de sous-réseaux (/24, /26, /27, /28...)
- Découpage d'un réseau en sous-réseaux multiples
- Identification : adresse réseau, broadcast, plage d'hôtes
- Subnetting sur classes A, B et C

**Exemple pratique :**
```
Réseau : 192.168.50.0/24 → 8 sous-réseaux /27
Subnet 1 : 192.168.50.0/27   | Hôtes : .1 → .30  | Broadcast : .31
Subnet 2 : 192.168.50.32/27  | Hôtes : .33 → .62 | Broadcast : .63
Subnet 3 : 192.168.50.64/27  | Hôtes : .65 → .94 | Broadcast : .95
...
```

---

### 🔵 Projet 3 — Configuration Interfaces Réseau Linux
**Date :** Mars 2026 | **Difficulté :** ⭐⭐

**Objectif :** Configurer des interfaces réseau statiques sur Ubuntu Server.

**Réalisations :**
```bash
# Configuration via Netplan (permanente)
# /etc/netplan/50-cloud-init.yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      addresses:
        - 192.168.56.10/24

# Appliquer la configuration
sudo netplan apply

# Configuration temporaire Parrot OS
sudo ip addr add 192.168.56.20/24 dev enp0s8
sudo ip link set enp0s8 up
```

---

### 🔵 Projet 4 — Analyse Trafic avec Wireshark
**Date :** Mars 2026 | **Difficulté :** ⭐⭐

**Objectif :** Capturer et analyser le trafic réseau entre les VMs.

**Réalisations :**
- Installation et configuration de Wireshark sur Parrot OS
- Capture du trafic ICMP (ping) entre Parrot et Ubuntu Server
- Analyse des paquets : ICMP request/reply, protocole ARP
- Application de filtres : `icmp`, `arp`, `tcp`

**Observations clés :**
- Paquets ICMP : Parrot (192.168.56.20) → Ubuntu (192.168.56.10)
- Paquets ARP : résolution MAC/IP visible en temps réel
- Structure OSI visible couche par couche (Ethernet II → IPv4 → ICMP)

---

### 🔵 Projet 5 — Routage Statique (Cisco Packet Tracer)
**Date :** Avril 2026 | **Difficulté :** ⭐⭐

**Objectif :** Faire communiquer 3 réseaux distincts via 2 routeurs.

**Topologie :**
```
PC0 ──── Router0 ──────── Router1 ──── PC1
     .10    .1   .1    .2   .1     .10

192.168.1.0/24  10.0.0.0/30  192.168.2.0/24
```

**Configuration Router0 :**
```
interface GigabitEthernet0/0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown

interface GigabitEthernet0/0/1
 ip address 10.0.0.1 255.255.255.252
 no shutdown

ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

**Résultat :** PC0 (192.168.1.10) communique avec PC1 (192.168.2.10) ✅

---

### 🔵 Projet 6 — VLAN & Inter-VLAN Routing
**Date :** Avril 2026 | **Difficulté :** ⭐⭐⭐

**Objectif :** Segmenter un réseau en VLANs et configurer le routage inter-VLAN.

**Topologie :**
```
PC4 (VLAN 10) ──┐
PC2 (VLAN 10) ──┤── Switch 2960 ──── Router (Router-on-a-stick)
PC5 (VLAN 20) ──┤
PC3 (VLAN 20) ──┘
```

**Configuration Switch :**
```
vlan 10
 name COMPTABILITE
vlan 20
 name IT

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10

interface FastEthernet0/5
 switchport mode trunk
```

**Configuration Router (Router-on-a-stick) :**
```
interface GigabitEthernet0/0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

**Résultat :** VLAN 10 communique avec VLAN 20 via le routeur ✅

---

### 🔵 Projet 7 — Serveur DHCP Cisco
**Date :** Avril 2026 | **Difficulté :** ⭐

**Objectif :** Configurer un routeur Cisco comme serveur DHCP.

```
ip dhcp pool MONRESEAU
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8

ip dhcp excluded-address 192.168.1.1
```

**Résultat :** 3 PCs obtiennent automatiquement une IP dans 192.168.1.0/24 ✅

---

### 🔵 Projet 8 — Sécurisation Ubuntu Server
**Date :** Mai 2026 | **Difficulté :** ⭐⭐

**Objectif :** Mettre en place une sécurité de base sur Ubuntu Server.

#### 8a — Firewall UFW
```bash
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow from 192.168.56.0/24
sudo ufw deny from 192.168.56.99
sudo ufw status numbered
```

**Politique par défaut :**
- Entrant : DENY (tout bloqué par défaut)
- Sortant : ALLOW

#### 8b — SSH sécurisé avec clé RSA 4096
```bash
# Génération de la paire de clés (sur Parrot OS)
ssh-keygen -t rsa -b 4096

# Copie de la clé publique vers Ubuntu Server
ssh-copy-id cosme@192.168.56.10

# Connexion sans mot de passe
ssh cosme@192.168.56.10
```

**Résultat :** Connexion SSH depuis Parrot vers Ubuntu sans mot de passe ✅

---

### 🔵 Projet 9 — VPN WireGuard
**Date :** Mai 2026 | **Difficulté :** ⭐⭐

**Objectif :** Installer et configurer un serveur VPN WireGuard sur Ubuntu Server.

```bash
# Installation
sudo apt install wireguard -y

# Génération des clés
sudo bash -c "wg genkey | tee /etc/wireguard/privatekey | wg pubkey > /etc/wireguard/publickey"

# Configuration wg0.conf
[Interface]
PrivateKey = <clé_privée>
Address = 10.0.0.1/24
ListenPort = 51820

# Démarrage
sudo wg-quick up wg0
sudo systemctl enable wg-quick@wg0
```

**Résultat :** Serveur VPN actif sur le port 51820 ✅

---

### 🔵 Projet 10 — Infrastructure PME (Packet Tracer)
**Date :** Mai 2026 | **Difficulté :** ⭐⭐⭐

**Objectif :** Simuler une vraie infrastructure d'entreprise complète.

**Composants déployés :**
- 📞 Téléphonie IP via CME (Cisco Unified CME)
- 🖥️ Réseau filaire pour les PCs
- 📶 Wi-Fi pour les appareils mobiles
- 🖨️ Impression réseau partagée

---

## 💻 Commandes Linux Maîtrisées

```bash
# Navigation et fichiers
ls, ls -la       # Lister les fichiers (avec permissions)
cd               # Changer de répertoire
grep             # Rechercher dans les fichiers
nano             # Éditeur de texte

# Permissions
chmod +x file    # Rendre exécutable
chmod 755 file   # Permissions numériques

# Services
systemctl status ssh      # Vérifier l'état d'un service
systemctl start ssh       # Démarrer un service
systemctl enable ssh      # Activer au démarrage

# Réseau
ip a                      # Voir les interfaces
ip addr add X.X.X.X/24 dev enp0s8  # Assigner une IP
ping X.X.X.X              # Tester la connectivité
ss -tuln                  # Voir les ports ouverts
nslookup google.com       # Test DNS

# Firewall
sudo ufw enable/disable
sudo ufw allow/deny
sudo ufw status numbered
```

---

## 🐍 Python — Progression

### Bases maîtrisées (Mai 2026)
```python
# Variables et types
nombre = 42          # int
nom = "Cosme"        # str
pi = 3.14            # float
actif = True         # bool

# Structures de données
liste = [1, 2, 3]
dico = {"clé": "valeur"}

# Conditions
if nombre > 0:
    print("Positif")
elif nombre < 0:
    print("Négatif")
else:
    print("Zéro")

# Boucles
for x in range(1, 11):
    if x % 3 == 0:
        print("Fizz")
    else:
        print(x)

# Fonctions avec gestion d'erreurs
def verifier_nombre():
    try:
        n = int(input("Entrez un nombre : "))
        return n
    except ValueError:
        print("Erreur : pas un nombre valide")
```

---

## 📅 Feuille de Route — Progression

```
✅ Février 2026  — Lab VirtualBox + Bases réseau
✅ Mars 2026     — Adressage IP + Subnetting + Wireshark
✅ Avril 2026    — Routage statique + VLAN + DHCP
✅ Mai 2026      — Sécurité réseau + SSH + VPN + Python bases
🔄 Juin 2026    — Introduction Pentesting (TryHackMe, nmap)
⏳ Juillet 2026  — Linux Avancé & Hardening
⏳ Août 2026     — Python pour la sécurité
⏳ Septembre 2026 — Monitoring & SIEM (Wazuh)
⏳ Octobre 2026  — Révision CCNA intensive
⏳ Novembre 2026 — Portfolio & Visibilité
⏳ Décembre 2026 — Examens blancs CCNA
⏳ Janvier 2027  — 🏆 Certification CCNA 200-301
```

---

## 📊 Statistiques d'apprentissage

| Mois | Heures | Sessions | Points clés |
|---|---|---|---|
| Novembre 2025 | - | 1 | Plan réseau PME Packet Tracer |
| Février 2026 | ~2h | 3 | Lab VirtualBox, IP/DNS/TCP/UDP |
| Mars 2026 | ~12.5h | 6 | Subnetting, Linux, Wireshark |
| Avril 2026 | ~2h | 3 | Routage, VLAN, DHCP |
| Mai 2026 | ~13h | 9 | Sécurité, SSH, VPN, Python, Badge Cisco |
| **Total** | **~30h** | **22** | |

---

## 🛠️ Stack Technique

### Réseau & Sécurité
![Cisco](https://img.shields.io/badge/Cisco_Packet_Tracer-Maîtrisé-blue)
![Wireshark](https://img.shields.io/badge/Wireshark-Maîtrisé-blue)
![WireGuard](https://img.shields.io/badge/WireGuard_VPN-Configuré-green)
![UFW](https://img.shields.io/badge/UFW_Firewall-Configuré-green)

### Systèmes & Virtualisation
![Linux](https://img.shields.io/badge/Linux_Ubuntu-Pratique-orange)
![Parrot](https://img.shields.io/badge/Parrot_OS-Lab-orange)
![VirtualBox](https://img.shields.io/badge/VirtualBox-Maîtrisé-blue)

### Programmation
![Python](https://img.shields.io/badge/Python-Initiation-yellow)

### Outils Cisco
![NetAcad](https://img.shields.io/badge/Cisco_NetAcad-En_cours-blue)
![PacketTracer](https://img.shields.io/badge/Packet_Tracer-Pratique-blue)

---

## 🎯 Objectifs 2027

- 🏆 **Certification CCNA 200-301** — Janvier 2027
- 🛡️ **CompTIA Security+** — 2027
- 🎓 **Master Cybersécurité** — Paris, France — Septembre 2027
- 📁 **Portfolio GitHub complet** — Novembre 2026

---

## 📬 Contact

- 📧 cosmeoboli0@gmail.com
- 📱 +229 52 83 10 71
- 💼 Cabinet d'Étude DOSSOU (CED) — Bénin

---

*Dernière mise à jour : Mai 2026*
