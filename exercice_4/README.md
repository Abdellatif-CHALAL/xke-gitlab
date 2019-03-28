# Services

Dans certains cas il serait utile de disposer d'un ou plusieurs conteneurs (`services`) accessibles 
par tous les jobs et ce tout au long du pipeline. Les conteneurs ainsi créés sont linkés (au sens docker) au conteneurs de jobs. 

Exemple d'utilisation : 
* Lancement de "backing services" pour les tests d'intégration (bdd, redis, ...)

Les `services` sont justement là pour répondre à cette problématique.

## Prérequis: Utilisation des services avec un *runner Kubernetes*

Avoir recours à des services dans un pipeline exécuté avec un runner Kubernetes impose certaines limitations.
* Les alias ne sont pas utilisables, on ne peut donc pas définir plusieurs services identiques
* Accéder au service ne pourra se faire que sur `localhost` ou sur `127.0.0.1` car le runner déploie un unique pod.

Ces limitations sont à connaître lorsqu'il s'agit de rattacher un runner à votre pipeline car elles obligent à adapter votre code.

## 1. Déclarer un service dans un pipeline

Ici, le but est de lancer un backing service `mongodb` et dans le job se connecter à ce service.   

* Créer un pipeline déclarant : 
    * Un service utilisant l'image `mongo` 
    * Un job `connect` avec la même image
* Modifier l'`entrypoint` du job et le faire pointer sur `/bin/sh` 

> Au sein du job nous avons uniquement besoin du client Mongo. 
> Pour éviter que lla base ne se lance nous, remplaçons le `entrypoint` de l'image par un simple lancement du shell.  

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

Les variables déclarés dans `.gitlab-ci.yml` sont automatiquement passés dans les services.

* Modifier le lancement de service precedente en lancant la base `mongodb` en initialisant l'utilisateur `root` :

```
MONGO_INITDB_ROOT_USERNAME: root
MONGO_INITDB_ROOT_PASSWORD: example
```
    
[< Previous](../exercice_3) | [Home](..) | [Next >](../exercice_5)