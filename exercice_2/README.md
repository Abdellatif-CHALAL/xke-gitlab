# Utilisation de Docker

Dans cet exercice nous allons voir comment un job peut être exécuté au sein d'une image Docker. 
Nous en profiterons pour rajouter des variables dans la configuration du pipeline, 
variables qui seront ensuite utilisées dans l'image.

* [Documentation](https://docs.gitlab.com/ee/ci/docker/README.html)

## 1. Définir une image Docker
    
* Définir une image globale reposant sur l'image Docker `ruby:slim`.  
* Créer un job affichant la version de Ruby.

<details>
<summary>Solution</summary>
<p>

```yaml
stages:
  - test

image: ruby:slim
    
myRubyTest:
  stage: test
  script:
    - ruby -v
```

</p>
</details>

## 2. Jobs identiques, images différentes 

Il est possible de faire cohabiter plusieurs versions d'une même image dans un pipeline.
Une application directe est la possibilité de réaliser des jobs identiques mais exécutés 
sur des plateformes différentes (`python`, `java`, `node` pour ne pas les citer)

Pour illuster cet exercice nous utiliserons l'image Docker de [Ruby](https://hub.docker.com/_/ruby) `ruby:<version>-alpine`
 
* Créer un pipeline composé de deux jobs. 
* Utiliser deux images distinctes pour chaque job
* Les jobs affichent la version courante de Ruby.
* Comment ont été lancés les jobs dans le pipeline ?

<details>
<summary>Solution</summary>
<p>

```yaml
stages:
  - test

myRubyTest:2.6:
  stage: test
  image: ruby:2.6-slim
  script:
    - ruby -v

myRubyTest:2.5:
  stage: test
  image: ruby:2.5-slim
  script:
    - ruby -v
```

</p>
</details>

## 3. Variables de pipeline

Vous l'aurez remarqué, la précédente configuration oblige créer un nouveau job pour chaque version de Ruby.
Nous allons essayer de faire un peu mieux en utilisant une variable de pipeline.

* Modifier la configuration précédente afin que le tag de l'image soit paramétrable.
* La variable stockant le tag doit disposer d'une valeur par défaut.
* Utiliser le lanceur de pipeline manuel dans lequel une variable sera injectée.

<details>
<summary>Solution</summary>
<p>

```yaml

```

</p>
</details>

[< Previous](../exercice_1/README.md) | [Home](../README.md) | [Next >](../exercice_3/README.md)