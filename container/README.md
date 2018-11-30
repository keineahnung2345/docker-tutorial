# Container

This tutorial is from [Get Started, Part 2: Containers](https://docs.docker.com/get-started/part2/)

## Build your docker image

```sh
docker build . -t friendlyhello
```

## Run the application
```sh
docker run -d -p 4000:80 friendlyhello
curl http://localhost:4000
```

## Check the container id
```sh
docker ps
```

## Stop the application
```sh
docker stop <container-id>
```

## Tag your docker image
```sh
docker tag friendlyhello <username>/get-started:part1
```

## Push the dokcer image to dockerhub
First login to dockerhub
```sh
docker login
```
and then key in your account and password

```sh
docker push <username>/get-started:part1
```

## Now you can run this application from another host
```sh
docker run -d -p 4000:80 <username>/get-started:part1
```
