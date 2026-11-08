# CLAUDE.md

Ce fichier donne à Claude Code (claude.ai/code) les consignes pour travailler dans ce dépôt.

## Ce dépôt

Les thèmes Home Assistant assortis aux cartes de [HA-Plooum-Cards](https://github.com/plooum/HA-Plooum-Cards) (même propriétaire, mêmes conventions), livrés comme un dépôt HACS de catégorie **Thème**.

- **Un seul fichier de thème** : [themes/plooum.yaml](themes/plooum.yaml). HACS ne gère qu'un fichier dans `themes/` par dépôt (les autres sont ignorés) ; ce fichier déclare donc tous les thèmes, chacun sous sa propre clé de premier niveau (`Plooum Lagon:`, …). [hacs.json](hacs.json) le désigne par `filename`.
- Chaque thème s'appelle `Plooum <Nom>` et déclare ses couleurs sous `modes: dark:` (et/ou `light:`).
- Les variables propres aux cartes Plooum (`plooum-group-color`, lue par `ha-plooum-group-card`) sont documentées dans le tableau des deux READMEs. Une nouvelle variable lue par une carte se documente ici et dans la doc de la carte, côté HA-Plooum-Cards.
- [previews/](previews/) : une capture par thème, affichée par les READMEs.

## Conventions

- **Tout est en français dans ce dépôt, sauf les thèmes (clés, commentaires du fichier YAML) et la documentation utilisateur** : [README.md](README.md) en anglais (affiché par GitHub et HACS), [README.fr.md](README.fr.md) en français, chacun renvoyant vers l'autre en haut. Toute modification de l'un se fait dans l'autre, dans la même PR. Ce fichier, les titres et descriptions de PR, les messages de commit et les notes de release sont en français.
- Les problèmes hors périmètre repérés en travaillant vont dans [TODO.md](TODO.md).

## Ajouter un thème

1. Ajoute-le dans [themes/plooum.yaml](themes/plooum.yaml), sous une nouvelle clé `Plooum <Nom>:`, avec un commentaire d'une ligne au-dessus.
2. Teste-le dans le Home Assistant de dev de HA-Plooum-Cards : copie le fichier dans son `dev/ha-config/themes/`, appelle `frontend.reload_themes` (`dev/api.sh POST services/frontend/reload_themes`) et regarde une vue avec `theme: Plooum <Nom>` (la vue `/plooum-test/group` y est sous Plooum Lagon). Vérifie le fond, le contraste des textes, les cartes Plooum et les groupes en verre.
3. Ajoute une capture dans [previews/](previews/) et une section dans les deux READMEs (ligne du tableau, description, aperçu).

## Workflow Git

Comme HA-Plooum-Cards : toute modification passe par une pull request, rien n'est commité directement sur `main` (seule exception : le commit initial du dépôt).

1. Branche `feature/<nom>` depuis un `main` à jour (`git fetch`, `git pull --ff-only`). Pas de `tags/` dans le nom de branche (HACS retire `tags/` des URL qu'il télécharge).
2. Commite, pousse, ouvre une PR vers `main` avec `gh pr create` et vérifie que sa CI ([validate.yml](.github/workflows/validate.yml) : validation HACS et contrôle du fichier de thème) passe.
3. Attends l'approbation explicite du propriétaire. Ne fusionne jamais une PR avant.
4. Une fois approuvée : rebase sur `origin/main` si `main` a bougé, puis **squash-merge** : `gh pr merge <N> --squash --delete-branch`, sujet `<titre de la PR> (#<N>)`.
5. En local : `git checkout main`, `git pull --ff-only`, `git branch -D feature/<nom>`.

### Releases

Tags `V<major>.<minor>.<patch>` (légers), versionnement sémantique : majeure quand un thème est renommé ou supprimé (les profils des utilisateurs le référencent par son nom), mineure pour un nouveau thème ou une nouvelle variable, patch pour un réglage de couleurs. Ils se posent sur `main` une fois les PR fusionnées (`git fetch --tags` d'abord). La release GitHub porte le nom du tag et ses notes sont en français. HACS installe le contenu de la **dernière release** : un tag seul ne suffit pas.

Vérifie que la nouvelle release est en tête de la liste que renvoie GitHub (`gh api repos/plooum/HA-Plooum-Themes/releases --jq '.[0].tag_name'`) : GitHub trie les tags en `V` majuscule comme du texte, donc au passage de 9 à 10 (`V1.9.0` → `V1.10.0`) l'ancienne release reste devant. Dans ce cas, avec l'accord du propriétaire, supprime les releases qui la précèdent (`gh release delete <tag> --yes`, sans `--cleanup-tag`).
