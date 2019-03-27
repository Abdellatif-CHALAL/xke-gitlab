# Services

Nous l'avons vu lors de l'exercice 2 il possible d'utiliser une image Docker au sein d'un job ou de manière globale 
sur tout un pipeline. Néanmoins dans certains cas il serait utile de disposer d'un ou plusieurs conteneurs accessibles 
par tous les jobs et ce tout au long du pipeline. L'accès à une base de données pendant les tests d'intégration est 
l'exemple le plus couramment donné.

Les `services` sont justement là pour répondre à cette problématique.

## 0. Utilisation des services avec un runner Kubernetes

Avoir recours à des services dans un pipeline exécuté avec un runner Kubernetes impose certaines limitations.
* Les alias ne sont pas utilisables, on ne peut donc pas définir plusieurs services identiques
* Accéder au service ne pourra se faire que sur `localhost` ou sur `127.0.0.1` car le runner déploie un unique pod.
Ces limitations sont à connaître lorsqu'il s'agit de rattacher un runner à votre pipeline car elles obligent à adapter votre code.

## 1. Déclarer un service dans un pipeline

* Créer un pipeline déclarant: 
    * Un service utilisant l'image mongo 
    * Un job `connect` avec la même image
* Modifier l'`entrypoint`du job et le faire pointer sur `/bin/sh`. 

> Au sein du job nous avons uniquement besoin du client Mongo. 
> Pour éviter que le serveur ne se lance nous remplaçons le point d'entrée de l'image par un simple appel au shell.  

* Afficher la liste des bases disponibles grâce au script suivant.

```javascript
db.adminCommand('listDatabases')
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

## 2.
    
[< Previous](../exercice_3) | [Home](..) | [Next >](../exercice_5)