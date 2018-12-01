# Stack

## Configure a docker-machine shell to the swarm manager
```sh
eval $(docker-machine env myvm1)
```

## Run the application
```sh
docker stack deploy -c docker-compose.yml getstartedlab
docker stack ps getstartedlab
curl -4 192.168.99.100:4000 # fail
curl -4 192.168.99.100:8080 # fail
```

## Persist the data using Redis
```sh
docker-machine ssh myvm1 "mkdir ./data"
docker stack deploy -c docker-compose-WITH-REDIS.yml getstartedlab
docker service ls
curl -4 http://192.168.99.100:8080 # fail
curl -4 http://192.168.99.100:80   # fail
curl -4 http://192.168.99.100:6379 # fail
```
