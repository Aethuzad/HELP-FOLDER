# Creation Projet avec user pas à pas

## Création du projet

- composer create-project symfony/website-skeleton nom_projet
ou
- symfony new --webapp my_project
## Éditez votre fichier .env pour y intégrer la BDD

- ```DATABASE_URL="mysql://login:password@127.0.0.1:3306/dbname"```
- Or if you use MariaDB you uncomment the line with MariaDB don't forget the VERSION of your DB

## Création du user

- ```php bin/console make:user```

## Ajout des champs au user

- ```php bin/console make:entity```
-> User

## Ajout de l'authentification

- ```php bin/console make:auth```
-> Empty or full Auth -> 1
-> Name - > Authenticator (Choice)
-> Name of the controller -> SecurityController (Choice)
-> Logout system -> Yes
-> Remember Me system -> Yes or No (Doesn't work perfectly)
-> Checkbox only if you had activated the previous choice -> 0 or 1 depends on you

## Ajout du crud user

- ```php bin/console make:crud```
-> Classname of the entity to create CRUD -> User

## Ajout du registration form

- ```php bin/console make:registration-form```
-> Unique entity -> Yes
-> Email -> No (Until you have a server with a DNS Mailer)
-> Authentificate the user auto after registration -> Yes (or No, it depends on you)

## Création de la BDD

- ```php bin/console doctrine:database:create```

## Création des autre entities pour mon projet

## Création de leurs crud

## Migrations

- ```php bin/console make:migration```
- ```php bin/console doctrine:migrations:migrate```

## Création d'une landing page : Home

- je crée un nouveau controller : Home
- ```php bin/console make:controller```
-> HomeController
- Je dois modifier sa route pour qu'elle corresponde à la landing page
- ```@Route("/home", name="app_home")```
- devient :
- ```@Route("/", name="app_home")```
- je peux aussi effectuer une redirection vers un autre controller ( cf UserController new )

## Suppression du user new inutil car remplacé par le register

- Dans mon UserController dans la methode new je remplace :

<code><pre>
    /**
     * @Route("/new", name="user_new", methods={"GET","POST"})
     */
    public function new(Request $request): Response
    {
        $user = new User();
        $form = $this->createForm(UserType::class, $user);
        $form->handleRequest($request);

        if ($form->isSubmitted() && $form->isValid()) {
            $entityManager = $this->getDoctrine()->getManager();
            $entityManager->persist($user);
            $entityManager->flush();

            return $this->redirectToRoute('user_index');
        }

        return $this->render('user/new.html.twig', [
            'user' => $user,
            'form' => $form->createView(),
        ]);
    }
</pre></code>

- une redirection vers register
<code><pre>
     
    public function new(Request $request): Response
    {
        return $this->redirectToRoute('app_register');
    }
</pre></code>

## RegistrationFormType : ajout des elements de formulaire qui me manque(ex:fistName,lastName...)  

- ```->add('firstName')```

## Confirmation du password

- form\RegistrationFormType.php
- ajout d'un nouveau champ confirmPassword copier/coller du plainPassword :
<code><pre>
->add('confirmPassword', PasswordType::class, [
    // instead of being set onto the object directly,
    // this is read and encoded in the controller
    'mapped' => false,
    'attr' => ['autocomplete' => 'new-password'],
    'constraints' => [
        new NotBlank([
            'message' => 'Please enter a password',
        ]),
        new Length([
            'min' => 6,
            'minMessage' => 'Your password should be at least {{ limit }} characters',
            // max length allowed by Symfony for security reasons
            'max' => 4096,
        ]),
    ],
])
</pre></code>

- j'effectue la comparaison dans mon RegistrationController :
- apres validation du formulaire je crée une condition pour comparer plainPassword et confirmPassword :

 if($form->get('plainPassword')->getData() === $form->get('confirmPassword')->getData() ){ ...

 - dans laquelle j'integre mon ash de plainpassword et l'enregistrement (persist et flush)

 } else {

- je renvoie mon utilisateur vers le formulaire avec une nouvelle variable 'passError' :

    return $this->render('registration/register.html.twig', [
                    'registrationForm' => $form->createView(),
                    'passError' => 'Les mots de pass ne sont pas identiques'
        ]);
 }


- dans la partie view (register.html.twig) j'affiche passError à l'utilisateur en le testant :

{% block body %}
    {% if passError is defined %}
        <div class="alert alert-danger" role="alert">{{ passError }}</div>
{% endif %}

## \Security\Authenticator.php effectuer la redirection après inscription :

<code><pre>
// For example : return new RedirectResponse($this->urlGenerator->generate('some_route'));
        throw new \Exception('TODO: provide a valid redirect inside '.__FILE__);
</pre></code>

- a remplacer par :
 return new RedirectResponse($this->urlGenerator->generate('app_home'));
        // throw new \Exception('TODO: provide a valid redirect inside '.__FILE__);

<!-- ## Automatiser le ROLE_USER lors de l'enregistrement d'un nouvel utilisateur : 

- RegistrationController :
    - juste après la validation :
    ```if ($form->isSubmitted() && $form->isValid()) {```
    - ```$user->setRoles(['ROLE_USER']);``` n'oubliez pas le type array [] -->

## Ajout d'un champ personnalisé dans la vue (template) registrationForm :

- {{ form_row(registrationForm.confirmPassword, {
            label: 'Confirm Password'
        }) }}
- ou
- {{ form_row(registrationForm.firstName) }}




## Installation du reset password UPDATE ne fonctionne pas en local ou définir une adresse mail dans le php.ini
 - ```composer require symfonycasts/reset-password-bundle```
 - ```php bin/console make:reset-password```

 - dans le .env il faudra ensuite configurer le MAILER_DSN= 

 