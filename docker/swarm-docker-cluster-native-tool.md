#Background
As a newbie in docker field, after read some books and post. I've notied that
    docker container should be run in a cluster mode in production env. I've
    know some concepts like **vagrant**(a tool manage vm like virtualbox,
    vmware), **coreOS**(a linux-based OS which scaled and managed easily, by
    using sofware like **Etcd**, it can manage the nodes conviniently. It does
    not traditional package management mechanism. It is easy to upgraded to a
    new version and rool back to an old version if error occurs with a
    two-partition structure).


#Guide from docker official guide
    https://docs.docker.com/swarm/install-w-machine/
##Prepare the Cluster Env for the Experiment
    1. intall docker-machine
        - curl -L https://github.com/docker/machine/releases/download/v0.6.0/docker-machine-`uname -s`-`uname -m` > /usr/local/bin/docker-machine && \
        chmod +x /usr/local/bin/docker-machine
    2. create the vms which emulate the nodes in cluster:
        - docker-machine create -d virtualbox manager
        - docker-machine create -d virtualbox agent1
        - docker-machine create -d virtualbox agent2
##Create a Swarm dicovery token
    1. eval $(docker-machine env manager)
        - docker-machine env manager --> will show a bunch of bash env, which
            should be exported, then we can use docker to connect to **manager**
            vm. The vm named **manager** is created by docker-machine.
    2. docker run --rm swarm create
        - after the cmd in step 1(export env), the cmd in this step will send to
            docker daemon in vm **manager**. The docker cmd will run **swarm
            create** in the container in **manager** vm. This will makes the
            Swarm container connect to the Docker Hub discovery service and get
            a unique Swarm ID, also known as a "discovery token".
##Create the Swarm manager and nodes
    1. docker run -d -p 3376:3376 -t -v /var/lib/boot2docker:/certs:ro swarm manage -H 0.0.0.0:3376 --tlsverify --tlscacert=/certs/ca.pem --tlscert=/certs/server.pem --tlskey=/certs/server-key.pem token://0ac50ef75c9739f5bfeeaf00503d4e6e
        - start a container that functions as the primary manager on **manager**
            vm.
    2. eval $(docker-machine env agent1)
        - make docker client connect to docker daemon running in **agent1** vm
        docker run -d swarm join --addr=$(docker-machine ip agent1):2376 token://0ac50ef75c9739f5bfeeaf00503d4e6e
        - run **swarm join** in docker daemon in agent1
    3. similar to step. make agent2 join the cluster:
        eval $(docker-machine env agent2)
        docker run -d swarm join --addr=$(docker-machine ip agent2):2376 token://0ac50ef75c9739f5bfeeaf00503d4e6e
## manager your Swarm
    1. DOCKER_HOST=$(docker-machine ip manager):3376
        - this make the following docker cmd sent to **manager** node.
    2. docker info
    3. docker ps
    4. docker run hello-world
       docker ps
       docker run hello-world
       docker ps
        - swarm will assign the container in agent1 and agent2
