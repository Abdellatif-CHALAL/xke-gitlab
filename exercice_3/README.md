# Utilisation du cache et des artefacts

Dans cet exercice nous allons chercher à partager certains éléments entre les jobs.
Pour compléter ces notions de cache et d'artefacts nous 

## 1. Utiliser le cache

* Créer un pipeline constitué de deux jobs:
    * Job `build`: Créer un fichier `build-info.txt` dans un répertoire `build`.
    * Job `test`: Vérifier l'existence du fichier.
    * Ne pas définir de cache pour le moment.
* Le fichier produit par le job `build` est-il disponible dans le job `test` ?
* Déclarer un cache
    * Se basant sur le contenu du répertoire `build`.
    * Disposant d'un clé pour isoler le cache entre les pipelines
* Vérifier que le pipeline n'échoue plus.

<details>
<summary>Solution</summary>
<p>

```yaml
cache:
  key: "$CI_COMMIT_REF_SLUG"
  paths:
    - ./build/

stages:
  - build
  - test

build:
  stage: build
  before_script:
    - rm -rf ./build
    - mkdir ./build
  script:
    - echo "test" > build/build-info.txt

test:
  stage: test
  script:
    - if [ ! -f build/build-info.txt ]; then exit 1; fi
```

</p>
</details>

## 2. Utiliser les artefacts

* Ajouter un job `dist` (stage `deploy`) construisant un `tar.gz` à partir du contenu de `build`.
    * `tar zcvf dist/artifact.tar.gz build/`
* Déclarer le `tar.gz` comme artefact du build.
* L'artefact doit:
    * Etre disponible dans le répertoire `dist`.
    * Avoir une durée de vie à 5 minutes.
    * Etre téléchargable dans un zip nommé `<projet>-<branche>-<sha1 du commit>` (cf. les variables d'environement d'un pipeline).

<details>
<summary>Solution</summary>
<p>

```yaml
cache:
  key: "$CI_COMMIT_REF_SLUG"
  paths:
    - ./build

stages:
  - build
  - test
  - deploy

build:
  stage: build
  before_script:
    - rm -rf ./build
    - mkdir ./build
  script:
    - echo "test" > ./build/build-info.txt

test:
  stage: test
  script:
    - if [ ! -f ./build/build-info.txt ]; then exit 1; fi

dist:
  stage: deploy
  before_script:
    - rm -rf ./dist
    - mkdir ./dist
  script:
    - tar zcvf ./dist/artifact.tar.gz ./build 
  artifacts:
    name: "$CI_PROJECT_NAME-$CI_COMMIT_REF_NAME-$CI_COMMIT_SHORT_SHA"
    paths:
      - dist/
    expire_in: 5 mins
```

</p>
</details>

## 3. Lier les jobs

* En s'inspirant du pipeline décrit dans l'exercice 2.2  
* 

<details>
<summary>Solution</summary>
<p>

```yaml

```

</p>
</details>

## 4. Déclencher un pipeline
  
* 
*

<details>
<summary>Solution</summary>
<p>

```yaml

```

</p>
</details>
    
[< Previous](../exercice_2/README.md) | [Home](../README.md) | [Next >](../exercice_4/README.md)