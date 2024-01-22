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


