*************
Jenkins Setup
*************



Installing on Docker
====================
* https://github.com/jenkinsci/docker/blob/master/README.md

#. Set-up environment:

    .. code-block:: console

        $ mkdir -p /home/jenkins
        $ chmod 777 /home/jenkins
        $ chmod 666 /var/run/docker.sock
        $ ln -s /home/jenkins /var/jenkins_home

#. Run Docker container:

    .. code-block:: console

        $ docker run \
            --detach  \
            --name jenkins \
            --rm \
            --publish 8080:8080 \
            --volume /home/jenkins:/var/jenkins_home \
            --volume /var/run/docker.sock:/var/run/docker.sock \
            jenkinsci/blueocean

#. Get admin password:

    .. code-block:: console

        $ cat /home/jenkins/secrets/initialAdminPassword


Installing using Docker Compose
===============================
#. Create ``/home/jenkins.yaml``:

    .. code-block:: yaml
        :caption: ``jenkins.yaml``

        version: '3'

        networks:
            ecosystem:
                driver: bridge

        services:
            jenkins:
                image: jenkinsci/blueocean
                container_name: jenkins
                restart: "no"
                ports:
                    - "8080:8080"
                networks:
                    - ecosystem
                volumes:
                    - /home/jenkins:/var/jenkins_home/
                    - /var/run/docker.sock:/var/run/docker.sock

#. Run Jenkins

    .. code-block:: console

        $ cd /home/
        $ docker-compose -f jenkins.yaml up -d


Config
======
* https://github.com/jenkinsci/configuration-as-code-plugin
* https://www.jenkins.io/doc/book/managing/system-properties/
