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

## Usercontroller : Suppression du user new inutile car remplacé par le register 

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

- PAR UNE REDIRECTION VERS REGISTER

<code><pre>
     
    public function new(Request $request): Response
    {
        return $this->redirectToRoute('app_register');
    }
</pre></code>

## RegistrationFormType : ajout des elements de formulaire qui me manque(ex:fistName,lastName...)  

- ```->add('firstName')``` (Ou le nom de l'entité que vous avez donné. Exemple : ->add('nom'))


## \Security\Authenticator.php : effectuer la redirection après inscription :

<code><pre>
// For example : return new RedirectResponse($this->urlGenerator->generate('some_route'));
        throw new \Exception('TODO: provide a valid redirect inside '.__FILE__);              
</pre></code>

- A REMPLACER PAR :

 return new RedirectResponse($this->urlGenerator->generate('app_home'));
        // throw new \Exception('TODO: provide a valid redirect inside '.__FILE__);

<!-- ## Automatiser le ROLE_USER lors de l'enregistrement d'un nouvel utilisateur : 

- RegistrationController :
    - juste après la validation :
    ```if ($form->isSubmitted() && $form->isValid()) {```
    - ```$user->setRoles(['ROLE_USER']);``` n'oubliez pas le type array [] -->

## Confirmation du password

- form\RegistrationFormType.php
- remplacement du champ plainPassword :
<code><pre>
->add('password', RepeatedType::class, [
                'type' => PasswordType::class,
                'invalid_message' => 'The password fields must match.',
                'options' => ['attr' => ['class' => 'password-field']],
                'required' => true,
                'first_options'  => ['label' => 'Password'],
                'second_options' => ['label' => 'Repeat Password'],
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
                ]
            ])
</pre></code>

- ajout du use pour le RepeatedType :
```use Symfony\Component\Form\Extension\Core\Type\RepeatedType;```

- emplacement des plainPassword par password dans RegistrationController

## Ajout d'un champ personnalisé dans la vue (template) registrationForm :



- suppression dans le twig de : ```{{ form_row(registrationForm.plainPassword) }}```
- et ajout dans le meme fichier de  :
```{{ form_row(registrationForm.password.first) }}{{ form_row(registrationForm.password.second) }}```




<!-- ## Installation du reset password UPDATE ne fonctionne pas en local ou définir une adresse mail dans le php.ini
 - ```composer require symfonycasts/reset-password-bundle```
 - ```php bin/console make:reset-password```

 - dans le .env il faudra ensuite configurer le MAILER_DSN=  -->

 