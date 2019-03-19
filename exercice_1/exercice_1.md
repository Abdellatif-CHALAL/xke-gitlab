# Les basiques

Dans cet exercice nous allons parcourir la syntaxe basique du fichier `gitlab-ci.yml`.
Nous utiliserons les notions de script, de job et de stage afin de construire un premier pipeline.

* Note: 
L'interface web de GitLab CI dispose d'un linter permettant de valider la syntaxe du fichier de configuration. 
Chercher CI Lint.
    
## 1. Utiliser les jobs et les stages
    
* Créer un pipeline contenant un unique job affichant "Hello World". 
* A quel stage votre job a-t'il été rattaché lors de son exécution ? 
* Ajoutez 3 stages à votre configuration (`build`, `test`, `deploy`)
* Rattachez à chaque stage un job spécifique

<details><summary>Solution</summary>
<p>

```yaml
myJob:
  script:
    - echo "Hello World"
```

</p>
<p>

```yaml
stages:
  - build
  - test
  - deploy

myBuildJob:
  stage: build
  script:
    - echo "Running the build script"

myTestJob:
  stage: test
  script:
    - echo "Running the test script"

myDeployJob:
  stage: deploy
  script:
    - echo "Running the deploy script"
```

</p>
</details>

## 3. Opérations avant et après un job

A l'entrée ou à la sortie d'un job il est possible de réaliser des opérations supplémentaires selon les besoins.
Ces opérations peuvent être globales ou spécifiques au job en cours d'exécution.

* Ajouter un pré-script global dans la configuration
* Ajouter un pré-script spécifique au job rattaché au stage `test`
* Vérifiez pour chaque job quel pré-script a été finalement lancé.
* Que peut-on en déduire à propos de la priorité entre un `before-script` global et un `before_script` à l'intérieur d'un job ?

<details><summary>Solution</summary>
<p>

```yaml
stages:
  - build
  - test
  - deploy

before_script:
  - echo "Running the default before script"

myBuildJob:
  stage: build
  script:
    - echo "Running the build script"

myTestJob:
  stage: test
  before_script:
    - echo "Running the before script for myTestJob" 
  script:
    - echo "Running the first test script"
    
myDeployJob:
  stage: deploy
  script:
    - echo "Running the deploy script"
```

## 4. Un exemple un peu plus concret

Dans les faits utiliser un `before_script` se rencontre surtout quand il s'agit de vérifier les pré-requis au lancement d'un job.

* Créer un nouveau fichier de pipeline

[< Previous](../exercice_0/exercice_0.md) | [Home](../README.md) | [Next >](../exercice_2/exercice_2.md)