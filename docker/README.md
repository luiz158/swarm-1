# Jenkins Slave Container with Docker

Deprecated: According to this article [Docker-in-Docker for your CI or testing environment? Think twice.](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) by Jérôme Petazzoni you will run into several problems when you integrate Docker into your CI-System with this image. I agree, because I ran into several performance issues using it.

Solution: Use my image that is reusing the socket of your Docker host: [blacklabelops/swarm-dockerhost](https://github.com/blacklabelops/swarm/tree/master/dockerhost)

Check this project on how to configure a swarm slave: [blacklabelops/jenkins-swarm](https://github.com/blacklabelops/jenkins-swarm)

# Make It Short!

In short, you can use Docker CLI commands inside a Jenkins slave.

First start a master!

~~~~
$ docker run -d -p 8090:8080 --name jenkins blacklabelops/jenkins
~~~~

> This will pull the my jenkins container ready with swarm plugin and ready-to-use!

Then start a Docker demon container!

~~~~
$ docker run -d --privileged --name docker_demon docker:1.9.1-dind
~~~~

> The swarm-slave does not run a docker demon itself! We use the official Docker image to create one for all slaves.

Now start the build slave!

~~~~
$ docker run -d \
    --link jenkins:jenkins \
    --link docker_demon:docker \
    blacklabelops/swarm-docker
~~~~

> CLI commands will be available.

# Docker Login

The slave can be started and login in a remote repository. The default is the dockerhub registry.

With the environment variables:

* DOCKER_REGISTRY_USER: Your account username for the registry. (mandatory)
* DOCKER_REGISTRY_EMAIL: Your account email for the registry. (mandatory)
* DOCKER_REGISTRY_PASSWORD: Your account password for the registry. (mandatory)

Example:

~~~~
$ docker run -d \
    --link jenkins:jenkins \
    --link docker_demon:docker \
    -e "DOCKER_REGISTRY_USER=**Your_Account_Username**" \
    -e "DOCKER_REGISTRY_EMAIL=**Your_Account_Email**" \
    -e "DOCKER_REGISTRY_PASSWORD=**Your_Account_Password**" \
    blacklabelops/swarm-docker
~~~~

> Will login the user to Dockerhub and save the credentials locally for repository pulls and pushes.

# Changing the Docker registry

The default for this container is dockerhub.io. If you want to use another remote repository, e.g. quay.io then Your_Account_Email can specify the repository with the environment variable DOCKER_REGISTRY.

Example:

~~~~
$ docker run -d \
    --link jenkins:jenkins \
    --link docker_demon:docker \
    -e "DOCKER_REGISTRY=quay.io"
    -e "DOCKER_REGISTRY_USER=**Your_Account_Username**" \
    -e "DOCKER_REGISTRY_EMAIL=**Your_Account_Email**" \
    -e "DOCKER_REGISTRY_PASSWORD=**Your_Account_Password**" \
    blacklabelops/swarm-docker
~~~~

> Will login to quay.io with the specified credentials.

# References

* [Docker Homepage](https://www.docker.com/)
* [Docker Userguide](https://docs.docker.com/userguide/)
