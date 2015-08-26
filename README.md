# USER Bundle by ArdTeam

## Installation

Require the bundle in your composer.json file:

```
// composer.json
{
    // ...
    require:{
        // ...
        "ardteam/user-project" : "1.0.x@dev",
        "friendsofsymfony/user-bundle": "@dev"
    }
}
```

Register the bundle:

``` php
// app/AppKernel.php

public function registerBundles()
{
    return array(
        // ...
        new FOS\UserBundle\FOSUserBundle(),
        new AT\UserBundle\ATUserBundle(),
        // ...
    );
}
```

Install the bundle:

```
$ composer update
```

## Configuration

``` yaml
# Add to app/config/config.yml
doctrine:
    dbal:
        # ...
        types:
            json: Sonata\Doctrine\Types\JsonType

fos_user:
    db_driver: orm
    firewall_name: main
    user_class: AT\UserBundle\Entity\User
```

``` yaml
# Add to app/config/security.yml

security
    encoders:
        AT\UserBundle\Entity\User: sha512

    role_hierarchy:
        ROLE_ADMIN:       ROLE_USER
        ROLE_SUPER_ADMIN: [ROLE_USER, ROLE_ADMIN, ROLE_ALLOWED_TO_SWITCH]

    providers:
        fos_userbundle:
            id: fos_user.user_provider.username

    firewalls:
        # ...

        main:
           pattern: ^/
           anonymous:    true
           form_login:
               provider: fos_userbundle
               login_path: fos_user_security_login
               check_path: fos_user_security_check
           logout:
                path: fos_user_security_logout
                target: /admin/ # route après logout
            remember_me:
                key: %secret% # %secret% est un paramètre de parameters.yml

    access_control:
        # ...
        - { path: ^/login$, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/register, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/resetting, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/admin/, role: ROLE_ADMIN } # exemple de route à sécuriser
        # ...

```

Import FOSUser routes

``` yaml
# Add to app/config/routing.yml

# Sécurité & Connexion
fos_user:
    resource: "@FOSUserBundle/Resources/config/routing/all.xml"
```

## Usage

Create a user. For example :

```
$ php app/console fos:user:create <user_name> <user_email> <user_password> --super-admin
```