# Pour effectuer des relations entre plusieurs tables sur Symfony : (Etape 1)

- symfony console make:entity
- Category
- No

(Adding some properties)

- titre
- 
- 
- 

## Creation d'une seconde entity : (Etape 2)

- symfony console make:entity
- Formation
- category
- relation
- Category
- ManyToOne
- no
- 
- 
- 

## Pour faire une migration où on peut revenir en arrière :

- symfony console doctrine:migrations:diff

Puis ensuite effectuer le migrate une fois que tout est vérifié
