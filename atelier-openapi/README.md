# CATAPULTE Connect — Contrat d'API

Module **CAP — Concevoir une API REST**. Coda Dijon, B2.

L'Office National des Trajectoires Balistiques vous confie la conception du contrat
d'API de **CATAPULTE Connect**, sa billetterie grand public. Vous n'écrirez pas de
code : vous écrivez le **contrat**, et le contrat fait foi.

## Démarrer

Placez-vous dans ce dossier, puis lancez le mock :

```bash
cd atelier-openapi
docker compose up
```

Votre contrat est servi sur `http://localhost:4010`. Il répond déjà — les opérations
d'authentification sont écrites.

```bash
curl -X POST http://localhost:4010/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"email":"bob@example.com","password":"correct-horse-battery-staple"}'
```

Prism relit `openapi.yaml` à chaque requête : modifiez le contrat, rejouez la requête,
la réponse a changé. Aucun redémarrage.

Votre contrat est aussi rendu en lecture sur `http://localhost:4011` (Swagger UI) :
la vue lisible du même `openapi.yaml`, utile pour relire vos opérations sans rouvrir
le YAML. Elle aussi se met à jour au rechargement de la page, sans redémarrage.

## Le fichier

Tout se passe dans **`openapi.yaml`**. Il est découpé en deux :

| Partie | État |
|---|---|
| Authentification et profil | **écrites** — vos modèles de référence |
| Sécurité, format d'erreur | **posés** — à réutiliser (`$ref`), pas à redéfinir |
| Le flux de réservation | **à vous** |

Les opérations fournies ne sont pas là pour être recopiées. Elles sont là parce que
tout ce dont vous avez besoin pour écrire le reste s'y trouve déjà au moins une fois :
un body, un `$ref`, une opération publique, une réponse d'erreur, un paramètre.

Le **contrat métier** distribué en fin de J2 fait foi sur ce que le flux doit contenir.
Les commentaires du fichier ne sont que des repères.

## Le linter

L'extension **Spectral** (VSCode) lint votre contrat en direct. Le ruleset est fixé dans
`.spectral.yaml` — il est le même pour tout le monde, donc ce qui passe chez vous passe
à la correction.

**Zéro erreur, toujours.** Une erreur de structure en cache souvent trois : corrigez au
fur et à mesure plutôt qu'à la fin.

Les **warnings**, eux, se lisent. Au démarrage vous en avez deux :

```
warning  oas3-unused-component  ...  components.responses.Forbidden
warning  oas3-unused-component  ...  components.responses.NotFound
```

Spectral vous dit que deux réponses d'erreur sont définies mais que personne ne s'en
sert. C'est exact : aucune des opérations fournies n'en a besoin. Ces deux warnings
sont donc votre todo-list — ils s'éteindront d'eux-mêmes quand vous aurez écrit les
opérations qui accèdent à une ressource pouvant être introuvable, ou appartenir à
quelqu'un d'autre.

Ne les faites pas taire en supprimant les composants : c'est le travail qui manque,
pas la déclaration qui est en trop.

## La boucle de travail

Écrire une opération, la rejouer dans Bruno (ou Prism/curl), regarder ce qui sort.
Swagger UI (`:4011`) donne la vue lisible du contrat pendant que vous l'écrivez —
utile pour relire une opération sans rouvrir le YAML. Puis la suivante.

Vous pouvez importer `openapi.yaml` dans Bruno pour générer votre collection : vos
requêtes viennent de votre propre contrat.

N'écrivez pas les douze opérations avant de tester la première.

Oui, j'ai utilisé l'IA pour pouvoir rédiger les schémas et entrypoints. Je lui ai demandé des blocs à utiliser et je les ai imbriqués ensemble. J'ai évidemment utilisé l'IA pour savoir si la syntaxe était correcte.