```mermaid
flowchart LR
    subgraph P["1. Provisionner (infra)"]
        V["Vagrant + VirtualBox<br/>VM Ubuntu locale<br/>127.0.0.1:2222"]
        T["Terraform + AWS<br/>EC2 Ubuntu<br/>SG 22 / 8080 / 8081"]
    end
    subgraph C["2. Configurer (Ansible - site.yml)"]
        A1["rôle docker_host<br/>apt docker.io, compose, git<br/>service docker"]
        A2["rôle voting_app<br/>git clone<br/>docker compose up"]
    end
    subgraph E["3. Exécuter (Docker Compose)"]
        D["vote :8080<br/>result :8081<br/>redis, db, worker"]
    end
    V -- "inventories/local.yml<br/>(écrit à la main)" --> A1
    T -- "inventories/aws.yml<br/>(généré par Terraform)" --> A1
    A1 --> A2 --> D
```

# comptoirs-frontend

Ce projet est un exemple de frontend vue.js sur le backend "comptoirs" [déployé sur heroku](https://springajax.herokuapp.com/).

## Configuration du frontend

### Accès au backend

On [configure un "proxy"](./vite.config.js) pour l'accès au backend, on peut appeler l'api à l'url ```/api/```, la redirection sera faite automatiquement.

### Appel de l'API REST

On définit [une fonction utilitaire pour appeler l'API](./src/api.js), qui facilite l'appel des services REST et permet une meilleure gestion des erreurs côté frontend.

### Vue Router

On utilise le [View router](./router.md) pour gérer la navigation entre les composants Vue.

### Un exemple de composant Vue qui fait un appel à l'API REST

[Le composant qui affiche les catégories](./src/views/CategorieView.vue)

## Installation des dépendances pour le frontend

```sh
npm install
```

### Compilation et exécution pour le développement

```sh
npm run dev
```

### Compilation pour la production

```sh
npm run build
```
