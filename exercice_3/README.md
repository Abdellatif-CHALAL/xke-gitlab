# Utilisation du cache et des artefacts

Dans cet exercice nous allons partager certains éléments entre les jobs à l'aide des notions de cache et d'artefacts. 

## 1. Le cache

`Cache` est utilisé pour spécifier une liste de fichiers et de répertoires à mettre en cache entre les `jobs`. 
Vous ne pouvez utiliser que les chemins situés dans l'espace de travail du projet.

Si le `cache` est défini en dehors des `jobs`, cela signifie qu'il est défini globalement et que tous les `jobs` utiliseront cette définition.

* Créer un pipeline constitué de deux jobs :
    * Sans définir le cache pour le moment
    * Définir un job `build` - y scripter la création d'un fichier `build-info.txt` dans un répertoire `build`
    * Définir un job `test` - y verifier l'existence de ce fichier (`if [ ! -f build/build-info.txt ]; then exit 1; fi`)
* Le fichier produit par le job `build` est-il disponible dans le job `test` ?
* Déclarer un cache :
    * Path `./build/`
    * Optional : utiliser un `key` de cache pour l'isoler entre les pipelines
* Vérifier que le pipeline passe "au vert"

<details>
<summary>Solution</summary>
<p>

```yaml
cache:
  key: "$CI_COMMIT_REF_SLUG"
  paths:
    - ./build/

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

## 2. Les artefacts

* Ajouter un job `dist` (stage `deploy`) construisant un `tar.gz` à partir du contenu de repertoire `build` (`tar zcvf dist/artifact.tar.gz build/`)
* Déclarer le `tar.gz` comme artefact du build. Cet artefact doit :
    * Etre disponible dans le répertoire `dist`
    * Avoir une durée de vie à `5 minutes`
    * Etre téléchargable dans un zip nommé `<projet>-<branche>-<sha1 du commit>` (cf. les variables d'environement d'un pipeline)

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

<p>
<img src="artefact.png" height="200">
</p> 

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