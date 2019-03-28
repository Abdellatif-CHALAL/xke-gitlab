# Lancement d'un pipeline à partir de pipeline

Le but de l'exercise est d'executer un pipeline à partir de l'autre pipeline.

* Ici, nous lançerons automatiquement le pipeline `deploy` à partir de pipeline de `build` pour la simuler le deployement continue sur l'environement de *developement* _(Attention : on ne fera ça que pour le branch master)_  
* Or, il n'est pas toujours opportune de faire le demplyement continue vers l'environement de *production*, om on choisi souvent de s'arrêter sur la *livraison continue* 
* Pour cela, dans le second temps on ajoutera une possibilité de lancer le même pipeline `deploy` de façon *manuel* pour le deployement vers l'environement de *production* 


## 1. Lancement d'un pipeline `deploy` à partir de pipeline `build`

* Créer 2 projets GitLab (nommés `myBuildPipeline` et `myDeployPipeline` par exemple) avec leurs fichiers `.gitlab-ci.yml` respectives
* Créer un job `myBuild` dans le pipeline `myBuildPipeline` attaché à un stage `build`
* Créer un job `myDeploy:dev` dans le pipeline `myBuildPipeline` attaché à un stage `deploy`
* Preciser que le job `myDeploy:dev` ne se lance que sur la branche `master` (utiliser l'instruction `only:`)
* Dans le job `myDeploy:dev` lancer le pipeline `myDeployPipeline` (see [documentation](https://docs.gitlab.com/ee/ci/triggers/README.html#ci-job-token))
    *. Il est nécessaire de trouver l'ID de projet `myDeployPipeline` pour le declancher par APIs :

<p>
<img src="project-id.png" height="100">
</p> 

    *. Puis dans le pipeline `myDeployPipeline` créer un nouveau triggeur :
    
<p>
<img src="ci-cd-settings.png" height="200">
</p> 

<p>
<img src="trigger.png" height="300">
</p> 

    * Utiliser les instructions pour declancher le pipeline


<details>
<summary>Solution</summary>
<p>

```yaml

myBuild:
    stage: build
    script:
        - echo "Build"
    
myDeploy:dev:
    stage: deploy
    image: byrnedo/alpine-curl
    script:   
        - curl --request POST --form "token=$CI_JOB_TOKEN" --form ref=master https://gitlab.sab2i-cloud.com/api/v4/projects/97/trigger/pipeline
    only:
        - master
```

</p>
</details>


## 2. Les jobs manuels

* TODO

[< Previous](../exercice_4) | [Home](..) | [Next >](../exercice_6)