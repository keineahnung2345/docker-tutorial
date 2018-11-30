# Swarm

## Create a couple of VMs using docker-machine, using the VirtualBox driver
```sh
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2
```
It will take some time to complete.
Its output:
<pre>
Running pre-create checks...
(myvm1) Unable to get the local Boot2Docker ISO version:  Did not find prefix "-v" in version string
(myvm1) Default Boot2Docker ISO is out-of-date, downloading the latest release...
(myvm1) Latest release for github.com/boot2docker/boot2docker is v18.09.0
(myvm1) Downloading /home/dgxuser/.docker/machine/cache/boot2docker.iso from https://github.com/boot2docker/boot2docker/releases/download/v18.09.0/boot2docker.iso...
(myvm1) 0%....10%....20%....30%....40%....50%....60%....70%....80%....90%....100%
Creating machine...
(myvm1) Unable to get the local Boot2Docker ISO version:  Did not find prefix "-v" in version string
(myvm1) Default Boot2Docker ISO is out-of-date, downloading the latest release...
(myvm1) Latest release for github.com/boot2docker/boot2docker is v18.09.0
(myvm1) Downloading /home/dgxuser/.docker/machine/cache/boot2docker.iso from https://github.com/boot2docker/boot2docker/releases/download/v18.09.0/boot2docker.iso...
(myvm1) 0%....10%....20%....30%....40%....50%....60%....70%....80%....90%....100%
(myvm1) Copying /home/dgxuser/.docker/machine/cache/boot2docker.iso to /home/dgxuser/.docker/machine/machines/myvm1/boot2docker.iso...
(myvm1) Creating VirtualBox VM...
(myvm1) Creating SSH key...
(myvm1) Starting the VM...
(myvm1) Check network to re-create if needed...
(myvm1) Found a new host-only adapter: "vboxnet0"
(myvm1) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm1
</pre>

## List the machines and get their IP addresses
```sh
docker-machine ls
```
It will output:
<pre>
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER     ERRORS
myvm1   -        virtualbox   Running   tcp://192.168.99.100:2376           v18.09.0   
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.09.0   
</pre>

## (If something went wrong, you may want to shut down and then start your docker machines)
```sh
docker-machine stop myvm1
```
It will output:
<pre>
Stopping "myvm1"...
Machine "myvm1" was stopped.
</pre>

```
docker-machine start myvm1
```
It will output:
<pre>
Starting "myvm1"...
(myvm1) Check network to re-create if needed...
(myvm1) Waiting for an IP...
Machine "myvm1" was started.
Waiting for SSH to be available...
Detecting the provisioner...
Started machines may have new IP addresses. You may need to re-run the `docker-machine env` command.
</pre>

## (Or even remove your docker machiens)
```sh
docker-machine rm myvm1
```
It will output:
<pre>
About to remove myvm1
WARNING: This action will delete both local reference and remote instance.
Are you sure? (y/n): y
Successfully removed myvm1
</pre>

## Initialize the docker swarm
```sh
docker-machine ssh myvm1 "docker swarm init --advertise-addr=192.168.99.100"
```
It will output:
<pre>
Swarm initialized: current node (ph0fx4x80nlxxok20wns8qwbq) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3l5ftbhcyq2zxfi0z53qohbblyxzf5nwsp74iatp8896oddrg6-1apcua9jhxffpm2jac3g47r50 192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
</pre>

Then add myvm2 to the swarm:
```sh
docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-3l5ftbhcyq2zxfi0z53qohbblyxzf5nwsp74iatp8896oddrg6-1apcua9jhxffpm2jac3g47r50 192.168.99.100:2377"
```
It will output:
<pre>
This node joined a swarm as a worker.
</pre>

### Troubleshooting:
#### Note1: The flag --advertise-addr is necessary when you have multiple addresses, 
here's the output of `docker-machine ssh myvm1 "docker swarm init"`:
<pre>
Error response from daemon: could not choose an IP address to advertise since this system has multiple addresses on different interfaces (10.0.2.15 on eth0 and 192.168.99.100 on eth1) - specify one with --advertise-addr
exit status 1
</pre>

#### Note2: The advertise-addr of swarm manager should be the same as the output from `docker-machine ls`, 
if you use the other ip, such as 
```sh
docker-machine ssh myvm1 "docker swarm init --advertise-addr=10.0.2.15"
```
Then the output will look fine:
<pre>
Swarm initialized: current node (kii99x957t7e2hzwaxuhf0nkc) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-1acmpwribhi2uml8ktr7lpyffjj64uq64nks3y8pmgdfibrap2-e4wxy06gtw33oiabev0ewlzc7 10.0.2.15:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
</pre>

But when you try to let myvm2 join the swarm,
```sh
docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-1acmpwribhi2uml8ktr7lpyffjj64uq64nks3y8pmgdfibrap2-e4wxy06gtw33oiabev0ewlzc7 10.0.2.15:2377"
```
It will give you an error:
<pre>
Error response from daemon: rpc error: code = Unavailable desc = all SubConns are in TransientFailure, latest connection error: connection error: desc = "transport: Error while dialing dial tcp 10.0.2.15:2377: connect: connection refused"
exit status 1
</pre>

## Check the swarm node in the manager node
```sh
docker-machine ssh myvm1 "docker node ls"
```
It will output:
<pre>
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
upzzrexydlvbru6n3pvfi9u1v *   myvm1               Ready               Active              Leader              18.09.0
jl8rh4aza9hfcrx9oy2xf3xi9     myvm2               Ready               Active                                  18.09.0
</pre>

## Use a docker-machine shell to control the swarm manager
Configure a docker-machine shell to the swarm manager
```sh
eval $(docker-machine env myvm1)
```
This allows you to use your local docker-compose.yml file to deploy the app “remotely” without having to copy it anywhere

Verify that myvm1 is now the active machine:
```sh
docker-machine ls
```
It will output:
<pre>
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER     ERRORS
myvm1   *        virtualbox   Running   tcp://192.168.99.100:2376           v18.09.0   
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.09.0   
</pre>

Some operation learned before:
```sh
docker stack deploy -c docker-compose.yml getstartedlab
docker stack ps getstartedlab
curl http://192.168.99.100 #Fail for me : "curl: (7) Failed to connect to 192.168.99.100 port 80: Connection refused"
docker stack rm getstartedlab
```

Unset docker-machine shell variable settings
```sh
eval $(docker-machine env -u)
```

## Or, use docker-machine to send an operation to the swarm manager
```sh
docker-machine scp docker-compose.yml myvm1:~
docker-machine ssh myvm1 "docker stack deploy -c docker-compose.yml getstartedlab"
docker-machine ssh myvm1 "docker stack ps getstartedlab"
docker-machine ssh myvm1 "curl http://localhost" #Fail for me : curl: (7) Failed to connect to localhost port 80: Connection refused exit status 7
docker-machine ssh myvm1 "docker stack rm getstartedlab"
```
