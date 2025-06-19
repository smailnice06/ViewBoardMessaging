# 🔐 Projet : Messagerie & Partage de fichiers P2P sécurisée sans fil

Ce projet propose une application de **communication sécurisée pair-à-pair (P2P)** entre des dispositifs tels que **Raspberry Pi** et **ViewBoard (ViewSonic)**. Les échanges se font **directement entre les utilisateurs via un protocole réseau sans fil personnalisé**, sans stockage centralisé, tout en garantissant **la confidentialité grâce au chiffrement RSA**.

---

## 🚀 Objectifs du projet

- Mettre en place un **système de messagerie et de transfert de fichiers** entre des écrans ViewBoard et Raspberry Pi.
- Utiliser un **serveur Flask léger** uniquement pour :
  - Authentification
  - Gestion des amis
  - Récupération d'adresse IP
- Implémenter un **protocole sans fil basé sur TCP/IP** (via sockets), pour échanger :
  - Des **messages textuels chiffrés**
  - Des **fichiers** (vidéos, images, documents)

---

## 🧠 Architecture du système

```
+------------------+           +------------------+           +------------------+
|     Utilisateur  | <--TCP--> |  Raspberry Pi    | <--TCP--> |    ViewBoard     |
| (Interface Web)  |           | (Client P2P)     |           | (Client P2P)     |
+------------------+           +------------------+           +------------------+
       |                               |                               |
       |--------- Requête ------------>|                               |
       |          d'IP                 |                               |
       |                               |<--------- Socket TCP -------->|
       |                               |   (Communication directe)     |
       |                               |                               |
       |<-- Interface Web (Flask) ---->|                               |
```
---

## 🧩 Diagramme de Blocs SYSML (Structure)

```
+-----------------------------------------------------+
|                 [Application Web Flask]             |
|-----------------------------------------------------|
| - Interface utilisateur (HTML/CSS/JS)               |
| - Communication avec Flask backend                  |
| - Choix de l’ami, envoi et réception de messages    |
| - Communication avec Raspberry Pi local             |
+-----------------------------------------------------+
               |             ^
               | REST API    | Socket TCP
               v             |
+-----------------------------------------------------+
|                  [Serveur Central Flask]            |
|-----------------------------------------------------|
| - Authentification (/register, /login)              |
| - Gestion des amis (/friend-request, /friends)      |
| - Stockage IP publique et présence                  |
| - Base de données SQLite                           |
+-----------------------------------------------------+
               ^             |
               | HTTP        | JSON
               v             |
+-----------------------------------------------------+
|          [Client P2P sur Raspberry Pi]              |
|-----------------------------------------------------|
| - Récupération IP publique                          |
| - Enregistrement / Connexion à Flask                |
| - Communication directe P2P via sockets TCP         |
| - Chiffrement RSA (clé publique/privée)             |
| - Réception/Envoi de fichiers et messages           |
+-----------------------------------------------------+
               |             ^
               | Socket TCP  | Clés RSA + Fichiers
               v             |
+-----------------------------------------------------+
|              [Client P2P distant (ViewBoard)]       |
|-----------------------------------------------------|
| - Même code Python tournant sur ViewBoard           |
| - Interface web via navigateur ViewBoard            |
| - Dialogue direct avec Raspberry Pi                 |
+-----------------------------------------------------+



```

---

## 🎯 Diagramme de cas d’utilisation SYSML (Use Case Diagram)

```
                           [Utilisateur]
                                |
               +----------------+----------------+
               |                                 |
     (1) S'enregistrer                     (2) Se connecter
               |                                 |
               +---------------+-----------------+
                               |
                       [Serveur Flask]
                               |
           +------------------+-------------------+
           |                                      |
 (3) Ajouter un ami                     (4) Lister mes amis
           |                                      |
           +------------------+-------------------+
                               |
                        (5) Obtenir l'IP d'un ami
                               |
                            [P2P System]
           +------------------+-------------------+
           |                                      |
(6) Échanger des messages            (7) Échanger des fichiers
       chiffrés (RSA)                     (vidéos/docs)
           |                                      |
       [Raspberry Pi]                    [ViewBoard distant]



```

---

##🧭 Diagramme de Séquence SYSML – Scénario d’un échange P2P

