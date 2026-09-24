# Inventaire des points d'entrée

Le livrable de l'atelier d'analyse. Il dit ce que l'API devra **permettre** — pas comment on
l'écrira.

Une règle : tout ce qui figure ici doit se justifier par la lettre de mission ou par un écran
des maquettes. Si vous ne savez plus d'où vient une ligne, c'est peut-être qu'elle n'en vient
pas. (Seule exception : la section bonus, si vous en ajoutez une.)

---

## Ce que l'utilisateur peut faire

Une action par ligne, en français. Pas encore de chemins.

- Rechercher une ville desservie par le réseau de catapultes
- Rechercher des lancers directs disponibles entre deux villes à une date donnée pour un nombre de passagers
- Consulter le détail et les contraintes balistiques d'un lancer
- Créer un compte voyageur (dossier voyageur)
- Se connecter à son compte voyageur
- Renouveler sa session de connexion
- Consulter son dossier voyageur
- Ouvrir un panier de réservation
- Consulter son panier actif
- Ajouter un lancer et un nombre de places dans son panier
- Retirer une ligne de son panier
- Régler son panier et valider sa réservation
- Consulter ses billets émis

## Les points d'entrée

| Ce que ça fait | Chemin proposé | Qui peut l'appeler |
|---|---|---|
| Rechercher une ville desservie | GET /cities | Tout le monde (public) |
| Rechercher des lancers disponibles | POST /trips/search | Tout le monde (public) |
| Consulter le détail d'un lancer | GET /trips/{id} | Tout le monde (public) |
| Ouvrir un dossier voyageur (inscription) | POST /auth/register | Tout le monde (public) |
| Se connecter au compte voyageur | POST /auth/login | Tout le monde (public) |
| Consulter son dossier voyageur | GET /users/me | Voyageur connecté |
| Ouvrir un nouveau panier | POST /carts | Voyageur connecté |
| Consulter son panier actif en cours | GET /carts | Voyageur connecté |
| Ajouter un lancer et des places au panier | POST /carts/{id}/items | Voyageur connecté |
| Retirer une ligne du panier | DELETE /carts/{id}/items/{itemId} | Voyageur connecté |
| Valider et payer le panier | POST /carts/{id}/pay | Voyageur connecté |
| Consulter la liste de ses billets | GET /tickets | Voyageur connecté |

## Les données qui circulent

Pour chaque point d'entrée : ce qu'il reçoit, ce qu'il renvoie. Nommez les données comme
l'Office les nomme — les traduire en identifiants techniques, c'est le travail de demain.

### Rechercher une ville desservie (GET /cities)
- **Reçoit** : Filtre textuel de recherche (nom ou début de nom de ville).
- **Renvoie** : Liste des villes desservies avec le nom de chaque ville et sa catapulte centrale.

### Rechercher des lancers disponibles (POST /trips/search)
- **Reçoit** : Ville de départ, ville d'arrivée, date du lancer, nombre de places souhaité.
- **Renvoie** : Liste des lancers directs correspondants (ville de départ, ville d'arrivée, date et heure de tir, durée de vol, prix par place, places disponibles).

### Consulter le détail d'un lancer (GET /trips/{id})
- **Reçoit** : Identifiant du lancer (dans le chemin).
- **Renvoie** : Informations complètes du lancer (villes de départ et d'arrivée, horaire précis de tir, durée de vol, tarif par passager, places disponibles, franchise de masse autorisée pour les bagages, informations balistiques).

### Ouvrir un dossier voyageur (POST /auth/register)
- **Reçoit** : Adresse e-mail, mot de passe, prénom (facultatif), nom de famille (facultatif).
- **Renvoie** : Dossier voyageur créé (identifiant, adresse e-mail, prénom, nom, date de création).

### Se connecter (POST /auth/login)
- **Reçoit** : Adresse e-mail, mot de passe.
- **Renvoie** : Jeton d'accès (courte durée).

### Consulter son dossier voyageur (GET /users/me)
- **Reçoit** : Rien (identifié par le jeton du voyageur).
- **Renvoie** : Dossier du voyageur connecté (identifiant, adresse e-mail, prénom, nom, date de création).

### Ouvrir un panier (POST /carts)
- **Reçoit** : Rien (panier ouvert à l'initiative du voyageur connecté).
- **Renvoie** : Panier créé (identifiant du panier, état initial vide).

### Consulter son panier actif (GET /carts)
- **Reçoit** : Rien (identifié par la session du voyageur).
- **Renvoie** : Panier actif du voyageur (identifiant, liste des articles avec lancers réservés et nombre de places, montant total calculé par le serveur) ou panier vide s'il n'en a pas.

### Ajouter un lancer au panier (POST /carts/{id}/items)
- **Reçoit** : Identifiant du panier (dans le chemin), identifiant du lancer, nombre de places.
- **Renvoie** : Panier complet mis à jour avec les lignes d'articles, sous-totaux et montant total recalculé.

### Retirer une ligne du panier (DELETE /carts/{id}/items/{itemId})
- **Reçoit** : Identifiant du panier et identifiant de la ligne d'article (dans le chemin).
- **Renvoie** : Panier mis à jour (ou confirmation de fermeture du panier si c'était le dernier article).

### Valider et payer le panier (POST /carts/{id}/pay)
- **Reçoit** : Identifiant du panier (dans le chemin), moyen de paiement déclaré.
- **Renvoie** : Confirmation de la réservation validée avec état payé et liste des billets émis.

### Consulter ses billets (GET /tickets)
- **Reçoit** : Rien (identifié par la session du voyageur).
- **Renvoie** : Liste des billets détenus par le voyageur (identifiant du billet, référence du lancer, villes, date et heure du lancer, droit d'embarquement).

## Ce dont je ne suis pas sûrs

Les questions que la lettre et les maquettes ne tranchent pas. Une question notée vaut mieux
qu'une réponse inventée.

- Quelle est la durée de validité d'un panier avant expiration et remise en vente des places retenues ?
- Que doit renvoyer l'API si le voyageur tente de payer un panier déjà payé ?
- La franchise de masse concerne-t-elle le bagage individuel ou le total des passagers d'une ligne de réservation ?
- est ce que la personne doit de reconnecter a chaque fois ou il a un token ?

