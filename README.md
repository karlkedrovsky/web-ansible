# Docker Setup For Static Site Development Using Ansible.

This is meant to be a starter template for setting up docker containers for running a static site. It uses Ansible to create the containers instead of docker-compose.

The containers used are from [Wodby](https://wodby.com/) and the following referencess are helpful when configuring the playbook.

* [docker-compose.yml](https://github.com/wodby/docker4drupal/blob/master/docker-compose.yml) for container configuration.
* [Available environment variables for configuration](https://github.com/wodby/docker4drupal/blob/master/.env)
* [Wodby Drupal stack docs](https://wodby.com/docs/1.0/stacks/drupal/)

## Notes

The docroot of the site is the "site" directory.

The settings for the environment variables referenced above are kept in the vars.yml file.

## Usage

Copy/fork this repo, update the "PROJECT_NAME" variable in the "vars" file and run:

    ansible-playbook -i inventory.bolivar --connection=local playbook.yml

Obviously using a different inventory file and potentially not specifying the "--connection" option if you're not creating the containers on your local machine that is named "bolivar".

To install a fresh copy of Drupal:

    docker exec -it PROJECT_NAME-php /bin/bash
    composer create-project drupal/recommended-project .

Then visit the site and complete the site setup.

This setup assumes you've already set up the [traefik container](https://github.com/karlkedrovsky/traefik-ansible) on the machine to handle inbound traffic.

The Lets Encrypt integration assumes that your computer is accessible from the internet and port 80 is accepting requests for any sub-domain ending in bolivar.kedrovsky.com.

## Removing The Containers

If there's ever a need to stop and remove the containers just run:

    ansible-playbook -i inventory.bolivar --connection=local playbook-remove.yml

## Basic Auth

Generate a password using:

    openssl passwd -apr1

Update the basicauth and middleware labels in the web server config of the playbook with the new user and password. The default user/password in the playbook is web/web.

To remove the basic auth just remove the these two labels

    "traefik.http.middlewares.{{ PROJECT_NAME }}-auth.basicauth.users": "web:$apr1$6Zpn5i48$2c1a/YEZYW1TJS.iuTpmP/",
    "traefik.http.routers.{{ PROJECT_NAME }}.middlewares": "{{ PROJECT_NAME }}-auth"
