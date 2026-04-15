# 🔐 2. Connexion d’un utilisateur (VERSION MODIFIÉE)

## 2.1 Première utilisation

Lors de la première connexion :

- Le serveur identifie automatiquement l’utilisateur via son **adresse IP**
- L’utilisateur doit saisir :
  - **Pseudo**
  - **Nom complet**
  - **Mot de passe**

### Vérifications :

- **Pseudo :**
  - Doit être **unique**
  - Si déjà utilisé → demander un autre

- **Nom complet :**
  - Ne doit pas être vide

- **Mot de passe :**
  - Doit respecter un minimum de sécurité (longueur minimale, etc.)

### Une fois validé :

Le serveur enregistre l’utilisateur avec :

- **Adresse IP**
- **Pseudo**
- **Nom complet**
- **Mot de passe (stocké de manière sécurisée)**

---

## 2.2 Connexions suivantes

- L’utilisateur est reconnu via son **adresse IP**

### À la connexion :

L’utilisateur doit saisir :

- **Pseudo**
- **Nom complet**
- **Mot de passe**

### Vérifications :

Le serveur vérifie que :

- Le pseudo existe
- Le nom complet correspond au pseudo
- Le mot de passe est correct
