# Lancement d'un pipeline à partir d'un autre pipeline

Le but de cet exercise sera de montrer comment déclencher un pipeline à partir d'un autre pipeline et donc de chaîner les pipelines.

* Ici, nous lançerons automatiquement le pipeline `deploy` à partir du pipeline `build` afin de simuler un déploiement continu sur l'environnement de *dévelopement* _(Attention : seule la branche master sera concernée)_.  
* Néanmoins, dans l'environnement de *production*, nous souhaiterions pouvoir valider les artéfacts avant de continuer la livraison. 
* Pour ce faire, nous ajouterons la possibilité de lancer *manuellement* le pipeline `deploy` lorsqu' il s'agira de l' environnement de *production* 

## 1. Lancement d'un pipeline `deploy` à partir de pipeline `build`

* Créer deux projets GitLab (nommés `myBuildPipeline` et `myDeployPipeline` par exemple) avec leurs fichiers `.gitlab-ci.yml` respectifs.
* Dans `myBuildPipeline` : 
    * Créer un job `myBuild`, attaché à un stage `build`.
    * Créer un job `myDeployTrigger:dev`, attaché le job au stage `deploy`.
        * Utiliser l'image `psoders/docker-alpine-curl` pour disposer de `curl`.
        * Limiter le lancement du job à la branche `master` (utiliser l'instruction `only:`).
* Dans le projet `myDeployPipeline` : 
    * Créer un job `myDeploy:dev`. 
    * Créer un nouveau trigger (voir [documentation](https://docs.gitlab.com/ce/ci/triggers/README.html#ci-job-token))
    * Récupérer l'ID du projet. Cet ID sera utilisé plus tard par le trigger.

<p>
<img src="project-id.png" height="100">
</p> 

<p>
<img src="ci-cd-settings.png" height="200">
</p> 

<p>
<img src="trigger.png" height="300">
</p> 

* Utiliser les instructions pour compléter le job `myDeployTrigger:dev` du projet `myBuildPipeline`.
* Vérifier que dans l'interface web que les deux pipelines affichent le déclenchement du trigger.

<details>
<summary>Solution</summary>
<p>

```yaml

myBuild:
    stage: build
    script:
        - echo "Build"
    
myDeployTrigger:dev:
    stage: deploy
    image: psoders/docker-alpine-curl
    script:   
        - curl --request POST --form "token=$CI_JOB_TOKEN" --form ref=master https://gitlab.com/api/v4/projects/<myDeployPipeline_project_id>/trigger/pipeline
    only:
        - master
```

```yaml

myDeploy:dev:
    stage: deploy
    script:
        - echo "Deploy"
```

</p>
</details>

## 2. Enrichissement du trigger

Un trigger ne sert pas uniquement à déclencher le lancement d'un autre pipeline. 
*Il peut être transmis avec d'autres variables qui seront ensuite utilisées dans le pipeline cible.*

* Modifier le pipeline du projet `myBuildPipeline`
    * Ajouter une variable `MAKE_PACKAGE` au trigger, valeur `true` (voir [documentation](https://docs.gitlab.com/ce/ci/triggers/#making-use-of-trigger-variables))
* Modifier le pipeline du projet `myDeployPipeline`
    * Ajouter les stages `package` et `deploy`
    * Ajouter le job `myPackage:dev`, stage `package`
    * Ajouter une condition sur `myPackage:dev` pour qu'il ne se déclenche que si `MAKE_PACKAGE=true` (voir [documentation](https://docs.gitlab.com/ce/ci/yaml/README.html#onlyvariablesexceptvariables))   

<details>
<summary>Solution</summary>
<p>

```yaml
myBuild:
    stage: build
    script:
        - echo "Build"
    
myDeployTrigger:dev:
    stage: deploy
    image: psoders/docker-alpine-curl
    script:   
        - curl --request POST --form "token=$CI_JOB_TOKEN" --form ref=master --form "variables[MAKE_PACKAGE]=true" https://gitlab.com/api/v4/projects/11802990/trigger/pipeline
    only:
        - master
```

```yaml
stages:
    - package
    - deploy

myPackage:dev:
    stage: package
    script:
        - echo "Package"
    only:
        variables:
            - $MAKE_PACKAGE == "true"
             
myDeploy:dev:
    stage: deploy
    script:
        - echo "Deploy"
```

</p>
</details>

## 3. Trigger et jobs manuels

Comme énoncé au début de l'exercice faire du déploiement continu n'est pas forcément souhaité à tout instant.
Cela peut dépendre de l'environement cible, on peut choisir de privilégier un build plutôt qu'un autre ou encore vouloir attendre qu'une campagne de tests se termine sur un environement.
Bref, les raisons ne manquent pas et nous allons donc à présent voir comment mettre temporairement en pause un pipeline.  

* Modifier le pipeline du projet `myDeployPipeline`
    * Dans le job `myDeploy:dev` ajouter une condition `when` (voir [documentation](https://docs.gitlab.com/ce/ci/yaml/README.html#when))
    
| NB: Définir une action manuelle bloquante oblige de rajouter `allow_failure: false` sans quoi le pipeline continuerait de s'exécuter.

* Vérifier que le pipeline est effectivement bloqué
* Relancer le pipeline en amont
* Vérifier que le pipeline est aussi bloqué lors d'une déclenchement par trigger

<details>
<summary>Solution</summary>
<p>

```yaml
stages:
    - package
    - deploy

myPackage:dev:
    stage: package
    script:
        - echo "Package"
    only:
        variables:
            - $MAKE_PACKAGE == "true"
             
myDeploy:dev:
    stage: deploy
    script:
        - echo "Deploy"
    when: manual
    allow_failure: false
```

</p>
</details>

[< Previous](../exercice_4) | [Home](..) | [Next >](../exercice_6)