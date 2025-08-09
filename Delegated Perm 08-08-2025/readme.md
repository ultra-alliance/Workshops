# 🛡️ Délégation de permissions sur Ultra.io : sécuriser et automatiser vos transactions blockchain

Dans l'écosystème **Ultra.io**, vous pouvez déléguer des permissions à d'autres comptes ou logiciels pour exécuter certaines actions **en votre nom**, tout en contrôlant précisément ce qu’ils peuvent faire.  

Cette gestion fine des autorisations est héritée d’**EOSIO**, et permet à la fois d’automatiser des tâches et de renforcer la sécurité.

Ce tutoriel explique comment :  
1. Créer une permission dérivée avec `eosio::updateauth`  
2. Restreindre son usage avec `eosio::linkauth`  
3. Créer un wallet "sans clé privée" avec `eosio::newnonebact`  

---

## 🔑 Pourquoi déléguer des permissions ?

Prenons un exemple : dans un jeu Ultra comme **Empire**, vous devez récolter les ressources de votre mine toutes les heures. Plutôt que de le faire manuellement, vous pouvez déléguer cette action à un programme automatisé **sans lui donner accès complet à votre wallet**.

Ceci est possible grâce à la création d’une **permission spécifique et limitée**.

---

## 1️⃣ Créer une permission dérivée avec `eosio::updateauth`

`updateauth` permet de créer une **nouvelle permission** en la dérivant d’une permission existante (souvent `active`). Cette permission peut ensuite être donnée :  
- à une **clé publique** (type EOS ou Ultra)  
- à **un autre compte Ultra**  
- ou à un **temps d’attente** (`waits`)  

Chaque permission est **un ensemble de règles** qui déterminent qui ou quoi peut valider une transaction.

---

### 📌 Comprendre `threshold` et `weight`

C’est ici que réside la mécanique la plus importante.

- **`threshold`** : c’est **le score minimum** à atteindre pour qu’une transaction utilisant cette permission soit validée.  
  → Par exemple : si `threshold = 2`, il faut accumuler au moins 2 points de poids (`weight`) pour signer.

- **`weight`** : c’est **le score accordé à chaque autorisation** (clé publique, compte ou délai d’attente) définie dans la permission.  
  → Par exemple : une clé peut avoir un `weight = 1`, un compte un `weight = 1`, et un délai d’attente un `weight = 1`.

**Comment ça fonctionne ?**  
Lorsqu’une transaction est initiée avec cette permission, **on additionne les poids (`weight`) des signatures ou conditions validées**.  
- Si la somme **atteint ou dépasse le `threshold`**, la transaction passe.  
- Sinon, elle est refusée.

💡 Cela permet de créer des logiques simples (une seule signature suffisante) ou complexes (multi-signature obligatoire, délais avant exécution, etc.).

---

### 📋 Champs principaux de `updateauth`

- `account` : votre compte Ultra
- `permission` : le nom de la permission dérivée (ex : `mineperm`)
- `parent` : la permission dont on hérite (souvent `active`)
- `auth` : l’ensemble des règles d’accès
  - `threshold` : score minimum requis
  - `keys` : liste des clés publiques autorisées, avec leur `weight`
  - `accounts` : liste des comptes Ultra autorisés, avec leur `weight`
  - `waits` : délais à attendre avant validation, avec leur `weight`

---

## 2️⃣ Restreindre cette permission avec `eosio::linkauth`

Créer une permission, c’est bien. **Limiter son usage**, c’est encore mieux.  

`linkauth` permet d’associer une permission **à un smart contract et à une action précise**.  
Ainsi, le compte ou la clé à qui vous avez donné cette permission **ne pourra exécuter que ce qui est autorisé**.

---

### 📋 Champs principaux de `linkauth`

- `account` : votre compte Ultra
- `code` : nom du smart contract autorisé
- `type` : action autorisée (ex : `harvest`)
- `requirement` : permission dérivée créée précédemment

💡 Grâce à `linkauth`, un logiciel tiers pourra exécuter l’action `harvest` sur le smart contract `empiregame` en votre nom, **sans jamais pouvoir envoyer de tokens ou modifier d’autres paramètres**.

---

## 3️⃣ Créer un wallet "sans clé privée" avec `eosio::newnonebact`

Il est aussi possible de créer un compte Ultra **sans clé privée**, contrôlable uniquement via d’autres comptes.  

Ce mécanisme est idéal pour :  
- Créer un wallet **DAO trustless** (gouvernance par plusieurs membres)  
- Empêcher toute compromission par vol de clé  
- Gérer un compte via un smart contract ou un système automatisé

---

### Fonctionnement

Lors de la création, au lieu de définir une clé publique, on définit **des comptes Ultra dans `accounts`** pour les permissions `owner` et `active`.  

Cela signifie :
- **Aucune clé privée** ne peut signer une transaction depuis ce wallet
- Seuls les comptes définis peuvent agir dessus

---

## 🔐 Avantages et sécurité

✅ **Automatisation sécurisée** : déléguer des actions sans donner accès complet  
✅ **Contrôle précis** : limiter à une action ou un smart contract  
✅ **Multi-signature possible** : grâce au système `threshold` / `weight`  
✅ **Wallets inviolables** : via `newnonebact` sans clé privée  
✅ **Révocation simple** : modification ou suppression d’une permission à tout moment

---

## 🧪 Exemple d’usage avancé : DAO trustless

En combinant `newnonebact`, `updateauth` et `linkauth`, on peut créer un wallet **géré collectivement**, sans clé privée, et utilisable uniquement via un système de vote multi-signature.  

Cela permet de gérer des fonds ou des actifs **sans qu’aucun membre ne puisse agir seul**, rendant le système totalement décentralisé et sécurisé.

---

## 📚 Source

- [Advanced EOS Permissions – EOS Amsterdam](https://eos-amsterdam.medium.com/advanced-eos-permissions-2ad67f888f1e)
