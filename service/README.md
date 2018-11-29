# Service

This tutorial is from : [Docker入门六部曲——服务](https://blog.csdn.net/u011499747/article/details/73718918)

## Create a swarm
```sh
docker swarm init
```

## Run the application
```sh
docker stack deploy -c docker-compose.yml getstartedlab
```
It will output
> Creating network getstartedlab_webnet  
Creating service getstartedlab_web

## 

## Checkout the application's containers
List stacks:
```sh
docker stack ls
```
It will output
<pre>
NAME           SERVICES
getstartedlab  1
</pre>

List the tasks in the stack:
```sh
docker stack ps getstartedlab
```
It will output
<pre>
ID            NAME                 IMAGE                              NODE     DESIRED STATE  CURRENT STATE           ERROR  PORTS  
vdwtn91mms5r  getstartedlab_web.1  keineahnung2345/get-started:part1  dcosa71  Running        Running 29 minutes ago  
4eachjm68940  getstartedlab_web.2  keineahnung2345/get-started:part1  dcosa71  Running        Running 29 minutes ago  
jwch6nk4jifu  getstartedlab_web.3  keineahnung2345/get-started:part1  dcosa71  Running        Running 29 minutes ago  
h84u7c7z6e33  getstartedlab_web.4  keineahnung2345/get-started:part1  dcosa71  Running        Running 29 minutes ago  
p225czms0ush  getstartedlab_web.5  keineahnung2345/get-started:part1  dcosa71  Running        Running 29 minutes ago
</pre>

List the services in the stack
```sh
docker stack services getstartedlab
```
It will output
<pre>
ID            NAME               MODE        REPLICAS  IMAGE
0nsz5a45v2a8  getstartedlab_web  replicated  5/5       keineahnung2345/get-started:part1
</pre>

You can go to `http://localhost:8000/` and you can see the hostname varies when you refresh the webpage.

## Scale the application
Try changing `replicas: 15` to `replicas: 20` in `docker-compose.yml`, and then
```sh
docker stack deploy -c docker-compose.yml getstartedlab
```
Then your docker swarm will update automatically, you can check it with `docker stack ps getstartedlab`.

## Shutdown the application
Shutdown the application
```sh
docker stack rm getstartedlab
```

Check with `docker node ls`:
<pre>
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
3mv4zcg6utp5zfpil8bso9l27 *  dcosa71   Ready   Active        Leader
</pre>

You should remove the swarm node to shutdown the swarm:
```sh
docker swarm leave --force
```

Check again with `docker node ls`:
> Error response from daemon: This node is not a swarm manager. Use "docker swarm init" or "docker swarm join" to connect this node to swarm and try again.
