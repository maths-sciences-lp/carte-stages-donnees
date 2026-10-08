# carte-stages-donnees
Données nationales préparées pour les outils Stages et orientation (maths-sciences-pro.fr/stages)

Ce dépôt contient les fichiers de données de la version nationale de **Trouve
ton stage**. L’application et les scripts de préparation restent dans
[`maths-sciences-lp/carte-stages`](https://github.com/maths-sciences-lp/carte-stages).
Les anciennes adresses et les données Île-de-France restent dans ce dépôt d’origine.

Adresse GitHub Pages prévue :
`https://maths-sciences-lp.github.io/carte-stages-donnees/`.
**L’activation de Pages et la fusion de cette préparation attendent la validation
de Naïm.** Une PR ouverte ne constitue pas une publication.

## Fichiers

- `catalogue.json` : formations, types d’entreprises, académies, départements,
  nombres d’établissements, tailles et empreintes SHA-256 des fichiers Sirene
  et des lycées. `complet: true` exige les 101 départements des 30 académies.
- `sirene/<département>/<secteur>.json` : entreprises du secteur dans ce
  département, chargées uniquement quand la recherche le demande.
- `lycees/<département>.json` : lycées proposant une voie professionnelle,
  issus de l’annuaire de l’Éducation nationale.
- `lba/<département>/<secteur>.json` : indications de La bonne alternance pour
  les seuls établissements déjà retenus. Les fichiers existants sont listés
  dans `lba/meta.json` ; un secteur absent de cette liste n’a pas d’indication.
- `bilan.json` : collecte et classement par département, exclusions,
  taille des données et empreintes des tables de correspondance utilisées.

Chaque ligne Sirene contient, dans cet ordre : dénomination, enseigne, adresse,
latitude, longitude, indice de tranche d’effectifs, indicateur RGE, SIRET,
complément d’adresse. Les tranches d’effectifs sont celles de l’entreprise,
pas nécessairement de l’établissement local. Le catalogue donne leurs libellés.
Un établissement peut figurer dans plusieurs secteurs : leur somme ne constitue
donc pas un nombre d’établissements uniques. Le bilan distingue les deux nombres.

## Sources et limites

- [API Recherche d’entreprises](https://recherche-entreprises.api.gouv.fr/docs/)
  et répertoire Sirene de l’Insee : établissements actifs et diffusibles,
  entrepreneurs individuels exclus. Sans position ou sans nom utilisable,
  l’établissement n’est pas publié. Données Sirene sous Licence Ouverte 2.0.
- [Annuaire de l’Éducation nationale](https://data.education.gouv.fr/explore/dataset/fr-en-annuaire-education/)
  : établissements ouverts proposant une voie professionnelle. Les absences de
  coordonnées sont indiquées dans le bilan.
- [La bonne alternance](https://api.apprentissage.beta.gouv.fr/) : export
  national, filtré sur les SIRET déjà affichés. Les offres déléguées à des
  organismes de formation et les offres expirées sont écartées. Les durées
  d’affichage figurent dans `lba/meta.json`.
- Correspondances formation → types d’entreprises : table auditée et complétée
  dans le dépôt de l’application, commit `43a2d51` (252 CAP/bacs pro, 357 NAF).

La présence d’une entreprise ne garantit pas qu’elle accueille des stagiaires.
Un code d’activité peut couvrir plusieurs métiers. Les rapprochements donnent
des pistes à confirmer avec l’entreprise et le professeur. Un « recruteur
potentiel en alternance » n’est pas une promesse de recrutement ni d’accueil
en stage. Aucune position d’élève, aucun compte, CV ou contact nominatif n’est
enregistré dans ce dépôt. La clé d’API et les exports bruts LBA restent exclus.

## Entretien

Les scripts et leurs commandes sont documentés dans
`source/stage_france.md` du dépôt de l’application. La collecte Sirene est
reprenable grâce à un cache local distinct, identifié par la liste des codes
NAF. La mise à jour LBA proposée utilise un seul export pour les deux dépôts,
prépare les nouveaux fichiers dans des copies temporaires et conserve les
derniers fichiers valides en cas d’échec. Son installation nécessite validation.

Ne jamais réécrire l’historique de `carte-stages`. Une éventuelle réduction
annuelle de l’historique de **ce dépôt de données uniquement** exige une
sauvegarde et l’accord explicite de Naïm. Aucun script de préparation ne réalise
cette opération.

## Bilan de la préparation du 8 octobre 2026

- 101 départements, 30 académies.
- 1,657,440 établissements uniques après classement et filtres.
- 2487 lycées issus de l’annuaire.
- 213,411 établissements avec une indication LBA ; export daté du 2026-10-08T01:01:04.000Z.
- 198 809 776 octets de fichiers Sirene, 36 540 590 octets de fichiers LBA.
- Les tailles de tous les fichiers Sirene sont dans `tailles-fichiers.csv`.
- Les chiffres par académie sont dans `bilan-academies.csv` ; le détail par département est dans `bilan.json`.
- Les durées, la pause et les limites du journal sont dans `collecte.json`.
