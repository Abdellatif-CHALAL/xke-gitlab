# Services

Dans certaines situations il est utile de disposer d'un ou plusieurs conteneurs accessibles 
par tous les jobs, tout au long du pipeline. Notamment pour disposer de "backing services" pour les tests d'intégration (bdd, redis, ...)

Les `services` sont justement là pour répondre à cette problématique.
Cette commande permet en effet de déployer un ou plusieurs conteneurs linkés (au sens docker du terme) au conteneur du job en cours. 

## Caveat: Utilisation des services avec un *runner Kubernetes*

Avoir recours à des services dans un pipeline exécuté avec un runner Kubernetes impose certaines limitations.
* Les alias ne sont pas utilisables, on ne peut donc pas définir plusieurs services identiques
* Accéder au service ne pourra se faire que sur `localhost` ou sur `127.0.0.1` car le runner ne déploie qu'un unique pod.

Ces limitations sont à connaître lorsqu'il s'agit de rattacher un runner à votre pipeline car elles obligent à adapter votre code.

## 1. Déclarer un service dans un pipeline

Ici, le but est de lancer un backing service `mongodb` puis de s'y se connecter pendant l'exécution du job.   

* Créer un pipeline déclarant : 
    * Un service utilisant l'image `mongo`. 
    * Un job `connect` avec la même image.
* Modifier l'`entrypoint` du job et le faire pointer sur `/bin/sh` 

> Au sein du job nous avons uniquement besoin du client Mongo. 
> Pour éviter que la base mongo ne se lance, nous remplaçons l' `entrypoint` de l' image par un simple lancement du shell.  

* Afficher la liste des bases disponibles grâce au script suivant :

```bash
mongo --host <host> --eval "db.adminCommand('listDatabases')"
```

<details>
<summary>Solution</summary>
<p>

```yaml
services:
  - mongo

connect:
  image: 
    name: mongo
    entrypoint: ["/bin/sh"] 
  script:
    - mongo --host 127.0.0.1 --eval "db.adminCommand('listDatabases')"
```

</p>
</details>

## 2. Utilisation des variables dans les services

Les variables déclarées dans le fichier `.gitlab-ci.yml` sont automatiquement passées dans les services.

* Modifier le lancement du service `mongodb` en initialisant l' utilisateur `root` :

```
MONGO_INITDB_ROOT_USERNAME: root
MONGO_INITDB_ROOT_PASSWORD: example
```
    
[< Previous](../exercice_3) | [Home](..) | [Next >](../exercice_5)