# carte-stages-donnees
Données nationales préparées pour les outils Stages et orientation (maths-sciences-pro.fr/stages)

Ce dépôt contient les fichiers de données de la version nationale de **Trouve
ton stage**. L’application et les scripts de préparation restent dans
[`maths-sciences-lp/carte-stages`](https://github.com/maths-sciences-lp/carte-stages).
Les anciennes adresses restent dans le dépôt de l’application. Les fichiers
Île-de-France historiques y restent conservés ; la migration proposée les remplace
à la lecture par les mêmes données nationales, sans les supprimer.

Adresse GitHub Pages prévue :
`https://maths-sciences-lp.github.io/carte-stages-donnees/`.
**L’activation de Pages et la fusion de cette préparation attendent la validation
de Naïm.** Une PR ouverte ne constitue pas une publication.

## Fichiers

- `catalogue.json` : formations, types d’entreprises, académies, départements,
  nombres d’établissements, tailles et empreintes SHA-256 des fichiers Sirene
  et des lycées. `complet: true` exige les 101 départements des 30 académies.
- `catalogue-leger.json` : formations, domaines et géographie, sans la liste des
  fichiers d’entreprises ; utilisé par les pages nationales.
- `catalogues/ile-de-france.json` : même catalogue pour les trois académies
  franciliennes et les huit départements limitrophes (02, 10, 27, 28, 45, 51, 60, 89).
- `manifestes/<département>.json` : empreintes et tailles des fichiers, chargées
  seulement après le choix d’une formation. Une recherche au rayon franchit les
  frontières d’académie ; « Toute la région » conserve la région choisie.
- `rapport-lycees-onisep.json` : provenance de chaque ajout et exceptions.
- `sirene/<département>/<secteur>.json` : entreprises du secteur dans ce
  département, chargées uniquement quand la recherche le demande.
- `lycees/<département>.json` : lycées proposant une voie professionnelle,
  issus de l’annuaire de l’Éducation nationale, complétés par Onisep (CAP, CAPa,
  bac pro, seconde professionnelle et BMA).
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
NAF. La mise à jour LBA proposée utilise un seul export pour les deux cartes et ne met à jour que ce dépôt,
prépare les nouveaux fichiers dans des copies temporaires et conserve les
derniers fichiers valides en cas d’échec. Son installation nécessite validation.

Ne jamais réécrire l’historique de `carte-stages`. Une éventuelle réduction
annuelle de l’historique de **ce dépôt de données uniquement** exige une
sauvegarde et l’accord explicite de Naïm. Aucun script de préparation ne réalise
cette opération.

## Bilan de la préparation du 8 octobre 2026

- 101 départements, 30 académies.
- 1,657,440 établissements uniques après classement et filtres.
- 2 487 lycées issus de l’annuaire, complétés par 699 UAI Onisep : **3 186 UAI**.
  Les 2 487 fiches initiales restent inchangées. Parmi 768 UAI absents, 68 sont
  hors périmètre (66 collectivités, Andorre et Monaco) et l’UAI 9741070V
  du GRETA Réunion est différé : deux antennes sans point unique vérifié.
  Le choix Plabennec pour 0291604L est croisé avec l’annuaire.
  55 lignes Onisep sans UAI ne sont pas ajoutées ; détail dans le rapport.
- 213,411 établissements avec une indication LBA ; export daté du 2026-10-08T01:01:04.000Z.
- 198 809 776 octets de fichiers Sirene, 36 540 590 octets de fichiers LBA.
- Les tailles de tous les fichiers Sirene sont dans `tailles-fichiers.csv`.
- Les chiffres par académie sont dans `bilan-academies.csv` ; le détail par département est dans `bilan.json`.
- Les durées, la pause et les limites du journal sont dans `collecte.json`.

## Reproduire le complément et les catalogues légers

Dans la copie du dépôt de l’application contenant la mission 8.5 :

```sh
python3 source/stage_lycees.py --root /copie/carte-stages-donnees --onisep /cache/605340ddc19a9.csv
python3 source/stage_catalogues.py --root /copie/carte-stages-donnees
```

Source : [offre de formation initiale Onisep](https://opendata.onisep.fr/data/605340ddc19a9/),
fichier local contrôlé le 8 octobre 2026, empreinte dans le rapport. Seuls UAI,
nom, commune, code postal et coordonnées sont copiés, sans contacts.
Les fichiers Sirene et LBA de la collecte du 8 octobre restent inchangés.
Les nouveaux catalogues doivent être disponibles avant de déployer le code qui les utilise.
