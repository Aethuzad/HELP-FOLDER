# Récupération d'un projet symfony :

- Forker le projet en prenant en compte de récupérer toutes les branches
- Cloner le projet
- Recréer le .env d'origine
- Faire un composer install
- Faire un composer update
- Recréer la database avec doctrine:database:create
- Supprimer les migrations
- Puis refaire une migration avec symfony console make:migration
- Puis finaliser la migration avec symfony console doctrine:migrations:migrate

## Installation de Easyadmin

- composer require easycorp/easyadmin-bundle

## Pour remplir la database avec des données utilisant Faker et Fixtures

- symfony console doctrine:fixtures:load

## Pour la pagination on utilise : knplabs

- composer require knplabs/knp-paginator-bundle

## Pour revenir à une version précédente : 

- symfony console doctrine:migrations:rollup

Ne pas oublier de supprimer toutes les versions aussi bien sur VSCODE que sur la table doctrine_migrations_versions

## Pour voir la difference entre les versions : 

- symfony console doctrine:migrations:diff