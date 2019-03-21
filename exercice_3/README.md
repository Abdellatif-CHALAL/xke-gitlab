# Utilisation du cache et des artefacts

Dans cet exercice nous allons chercher à partager certains éléments entre les jobs.
Pour compléter ces notions de cache et d'artefacts nous 

## 1. Utiliser le cache

* Créer un pipeline constitué de deux jobs:
    * Job `build`: Créer un fichier `build-info.txt` dans un répertoire `build`.
    * Job `test`: Vérifier l'existence du fichier.
    * Ne pas définir de cache pour le moment.
* Le fichier produit par le job `build` est-il disponible dans le job `test` ?
* Déclarer un cache se basant sur le contenu du répertoire `build`.
* Vérifier que le pipeline n'échoue plus.

<details>
<summary>Solution</summary>
<p>

```yaml
cache:
    paths:
        - ./build/

stages:
    - build
    - test
    - deploy

build:
    stage: build
    script:
        - mkdir build
        - echo "test" > build/build-info.txt

test:
    stage: test
    script:
        - if [ ! -f build/build-info.txt ]; then exit 1; fi
```

</p>
</details>

## 2. Utiliser les artefacts

* Ajouter un job `dist` (stage `deploy`) construisant un `*.tar.gz` à partir du contenu de `build`
    * `tar zcvf dist/artifact.tar.gz build/`
* Déclarer le tar.gz comme artefact du build
* Fixer sa durée de vie à 5 minutes maximum

<details>
<summary>Solution</summary>
<p>

<details>
<summary>Solution</summary>
<p>

```yaml
cache:
    paths:
        - ./build/

stages:
    - build
    - test
    - deploy

build:
    stage: build
    script:
        - mkdir build
        - echo "test" > build/build-info.txt

test:
    stage: test
    script:
        - if [ ! -f build/build-info.txt ]; then exit 1; fi

deploy:
    stage: deploy
    script:
        - mkdir dist
        - tar zcvf dist/artifact.tar.gz build/
```

</p>
</details>

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