```
User A         Serveur Flask             User B
  |                  |                      |
  |---- /register -->|                      |
  |<---   OK      ----                      |
  |---- /login ------>                      |
  |<---  OK/IP saved--                      |
  |                                         |
  |---- /friend-request ->                  |
  |<--   "Demande envoyée"                  |
  |                                         |
  |                  <--- /register ------- |
  |                  ----->  OK             |
  |                  <--- /login ---------- |
  |                  ----->  OK             |
  |<-- /friend-request (croisée auto acceptée)
  |                                         |
  |---- /get-friend-ip(B) -->               |
  |<---  IP: 192.168.1.47                    |
  |                                         |
  |============= Socket TCP ===============>|
  |          Connexion directe P2P          |
  |          Échange de clés RSA            |
  |<============= Socket TCP ==============|
  |                                         |
  |==== Message chiffré (RSA + signature) ==>|
  |<=== Réponse chiffrée ===================|
  |                                         |


```


---
## 🧩 Fonctionnalités

- 🔐 **Chiffrement RSA** (2048 bits) des messages
- 🔄 **Échange direct P2P** sans serveur intermédiaire
- 🌐 **Serveur Flask** pour :
  - Authentification / inscription
  - Gestion des amis
  - Distribution des adresses IP
- 📡 **Connexion réseau sans fil** via TCP/IP
- 🖥️ Interface Web Flask installable sur ViewBoard

---

## 📁 Structure du projet

```
Secure-P2P-Messaging/
├── app.py              # Application principale Flask
├── utils.py            # Fonctions de base de données et logique utilisateur
├── templates/
│   └── index.html      # Interface utilisateur HTML
├── static/             # Fichiers CSS/JS si nécessaire
├── rsa_utils.py        # Génération et manipulation des clés RSA
├── p2p_client.py       # Client P2P : envoi de messages/fichiers
├── p2p_server.py       # Serveur P2P : réception de messages/fichiers
├── connectedusers.db   # Base SQLite pour le serveur Flask
└── README.md           # Documentation
```

---

## 🛠️ Technologies utilisées

- Python 3
- Flask (API REST)
- Sockets TCP
- Cryptography (librairie RSA)
- SQLite
- HTML/CSS (Flask templates)
- Raspberry Pi OS
- ViewBoard avec navigateur intégré

---

## 👥 Répartition des tâches

### Smaïl — Chef de projet & développeur back-end
- Architecture sécurisée RSA + sockets TCP
- Développement du serveur Flask
- Intégration complète du protocole sécurisé
- Rédaction de la documentation technique

### Othmane — Développeur front-end web
- Création de l’interface utilisateur Flask
- Intégration du menu, formulaire et messagerie web
- Tests de compatibilité sur ViewBoard

### Oussama — Responsable Raspberry Pi & réseau
- Configuration des Raspberry Pi
- Déploiement du client P2P
- Tests réseau sans fil ViewBoard <-> Pi
- Utilisation possible de Samba pour transfert fichiers

---

## 🧪 Cas d’usage typique

1. L’utilisateur s’enregistre via l’interface web.
2. Il ajoute un ami.
3. Le Raspberry Pi ou ViewBoard récupère l’adresse IP de l’ami depuis le serveur Flask.
4. Une connexion TCP P2P est initiée entre les deux clients.
5. Les messages ou fichiers sont échangés **de façon chiffrée** sans transiter par le serveur.

---

## 🔒 Sécurité

- **RSA 2048 bits** pour chiffrer les messages
- **Signature numérique** pour garantir l’intégrité
- **Aucune donnée sensible stockée** sur le serveur (zéro message, zéro fichier)

---

## 📡 Protocole réseau sans fil

Le système repose sur une **connexion TCP/IP sans fil personnalisée**, utilisant des sockets entre Raspberry Pi et ViewBoard, sur le même réseau Wi-Fi local ou VPN :

- Pas besoin d’Internet après la phase de récupération d’IP
- Les messages/fichiers sont transmis de manière directe
- Extensible à d’autres types de contenus (audio, PDF, etc.)

---

## 🧾 Licence

Ce projet est librement utilisable à des fins éducatives et personnelles.  
**Il est interdit de le commercialiser ou de le revendre sans autorisation.**

---

## 📞 Contact

Pour toute question ou suggestion, contactez l’équipe :  
- Smaïl Mehbali  
- Othmane  
- Oussama
