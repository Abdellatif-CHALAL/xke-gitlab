# Lancement d'un pipeline à partir de pipeline

Le but de l'exercise est d'executer un pipeline à partir de l'autre pipeline.

* Ici, nous lançerons automatiquement le pipeline `deploy` à partir de pipeline de `build` pour simuler un déploiement continu sur l'environnement de *developement* _(Attention : seule la branche master sera concernée)_.  
* Néanmoins, il n'est pas toujours opportun de faire du déployement continu vers l'environnement de *production*, nous souhaitons pouvoir décider d' arrêter à la fin de la *livraison continue*. 
* Pour ce faire, nous ajouterons la possibilité de lancer le même pipeline `deploy` de façon *manuelle* pour le déploiement vers l'environnement de *production* 


## 1. Lancement d'un pipeline `deploy` à partir de pipeline `build`

* Créer deux projets GitLab (nommés `myBuildPipeline` et `myDeployPipeline` par exemple) avec leurs fichiers `.gitlab-ci.yml` respectifs.
* Créer un job `myBuild` dans le pipeline de `myBuildPipeline`, attaché à un stage `build`.
* Créer un job `myDeploy:dev` dans le pipeline de `myBuildPipeline`, attaché à un stage `deploy`.
* Préciser que le job `myDeploy:dev` ne se lance que sur la branche `master` (utiliser l'instruction `only:`).
* Dans le projet `myDeployPipeline` lancer le pipeline `myDeploy:dev` (see [documentation](https://docs.gitlab.com/ce/ci/triggers/README.html#ci-job-token))
    *. Il est nécessaire de trouver l'ID de projet `myDeployPipeline` pour pouvoir le déclencher par API.

<p>
<img src="project-id.png" height="100">
</p> 

    *. Puis dans le pipeline `myDeployPipeline` créer un nouveau trigger :
    
<p>
<img src="ci-cd-settings.png" height="200">
</p> 

<p>
<img src="trigger.png" height="300">
</p> 

    * Utiliser les instructions pour decléncher le pipeline


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
        - curl --request POST --form "token=$CI_JOB_TOKEN" --form ref=master https://gitlab.sab2i-cloud.com/api/v4/projects/<myDeployPipeline_project_id>/trigger/pipeline
    only:
        - master
```

</p>
</details>


## 2. Les jobs manuels

* TODO

[< Previous](../exercice_4) | [Home](..) | [Next >](../exercice_6)