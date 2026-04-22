# 🌐 Adressage IP — Guide Complet

> **Niveau :** Débutant → Intermédiaire  
> **Support :** [Présentation interactive](./presentation/index.html) (Reveal.js)

---

## 📋 Table des matières

1. [Qu'est-ce qu'une adresse IP ?](#1-quest-ce-quune-adresse-ip-)
2. [Structure d'une adresse IPv4](#2-structure-dune-adresse-ipv4)
3. [Représentation binaire complète](#3-représentation-binaire-complète)
4. [Masque de sous-réseau & CIDR](#4-masque-de-sous-réseau--cidr)
5. [Mécanique du masque (opération AND)](#5-mécanique-du-masque--opération-and)
6. [Table CIDR complète](#6-table-cidr-complète)
7. [Exemple concret de subnetting](#7-exemple-concret-de-subnetting)
8. [IPv4 vs IPv6](#8-ipv4-vs-ipv6)
9. [IP Publique vs IP Privée (RFC 1918)](#9-ip-publique-vs-ip-privée-rfc-1918)
10. [Adresses spéciales](#10-adresses-spéciales)
11. [Les classes d'adresses IP](#11-les-classes-dadresses-ip-système-legacy)
12. [DHCP vs IP Statique](#12-dhcp-vs-ip-statique)
13. [NAT — Comment Internet fonctionne](#13-nat--comment-internet-fonctionne)
14. [DNS — Le carnet d'adresses d'Internet](#14-dns--le-carnet-dadresses-dinternet)
15. [Ports & Services communs](#15-ports--services-communs)
16. [Commandes essentielles](#16-commandes-essentielles)

---

## 1. Qu'est-ce qu'une adresse IP ?

Une **adresse IP (Internet Protocol)** est un identifiant numérique unique attribué à chaque appareil connecté à un réseau (ordinateur, smartphone, imprimante, serveur, caméra IP…).

Elle permet de **router les paquets de données** d'un appareil à un autre.

> 💡 **Analogie :** Le réseau est une rue, l'adresse IP est le numéro de porte.

Il existe deux versions principales :

| Version | Taille | Exemple |
|---------|--------|---------|
| **IPv4** | 32 bits | `192.168.1.10` |
| **IPv6** | 128 bits | `2001:0db8:85a3::8a2e:0370:7334` |

---

## 2. Structure d'une adresse IPv4

Une adresse IPv4 est composée de **4 octets** (4 nombres de 0 à 255) séparés par des points.

```
192  .  168  .  1  .  10
[___réseau_____]   [hôte]
```

| Propriété | Valeur |
|-----------|--------|
| Nombre de bits | **32** |
| Nombre d'octets | **4** (groupes de 8 bits) |
| Adresses possibles | **≈ 4,3 milliards** |

Chaque octet fonctionne en **binaire** en interne, mais s'affiche en décimal pour la lisibilité humaine.

---

## 3. Représentation binaire complète

Exemple : `192.168.1.10` avec un masque `/24`

| Octet | Décimal | Binaire | Rôle |
|-------|---------|---------|------|
| 1er | **192** | `11000000` | Réseau |
| 2ème | **168** | `10101000` | Réseau |
| 3ème | **1** | `00000001` | Réseau |
| 4ème | **10** | `00001010` | Hôte |

```
11000000.10101000.00000001.00001010
[______24 bits réseau_____][8 bits hôte]
```

> 💡 Chaque bit vaut une puissance de 2 : **128, 64, 32, 16, 8, 4, 2, 1**  
> Exemple : 192 = 1×128 + 1×64 + 0×32 + 0×16 + 0×8 + 0×4 + 0×2 + 0×1

---

## 4. Masque de sous-réseau & CIDR

Le masque indique **jusqu'où va la partie réseau** dans une adresse IP.

```
IP :     192.168.1.10
Masque : 255.255.255.0
```

- **Partie réseau :** `192.168.1`
- **Partie hôte :** `.10`

### Notation CIDR

Au lieu d'écrire le masque en décimal, on utilise `/n` où `n` = nombre de bits réseau.

```
192.168.1.10/24   →   255.255.255.0   →   254 hôtes
```

**Formule hôtes :** `2^(32 - préfixe) - 2`  
*(On soustrait l'adresse réseau et le broadcast)*

---

## 5. Mécanique du masque — Opération AND

Pour savoir si deux machines sont dans le même réseau, le routeur effectue une opération **ET (AND) bit à bit** entre l'IP et le masque.

```
IP     : 11000000.10101000.00000001.00001010  (192.168.1.10)
Masque : 11111111.11111111.11111111.00000000  (255.255.255.0 = /24)
AND    : 11000000.10101000.00000001.00000000  → 192.168.1.0
```

> ⚡ Si deux machines ont le même résultat AND, elles sont **voisines** (même réseau).

---

## 6. Table CIDR complète

| Préfixe | Masque | Hôtes utilisables | Usage typique |
|---------|--------|-------------------|---------------|
| `/8` | 255.0.0.0 | 16 777 214 | Grandes entreprises (Classe A) |
| `/16` | 255.255.0.0 | 65 534 | Moyennes entreprises (Classe B) |
| `/24` | 255.255.255.0 | 254 | LAN standard (Classe C) |
| `/25` | 255.255.255.128 | 126 | Département moyen |
| `/26` | 255.255.255.192 | 62 | Petit département |
| `/27` | 255.255.255.224 | 30 | Petite équipe |
| `/28` | 255.255.255.240 | 14 | Très petit groupe |
| `/29` | 255.255.255.248 | 6 | Liaison inter-routeurs |
| `/30` | 255.255.255.252 | 2 | Lien point-à-point |

---

## 7. Exemple concret de Subnetting

**Scénario :** Une entreprise possède `192.168.1.0/24` et veut le diviser en **4 sous-réseaux**.

**Solution :** Utiliser un `/26` → 4 sous-réseaux de **62 hôtes** chacun.

| Sous-réseau | Adresse réseau | Plage hôtes | Broadcast |
|-------------|---------------|-------------|-----------|
| SR 1 | `192.168.1.0/26` | `.1` → `.62` | `192.168.1.63` |
| SR 2 | `192.168.1.64/26` | `.65` → `.126` | `192.168.1.127` |
| SR 3 | `192.168.1.128/26` | `.129` → `.190` | `192.168.1.191` |
| SR 4 | `192.168.1.192/26` | `.193` → `.254` | `192.168.1.255` |

> ⚠️ **Règle :** Adresse réseau + broadcast = **2 adresses perdues** par sous-réseau → d'où `2ⁿ − 2` hôtes.

---

## 8. IPv4 vs IPv6

| Propriété | IPv4 | IPv6 |
|-----------|------|------|
| Taille | 32 bits | 128 bits |
| Format | Décimal pointé | Hexadécimal |
| Adresses | ≈ 4,3 milliards | 3,4 × 10³⁸ |
| Exemple | `192.168.1.1` | `2001:db8::1` |
| Statut | Le plus utilisé — pénurie depuis ~2011 | Conçu pour remplacer IPv4 |
| Sécurité | Optionnelle | IPsec intégré |

> 🚀 IPv6 offre assez d'adresses pour attribuer **670 quadrillions** d'IPs par mm² de la Terre !

---

## 9. IP Publique vs IP Privée (RFC 1918)

```
PC (192.168.1.10) → Routeur NAT → Internet (82.64.12.45)
[  réseau privé  ]              [   IP publique    ]
```

### Plages privées (RFC 1918) — non routables sur Internet

| Plage | Masque typique | Usage |
|-------|----------------|-------|
| `10.0.0.0 – 10.255.255.255` | `/8` | Grandes entreprises |
| `172.16.0.0 – 172.31.255.255` | `/12` | Moyennes entreprises |
| `192.168.0.0 – 192.168.255.255` | `/16` | Réseaux domestiques / PME |

### IP Publique
- Attribuée par votre **FAI** (Free, Orange…)
- Unique sur toute l'internet mondiale
- Peut changer (IP dynamique FAI)

```bash
# Trouver son IP publique
curl ifconfig.me
```

---

## 10. Adresses spéciales

| Adresse | Nom | Rôle |
|---------|-----|------|
| `127.0.0.1` | Loopback | L'ordinateur lui-même ("localhost") |
| `0.0.0.0` | Wildcard | Toutes les interfaces disponibles |
| `192.168.1.255` | Broadcast | Message à TOUS les hôtes du réseau |
| `192.168.1.0` | Adresse réseau | Identifie le réseau (non assignable) |
| `169.254.x.x` | APIPA | Attribution auto quand le DHCP échoue |

> 💡 **Astuce :** `ping 127.0.0.1` — si ça répond, votre carte réseau est fonctionnelle, même sans connexion.

---

## 11. Les classes d'adresses IP (Système Legacy)

> ⚠️ Système dépassé — remplacé par CIDR. Encore enseigné en formation.

| Classe | 1er bit | Plage | Hôtes max | Usage |
|--------|---------|-------|-----------|-------|
| **A** | `0xxxxxxx` | 1.0.0.0 → 126.x.x.x | 16 millions | Très grands réseaux |
| **B** | `10xxxxxx` | 128.0.0.0 → 191.x.x.x | 65 534 | Réseaux moyens |
| **C** | `110xxxxx` | 192.0.0.0 → 223.x.x.x | 254 | Petits réseaux (LAN) |
| **D** | `1110xxxx` | 224.0.0.0 → 239.x.x.x | — | Multicast |
| **E** | `1111xxxx` | 240.0.0.0 → 255.x.x.x | — | Réservé (expérimental) |

---

## 12. DHCP vs IP Statique

| | DHCP (dynamique) | IP Statique |
|-|-----------------|-------------|
| Configuration | Automatique (routeur) | Manuelle (admin) |
| Stabilité | IP peut changer | IP fixe et prévisible |
| Idéal pour | PC, smartphone, tablette | Serveurs, imprimantes, caméras |

```bash
# Windows — Renouveler son IP DHCP
ipconfig /release
ipconfig /renew
```

```bash
# Linux — Configurer une IP statique
ip addr add 192.168.1.10/24 dev eth0
```

> 💡 Un serveur web **doit** avoir une IP statique — sinon les utilisateurs ne pourraient jamais le retrouver !

---

## 13. NAT — Comment Internet fonctionne

**NAT (Network Address Translation)** est le traducteur entre votre réseau privé et Internet.

```
💻 192.168.1.10 ─┐
📱 192.168.1.11 ──┤── 🔀 Routeur NAT ──── 🌍 82.64.12.45 (1 seule IP !)
🖨️ 192.168.1.12 ─┘
[  Réseau local  ]                      [   Internet   ]
```

> 🎯 Grâce au NAT, **tous vos appareils partagent une seule IP publique**.  
> C'est pourquoi les IPs privées peuvent se répéter dans des milliers de maisons différentes.

---

## 14. DNS — Le carnet d'adresses d'Internet

**DNS (Domain Name System)** traduit les noms lisibles en adresses IP.

```
Vous tapez "google.com"
       ↓
Serveur DNS cherche...
       ↓
Répond : 142.250.74.46
       ↓
Votre PC se connecte ✅
```

### DNS populaires

| Adresse | Fournisseur |
|---------|-------------|
| `8.8.8.8` | Google DNS |
| `1.1.1.1` | Cloudflare DNS |
| `9.9.9.9` | Quad9 DNS |

> 🧠 Sans DNS, il faudrait mémoriser l'IP de chaque site web.

---

## 15. Ports & Services communs

L'adresse IP trouve la **machine**, mais le port trouve l'**application**.

> 💡 L'IP = adresse de l'immeuble — le port = numéro d'appartement.

| Protocole | Port | Usage |
|-----------|------|-------|
| HTTP | `80` | Web non sécurisé |
| HTTPS | `443` | Web sécurisé (TLS) |
| DNS | `53` | Résolution de noms |
| SSH | `22` | Accès distant sécurisé |
| FTP | `21` | Transfert de fichiers |
| SMTP | `25` | Envoi d'e-mails |

---

## 16. Commandes essentielles

### 🪟 Windows

```powershell
# Voir toutes les interfaces réseau
ipconfig /all

# Tester la connectivité
ping 8.8.8.8

# Renouveler l'IP DHCP
ipconfig /release
ipconfig /renew

# Trouver l'IP publique
curl ifconfig.me
```

### 🐧 Linux / macOS

```bash
# Voir les interfaces réseau
ip addr show
# ou
ifconfig

# Tester la connectivité
ping -c 4 8.8.8.8

# Voir la table de routage
ip route show

# Configurer une IP statique
ip addr add 192.168.1.10/24 dev eth0
```

---

## 🏁 Résumé — Les essentiels

| Concept | Rôle |
|---------|------|
| **IP** | Votre nom sur le réseau |
| **Masque / CIDR** | Découpe réseau ↔ hôte |
| **Ports** | Votre porte d'entrée par application |
| **DNS** | Votre annuaire (nom → IP) |
| **NAT** | Votre bouclier & partage d'IP publique |
| **DHCP** | Distribution automatique d'IP |

---

*Présentation interactive : [`./presentation/index.html`](./presentation/index.html)*
