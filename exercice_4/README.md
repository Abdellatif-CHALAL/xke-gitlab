# Services

Dans certains cas initialiser des resources est coûteux. 
Il est alors utile de mutualiser et de disposer de "backing services" comme ceux requis pour les tests d'intégration (bdd, redis, ...) par exemple.

Dans Gitlab CI, les `services` permettent justement de créer des conteneurs mutualisés. 
Cette commande permet en effet de déployer un ou plusieurs conteneurs linkés (au sens docker du terme) au conteneur du job en cours.
Ces conteneurs deviennent alors accessibles à tous les jobs, tout au long du pipeline.


## 1. Déclarer un service dans un pipeline

Ici, le but est de lancer un backing service `mongodb` puis de s'y se connecter pendant l'exécution du job.   

* Créer un pipeline déclarant : 
    * Un service utilisant l'image `mongo`. 
    * Un job `connect` avec la même image.
* Modifier l'`entrypoint` du job afin qu'il ne lance rien (vide) 

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
    entrypoint: [""] 
  script:
    - mongo --host mongo --eval "db.adminCommand('listDatabases')"
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

* Vérifier qu' il faut à présent donner cet utilisateur au client mongo

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
    - mongo --host mongo --user --password --eval "db.adminCommand('listDatabases')"
```

</p>
</details>

[< Previous](../exercice_3) | [Home](..) | [Next >](../exercice_5)
