# Lancement d'un pipeline à partir de pipeline

Le but de l'exercise est d'executer un pipeline à partir de l'autre pipeline.

* Ici, nous lançerons automatiquement le pipeline `deploy` à partir de pipeline de `build` pour simuler un déploiement continu sur l'environnement de *developement* _(Attention : seule la branche master sera concernée)_.  
* Néanmoins, il n'est pas toujours opportun de faire du déployement continu vers l'environnement de *production*, nous souhaitons pouvoir décider d' arrêter à la fin de la *livraison continue*. 
* Pour ce faire, nous ajouterons la possibilité de lancer le même pipeline `deploy` de façon *manuelle* pour le déploiement vers l'environnement de *production* 


## 1. Lancement d'un pipeline `deploy` à partir de pipeline `build`

* Créer deux projets GitLab (nommés `myBuildPipeline` et `myDeployPipeline` par exemple) avec leurs fichiers `.gitlab-ci.yml` respectifs.
* Dans `myBuildPipeline` : 
    * Créer un job `myBuild`, attaché à un stage `build`.
    * Créer un job `myDeployTrigger:dev`, attaché le job au stage `deploy`.
        * Utiliser l'image `byrnedo/alpine-curl` pour disposer de `curl`.
        * Limiter le lancement du job à la branche `master` (utiliser l'instruction `only:`).
* Dans le projet `myDeployPipeline` : 
    * Créer un job `myDeploy:dev`. 
    * Récupérer l'ID du projet. Cet ID sera utilisé plus tard par le trigger.

<p>
<img src="project-id.png" height="100">
</p> 

    *. Créer un nouveau trigger (see [documentation](https://docs.gitlab.com/ce/ci/triggers/README.html#ci-job-token))
    
<p>
<img src="ci-cd-settings.png" height="200">
</p> 

<p>
<img src="trigger.png" height="300">
</p> 

    * Utiliser les instructions pour compléter le job `myDeployTrigger:dev` du projet `myBuildPipeline`


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
    image: byrnedo/alpine-curl
    script:   
        - curl --request POST --form "token=$CI_JOB_TOKEN" --form ref=master https://gitlab.sab2i-cloud.com/api/v4/projects/<myDeployPipeline_project_id>/trigger/pipeline
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


## 2. Les jobs manuels

* TODO

[< Previous](../exercice_4) | [Home](..) | [Next >](../exercice_6)