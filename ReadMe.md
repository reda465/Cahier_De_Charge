# Cahier des charges fonctionnel - Application de messagerie instantanée

## 1. Vue d’ensemble

L’application permet à plusieurs utilisateurs de communiquer entre eux via :

- Messages texte en temps réel
- Appels audio (directs entre deux clients)
- Appels vidéo (directs entre deux clients)

### 1.1 Architecture générale

- Un **serveur central** multithread gère les connexions et relaie les messages texte.
- Les **clients** communiquent avec le serveur pour la signalisation (connexion, envoi de messages, demande d’appel).
- Les **flux audio et vidéo** sont échangés directement entre les clients (sans passer par le serveur).

---

## 2. Connexion d’un utilisateur (interface utilisateur)

### 2.1 Écran de connexion

Au démarrage de l’application, l’utilisateur voit une **fenêtre de connexion** contenant :

- Un champ de texte pour saisir son **pseudo** (obligatoire)
- Un champ de texte pour saisir l’**adresse IP du serveur** (exemple : 192.168.1.45)
- Un champ de texte pour saisir le **port du serveur** (par défaut 1234)
- Un bouton **« Se connecter »**

### 2.2 Déroulement de la connexion (côté utilisateur)

1. L’utilisateur remplit les trois champs.
2. Il clique sur **« Se connecter »**.
3. L’application tente de joindre le serveur :
   - Si la connexion réussit et que le pseudo est libre → la fenêtre de connexion se ferme et la **fenêtre principale** s’ouvre.
   - Si le pseudo est déjà utilisé → un message d’erreur s’affiche : *« Pseudo déjà pris »*.
   - Si le serveur est injoignable → un message d’erreur s’affiche : *« Impossible de joindre le serveur »*.

### 2.3 Fenêtre principale (après connexion)

La fenêtre principale contient :

- **Colonne de gauche** : liste des utilisateurs connectés (mise à jour automatiquement)
- **Zone centrale** : historique des messages (affichage des conversations)
- **Champ de saisie** en bas : pour taper un message
- **Bouton « Envoyer »** (ou touche Entrée)
- **Boutons « Appel audio »** et **« Appel vidéo »** (actifs quand un contact est sélectionné)
- **Bouton « Déconnexion »**

---

## 3. Fonctionnalités entre clients

### 3.1 Envoi de message texte

**Déclencheur** : L’utilisateur sélectionne un destinataire dans la liste des connectés, tape un message et clique sur « Envoyer ».

**Comportement** :
- Le message est envoyé au serveur.
- Le serveur le relaie immédiatement au destinataire.
- L’expéditeur voit son message apparaître dans la zone de chat.

### 3.2 Réception automatique des messages

**Déclencheur** : Aucune action de l’utilisateur. Le message arrive automatiquement.

**Comportement** :
- Dès qu’un message est envoyé par un autre utilisateur, il s’affiche **immédiatement** dans la zone de chat du destinataire.
- L’utilisateur n’a pas besoin de rafraîchir ou de cliquer sur « Recevoir ».
- Pendant la réception, l’utilisateur peut continuer à taper ou à naviguer dans l’interface (pas de blocage).

### 3.3 Visualisation de la liste des connectés

**Déclencheur** : Connexion réussie ou changement dans la liste (arrivée/départ d’un utilisateur).

**Comportement** :
- À la connexion, l’utilisateur voit la liste de tous les autres utilisateurs déjà connectés.
- Quand un nouvel utilisateur se connecte, son pseudo apparaît **automatiquement** dans la liste de tous les clients connectés.
- Quand un utilisateur se déconnecte, son pseudo disparaît **automatiquement** de toutes les listes.

### 3.4 Démarrer un appel audio

**Déclencheur** : L’utilisateur sélectionne un contact et clique sur « Appel audio ».

**Comportement** :
- Une demande d’appel est envoyée au destinataire via le serveur.
- Le destinataire voit une notification : *« X vous appelle. Accepter ? Refuser ? »*
- Si le destinataire **accepte** :
  - Un flux audio direct s’établit entre les deux clients.
  - Les deux utilisateurs peuvent parler en temps réel.
- Si le destinataire **refuse** :
  - L’appelant reçoit une notification : *« Appel refusé »*.

### 3.5 Démarrer un appel vidéo

**Déclencheur** : L’utilisateur sélectionne un contact et clique sur « Appel vidéo ».

**Comportement** :
- Même mécanisme que l’appel audio, mais avec transmission de la vidéo en plus.
- Le destinataire voit : *« X demande un appel vidéo. Accepter ? Refuser ? »*
- Si accepté :
  - Flux audio + vidéo directs entre les deux clients.
  - Chaque utilisateur voit l’image de l’autre (et la sienne en petit).

### 3.6 Terminer un appel

**Déclencheur** : L’utilisateur clique sur « Raccrocher » (bouton apparaissant pendant l’appel).

**Comportement** :
- L’audio et/ou la vidéo s’arrêtent.
- Les deux clients retournent à l’écran de chat.
- L’autre utilisateur est automatiquement informé de la fin d’appel.

### 3.7 Déconnexion

**Déclencheur** : L’utilisateur clique sur « Déconnexion » ou ferme la fenêtre.

**Comportement** :
- Le client prévient le serveur.
- Le serveur supprime l’utilisateur de la liste des connectés.
- Tous les autres clients voient le pseudo disparaître de leur liste.

---

## 4. Résumé des fonctionnalités (version 1)

| Fonctionnalité | Acteur | Automatique ? |
|----------------|-----------|------------|
| Connexion | Utilisateur | Non |
| Déconnexion | Utilisateur | Non |
| Envoyer un message | Utilisateur | Non |
| Recevoir un message | Client | **Oui** |
| Voir la liste des connectés | Utilisateur | Non (mais mise à jour automatique) |
| Démarrer appel audio | Utilisateur | Non |
| Démarrer appel vidéo | Utilisateur | Non |
| Répondre à un appel | Utilisateur | Non |
| Recevoir notification d’appel | Client | **Oui** |
| Terminer un appel | Utilisateur | Non |

---

## 5. Contraintes techniques (rappel)

- **Messages texte** : transitent obligatoirement par le serveur (TCP)
- **Signalisation** (connexion, appel, acceptation, refus) : transitent par le serveur (TCP)
- **Flux audio et vidéo** : transitent directement entre clients (sans le serveur)
- Le client doit maintenir **deux threads** :
  - Un pour l’interface utilisateur
  - Un pour écouter en permanence les messages entrants (automatiques)

---

## 6. Exigences non fonctionnelles

- Temps de latence : les messages doivent s’afficher chez le destinataire en **moins d’une seconde**.
- Stabilité : le serveur ne doit pas planter si un client se déconnecte brutalement.
- Interface : simple et intuitive (inspirée de WhatsApp ou Messenger).
