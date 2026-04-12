# Cahier des charges fonctionnel - Application de messagerie instantanée (version avancée)

## 1. Vue d’ensemble

L’application permet à plusieurs utilisateurs de communiquer entre eux via :

- Messages texte (temps réel et différé)
- Appels audio (directs entre deux clients)
- Appels vidéo (directs entre deux clients)
- Création et gestion de groupes

### Particularité importante

Un utilisateur peut :

- Recevoir des messages
- Être ajouté à un groupe
- Recevoir une tentative d’appel

👉 Même s’il n’est pas connecté, à condition qu’il se soit déjà connecté au serveur au moins une fois.

Le serveur conserve :
- Son adresse IP
- Son pseudo
- Son historique (messages, invitations, appels manqués)

---

## 1.1 Architecture générale

- Un **serveur central multithread** :
  - Gère les connexions
  - Associe **adresse IP ↔ pseudo**
  - Stocke les messages hors ligne
  - Gère les groupes
  - Gère la signalisation des appels

- Les **clients** :
  - Communiquent avec le serveur pour :
    - Messages
    - Groupes
    - Signalisation d’appel
  - Échangent directement les flux audio/vidéo entre eux

---

## 2. Connexion d’un utilisateur

### 2.1 Première utilisation

Lors de la première connexion :

- Le serveur identifie automatiquement l’utilisateur via son **adresse IP**
- L’utilisateur doit saisir un **pseudo**

**Vérification :**
- Si le pseudo est disponible → accepté
- Sinon → le système demande un autre pseudo

👉 Une fois validé :
- L’utilisateur est enregistré avec :
  - adresse IP
  - pseudo

---

### 2.2 Connexions suivantes

- L’utilisateur est automatiquement reconnu via son **adresse IP**
- Il n’a pas besoin de ressaisir son pseudo

👉 À la connexion :
- Le serveur restaure :
  - Les messages non lus
  - Les invitations de groupe
  - Les appels manqués

---

## 3. Gestion des utilisateurs

### 3.1 Identification

Chaque utilisateur est défini par :

- **Adresse IP (clé principale)**  
- **Pseudo (affichage utilisateur)**  

---

### 3.2 Types d’utilisateurs

- **Connectés**
- **Hors ligne (connus du serveur)**

---

### 3.3 Affichage côté client

- Liste des utilisateurs avec :
  - 🟢 En ligne
  - ⚫ Hors ligne

---

## 4. Fonctionnalités entre clients

---

## 4.1 Envoi de message texte

**Déclencheur :** L’utilisateur sélectionne un contact ou un groupe et envoie un message.

**Comportement :**

- Le message est envoyé au serveur
- Si le destinataire est connecté → réception immédiate
- Sinon → message stocké puis livré à la reconnexion

---

## 4.2 Réception des messages

- Réception instantanée si connecté
- Réception différée sinon

- L’affichage est automatique sans action utilisateur

---

## 4.3 Création de groupe

**Déclencheur :** L’utilisateur clique sur « Créer groupe »

**Comportement :**

- Sélection des membres (connectés ou non)
- Création du groupe côté serveur
- Enregistrement des membres

- Les utilisateurs hors ligne recevront l’invitation à leur prochaine connexion

---

## 4.4 Messagerie de groupe

- Les messages sont envoyés au serveur
- Distribués à tous les membres

- Membres connectés → réception immédiate  
- Membres hors ligne → stockage puis livraison ultérieure

---

## 4.5 Appel audio

**Déclencheur :** L’utilisateur sélectionne un contact et lance un appel audio.

**Comportement :**

- Une demande d’appel est envoyée via le serveur
- Si le destinataire est connecté :
  - Une notification s’affiche avec options *Accepter / Refuser*
  - En cas d’acceptation :
    - Un flux audio direct est établi entre les deux clients
- Si le destinataire est hors ligne :
  - L’appel est enregistré comme **appel manqué**
  - Le destinataire sera notifié à sa prochaine connexion

---

## 4.6 Appel vidéo

**Déclencheur :** L’utilisateur sélectionne un contact et lance un appel vidéo.

**Comportement :**

- Une demande d’appel vidéo est envoyée via le serveur

- Si le destinataire est connecté :
  - Il reçoit une notification avec options *Accepter / Refuser*
  - En cas d’acceptation :
    - Un flux audio et vidéo direct est établi
    - Les deux utilisateurs se voient en temps réel

- Si le destinataire est hors ligne :
  - Une **tentative d’appel vidéo est enregistrée**
  - Le destinataire verra à sa reconnexion une notification indiquant :
    👉 *« X a tenté de vous appeler en vidéo »*, simulant le comportement des appels manqués des applications de messagerie modernes

---

## 4.7 Liste des utilisateurs

- Mise à jour automatique
- Affichage des utilisateurs connectés et hors ligne

---

## 4.8 Déconnexion

- L’utilisateur devient hors ligne
- Son état est conservé côté serveur

---

## 5. Résumé des fonctionnalités

| Fonctionnalité | Disponible |
|----------------|----------|
| Envoyer message | Oui |
| Recevoir message | Oui |
| Messages hors ligne | Oui |
| Création groupe | Oui |
| Messagerie groupe | Oui |
| Appel audio | Oui |
| Appel vidéo | Oui |
| Notifications appels manqués | Oui |

---

## 6. Contraintes techniques

- **Serveur (TCP)** :
  - Messages
  - Signalisation
  - Groupes
  - Stockage

- **Client à client (P2P)** :
  - Audio
  - Vidéo

- **Stockage serveur** :
  - Messages non lus
  - Invitations
  - Appels manqués

- **Multithreading côté client** :
  - Interface utilisateur
  - Réception réseau

---

## 7. Exigences non fonctionnelles

- Latence < 1 seconde pour les messages
- Fiabilité de livraison (même hors ligne)
- Interface simple et intuitive
- Tolérance aux déconnexions brutales
