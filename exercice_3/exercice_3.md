# Utilisation du cache et des artefacts

Dans cet exercice nous allons chercher à partager certains éléments entre les jobs.
Pour compléter ces notions de cache et d'artefacts nous 

## 1. Utiliser le cache

* Créer un pipeline constitué de deux jobs
    * Job 1: Créer un fichier dans un répertoire `build`
    * Job 2: Créer un tar.gz contenant le fichier précédent et déposer l'archive dans un répertoire `dist` 
* Le deuxième job est-il capable d'utiliser le fichier produit par le job 1 ?
* Déclarer un cache utilisant le répertoire dist

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
    - dist

build:
    stage: build
    script:
        - mkdir build
        - echo "test" > build/build-info.txt

test:
    stage: test
    script:
        - if [ ! -f build/build-info.txt ]; then exit 1; fi

dist:
    stage: dist
    script:
        - mkdir dist
        - tar zcvf dist/artifact.tar.gz build/
```

</p>
</details>

## 2. Utiliser les artefacts
    
* Reprendre le pipeline précédent et déclarer le tar.gz comme artefact du build

NB: donner la commande de tar

* Fixer sa durée de vie à 5 minutes maximum

<details>
<summary>Solution</summary>
<p>

```yaml

```

</p>
</details>

## 3. Lier les jobs

<details>
<summary>Solution</summary>
<p>

```yaml

```

</p>
</details>

## 4. Déclencher un pipeline
  
TODO: 
* Expliquer la différence de scope entre le cache et les artefacts   
* 

<details>
<summary>Solution</summary>
<p>

```yaml

```

</p>
</details>
    
[< Previous](../exercice_2/exercice_2.md) | [Home](../README.md) | [Next >](../exercice_4/exercice_4.md)