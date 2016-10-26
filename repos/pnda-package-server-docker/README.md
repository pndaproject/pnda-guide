# PNDA Package Server Docker

When deploying a PNDA cluster, compiled components are downloaded from a package server. You can either use an existing package server, or build your own. 

This repository contains scripts that can build a fully functional package server. Specifically, the scripts create a Docker container, install the necessary build tools, clone the source code repositories, compile the source code into packages, and make the packages available from a web server. 

## Requirements

The scripts are designed to run inside a [Docker](https://www.docker.com) container. See the instructions for [installing Docker](https://docs.docker.com/engine/installation).

The scripts were tested using a host machine running [Ubuntu](http://www.ubuntu.com)

1. 14.04 LTS with 8 GB of memory & Docker version 1.8.3
2. 16.04 LTS with 10 GB of memory & Docker version 1.12.1, build 23cf638

A minumum of 10Gb disk space is recommended.

The scripts may work in other UNIX environments as well. See the [Questions & Answers](http://pndaproject.io/qa) site for further information.

## Pull in the repo

Before you can build the package server container, you must clone the repository onto your host machine:

`git clone https://github.com/pndaproject/pnda-package-server-docker`

Now change directory into `pnda-package-server-docker`

## Building the package server container

TODO: push the docker images to a registry.

The Docker container uses [Ubuntu](http://www.ubuntu.com) 14.04. The default credentials are `root/ubuntu`.

1. Build the package server docker images.

    ```
    sudo docker build -t pnda/packageserver -f Dockerfile .
    ```

2. Launch the package server.
	
    ```
    sudo docker run -d -v $PWD/html:/var/www/html -p 2222:22 -p 80:80 --name packageserver pnda/packageserver
    ```

    This will forward port 80 (the default HTTP port) on the host machine to the Docker container. Substitute a different port number (the first `80`) if you want to run the web server on a different port. 

	If you would like to make a backup copy of the compiled packages, you can mount the `/var/www/html` directory in the host environment. In that case, make a folder called `html` in the `pnda-package-server-docker` folder. 
    
    It is not necessary to mount the volume `/var/www/html`. However, if you would like to keep a copy of the compiled packages on the host computer, Just in case you would like to have the binaries on the host if you wanted to have a backup (if the container crash etc...). I you want to mount it, do not forget to create the folder html in pnda-package-server-docker folder

3. Connect to the running package server container, and build all the binaries.

	```
    ssh root@localhost -p 2222 # password is ubuntu
	export GITHUB_ENDPOINT=https://github.com/pndaproject
	export BRANCH=master
	/tmp/build.sh
    ```
	
    The `GITHUB_ENDPOINT` environment variable should be set to the URL of the PNDA GitHub repository that you want to use. The public PNDA repository is [https://github.com/pndaproject](https://github.com/pndaproject), but if you can also use your own fork of PNDA. If you are using a private repository, you may need to set up access: 
    
		a. If you have an ssh enpoint (e.g. user@pnda.domain.com:Team), you will need to add your private key in `/root/.ssh/id_rsa`. 
		b. If you have an HTTPS endpoint, you will need to enter your credentials. 

    The `BRANCH` environment variable should be set to the branch that you want to use. The default is `master`, but if you would like to pull from a different branch then you can set the name of the branch here. Note that the branch must exist in all the repositories whose components will be compiled. 

4. Remove the existing running package server container.

	```
    $ sudo docker rm $(sudo docker ps -a | grep packageserver | awk '{print $1}')
    ```

## Working with the package server container

Once you have built the package server container, you may want to make a backup so that you can deploy it later. These instructions will help you make a snapshot image.

1. Get the list of running containers:

    ```
    sudo docker ps

	CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                 PORTS                                      NAMES
	2ec50e9f0580        pnda/packageserver   "/usr/bin/supervisord"   34 hours ago        Up 34 hours   0.0.0.0:80->80/tcp, 0.0.0.0:2222->22/tcp   packageserver
    ```

2. Commit the running container using the ID:

	```
    sudo docker commit 2ec50e9f0580 pnda/packageserver:v1.2.3
    ```

	where `v1.2.3` is the new docker image version you want to create.

3. Save the image locally so thaty you can copy it somewhere else:
    
    ```	
    sudo docker save -o ./pnda_package_server_v1.2.3 pnda/packageserver:v1.2.3
	scp ./pnda_package_server_v1.2.3 ubuntu@XXXXXXX:/home/ubuntu
    ```

4. On the other host, load the Docker image, check that it has been loaded and run it:

    ```	
    sudo docker load -i ./pnda_package_server_v1.2.3
	sudo docker images 
	sudo docker run -d -p 2222:22 -p 80:80 --name packageserver pnda/packageserver
    ```
