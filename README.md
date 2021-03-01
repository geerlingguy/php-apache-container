# PHP Apache Container (Built with Ansible)

[![CI](https://github.com/geerlingguy/php-apache-container/workflows/Build/badge.svg?branch=master&event=push)](https://github.com/geerlingguy/php-apache-container/actions?query=workflow%3ABuild) [![Docker pulls](https://img.shields.io/docker/pulls/geerlingguy/php-apache)](https://hub.docker.com/r/geerlingguy/php-apache/)

This project is composed of three main parts:

  - **Ansible project**: This project is maintained on GitHub: [geerlingguy/php-apache-container](https://github.com/geerlingguy/php-apache-container). Please file issues, support requests, etc. against this GitHub repository.
  - **Docker Hub Image**: If you just want to use [the `geerlingguy/php-apache` Docker image](https://hub.docker.com/r/geerlingguy/php-apache/) in your project, you can pull it from Docker Hub.
  - **Ansible Role**: If you need a flexible Ansible role that's compatible with both traditional servers and containerized builds, check out [`geerlingguy.php`](https://galaxy.ansible.com/geerlingguy/php/) on Ansible Galaxy. (This is the Ansible role that does the bulk of the work in managing the PHP container.)

## Versions

Currently maintained versions include:

  - `7.4`, `7.4.x`, `latest`: PHP 7.4.x
  - `7.3`, `7.3.x`: PHP 7.3.x
  - `7.2`, `7.2.x`: PHP 7.2.x
  - `7.1`, `7.1.x`: PHP 7.1.x

## Standalone Usage

If you want to use the `geerlingguy/php-apache` image from Docker Hub, you don't need to install or use this project at all. You can quickly build a PHP container locally with:

    docker run -d --name=php-apache -p 80:80 geerlingguy/php-apache:latest /usr/sbin/apache2ctl -D FOREGROUND

You can also wrap up that configuration in a `Dockerfile` and/or a `docker-compose.yml` file if you want to keep things simple. For example:

    version: "3"
    
    services:
      php-apache:
        image: geerlingguy/php-apache:latest
        container_name: php-apache
        ports:
          - "80:80"
        restart: always
        # See 'Custom PHP codebase' for instructions for volumes.
        volumes: []

Then run:

    docker-compose up -d

Now you should be able to access the default home page at `http://localhost/`.

### Custom PHP codebase

If you have a codebase inside the folder `web`, mount it as a volume like `-v ./web:/var/www/html:rw,delegated`.

Or, if using a Docker Compose file:

    services:
      myapp:
        ...
        volumes:
          - ./web:/var/www/html:rw,delegated

If you wish to build an image using this image as the base (e.g. for deploying to production), create a Dockerfile and `COPY` the webroot into place so it's part of the image.

If you want to run multiple webroots, or need to further customize the Apache VirtualHost definitions, you can mount a config file over the existing one in the container, e.g.:

    services:
      myapp:
        ...
        volumes:
          - ./web:/var/www/html:rw,delegated
          - ./virtualhosts.conf:/etc/apache2/sites-enabled/vhosts.conf:rw

Similarly, you can mount a PHP config file to the path `/etc/php/7.4/apache2/php.ini` (substitute whatever PHP version you're currently using in that path).

## Management with Ansible

### Prerequisites

Before using this project to build and maintain PHP images for Docker, you need to have the following installed:

  - [Docker Community Edition](https://docs.docker.com/engine/installation/) (for Mac, Windows, or Linux)
  - [Ansible](http://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

### Build the image

First, install Ansible role requirements:

    ansible-galaxy install -r requirements.yml

Then, make sure Docker is running, and run the playbook to build the container:

    ansible-playbook --extra-vars="@vars/7.4.yml" main.yml

(Substitute whatever supported PHP version you desire in the vars path) Once the image is built, you can run `docker images` to see the `php-apache` image that was generated.

> Note: If you get an error like `Failed to import docker`, run `pip install docker`.

### Push the image to Docker Hub

See the `.github/workflows/build.yml` file in this repository for how it pushes all the tagged images automatically on any commit to the `master` branch.

## License

MIT / BSD

## Author Information

This container build was created in 2018 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
