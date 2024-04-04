***********
Artifactory
***********


Installation
============

From Docker
-----------
.. code-block:: console

    $ docker run \
        --name artifactory \
        --restart always \
        --rm \
        --detach \
        --publish 8081:8081 \
        docker.bintray.io/jfrog/artifactory-oss:latest



