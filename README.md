#### lldockerorches
#####2
######2.2
```
docker run node
```
```
docker build -t my .
docker run -P -d my
```
#####3
######3.2push to docker hub
```
docker build -t rengokantai/my:latest .
docker build -t rengokantai/my
```
######3.3 configure,push to github
trigger the build.
#####4
######4.2
[see here](https://github.com/rengokantai/lldockerorchesproj)
install docker-machine
first instll virtubox
```
echo deb http://download.virtualbox.org/virtualbox/debian vivid contrib >>/etc/apt/sources.list
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
sudo apt-get update && sudo apt-get install dkms virtualbox-5.0
vboxmanage --version
```

```
curl -L https://github.com/docker/machine/releases/download/v0.6.0/docker-machine-`uname -s`-`uname -m` > /usr/local/bin/docker-machine && \
chmod +x /usr/local/bin/docker-machine
```
create virtual machine using virtubox driver
```
docker-machine create --driver virtualbox name
```
```
docker-machine ls
docker-machine env name
curl (docker-machine ip name)
```
######4.3
```
docker-machine create -d digitalocean --digitalocean-access-token 123 name
eval `docker-machine env name`  //make sure it is active
docker-machine ls
docker-machine ssh name
```
back to local machine,download image:
```
docker run -d -p 80:3000 rengokantai/my
```
test
```
curl `docker-machine ip name`
```
#####5
######5.1 docker-compose
```
curl -L https://github.com/docker/compose/releases/download/1.7.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose
```
start:
build the script name ```docker-compose.yml```
```
webservice:
	build: . 
	ports:
		- "3000:80"
	environment: 
		port: "80"
	volumes:
		- .:/user/src/app
```
start:
```
docker-compose run (-d) webservice
```
build come part of service.
```
docker build webservice
```
run all!
```
docker-compose up -d  //run all service
```
test.
```
curl localhost:3000
```
to stop:
```
docker ps
docker stop 123
```
######using a db
[see here](https://github.com/rengokantai/lldockerorchesproj52)
docker-compose.yml
```
database:
	image: mongo

api:
	build: .
	volumes:
		- .:/usr/src/app
	ports:
		- "3000:80"
	environment:
		port: "80"
		mongoUri: "mongodb://database/app-dev"
	links:
		- database
```
start:
```
docker-compose up -d --build
```
you will see   
Starting service_database_1  
Starting service_api_1  

test:
```
curl http://localhost:3000/users
curl -H "Content-Type: application/json" -X POST -d '{"name": "john"}' http://localhost:3000/users
curl http://localhost:3000/users
```
using interactive shell
```
docker exec -it new1 /bin/bash
```
after in docker container,
```
cat /etc/hosts
```
then
```
exit
```
clearup
```
docker-compose stop
```
######multiple test
[see here](https://github.com/rengokantai/lldockerorchesproj53)
see 
```
docker-compose up 
docker ps
docker exec -it 3a0a9b353bee /bin/bash
cat /etc/hosts
```

#####cp 6
######6-2 using .env
######6-2 use docker-machine with docker-compose
[see here](https://github.com/rengokantai/lldockerorchesproj62)
```
docker-machine create -d digitalocean --digitalocean-access-token 123 name2
docker-machine ls
docker-machine env name2
eval `docker-machine env name2`
docker info
docker-compose -f production-docker-compose.yml up -d
docker-machine ls
curl http://1.2.3.4/users
curl (docker-machine ip name2)
curl (docker-machine ip name2)/users
```
#####7
######7-2 docker swarm
```
docker pull swarm
docker run swarm //will get help list
docker run swarm create
``` 
record the token! 123
######7-3
```
docker-machine create -d digitalocean --digitalocean-access-token 123 --swarm --swarm-master --swarm-discovery token://123 mastername
docker-machine create -d digitalocean --digitalocean-access-token 123 --swarm --swarm-discovery token://123 slavename
docker-machine env --swarm mastername
eval `docker-machine env --swarm mastername`  //just master, no slave
docker info
```
cleanup
```
docker-machine stop mastername
docker-machine stop slavename
docker-machine rm mastername
docker-machine rm slavename
```
#####8
######1 strategy
spread: EX: host1 has 2 containers, host2 has 1 container, the next will run on host2  
random: 
binback: opposite to spread, run as less hosts
######2 using label
```
docker-machine create -d digitalocean --digitalocean-access-token 123 --swarm --engine-label disktype=ssd --swarm-discovery token://40 slavename2
```
######3 affinify
```
docker run -d -P nginx
docker ps
docker run -d -P -e affinity:image==nginx nginx
docker ps
```
#####9 kubernetes
Deprecated. To be continued
