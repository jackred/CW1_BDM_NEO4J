# Coursework Big Data Management

## NEO4J

Note: if your user is not in the `docker` group, using `sudo` will be necessary to do most command.  
[Docker Linux PostInstall instruction](https://docs.docker.com/install/linux/linux-postinstall/)

### Installation

#### Docker

##### Create a network

`docker network create <network-name>`

#### Use docker-compose

- add authentication information in `auth.env` (password), or set `NEO4J_AUTH=none`
- check the path for the volumes (default: `./data`) and the network name (that you created before)
- run `docker-compose up`

##### Or run a NEO4J container directly

without authentication:

`docker run --volume=<path-to-shared-volume-on-host>:/data --restart always --env NEO4J_AUTH=none --name <name-container> --net <network-name> -d neo4j`


with authentication:
- default password: `docker run --volume=<path-to-shared-volume-on-host>:/data --restart always --name <name-container> --net <network-name> -d neo4j` login: `neo4j` / password: `neo4j`
- changed password: `docker run --volume=<path-to-shared-volume-on-host>:/data --restart always --env NEO4J_AUTH=neo4j/<password> --name <name-container> --net <network-name> -d neo4j` --> login: `neo4j` / password: `<password>`

warning: If you want to change the password, you'll have to empty the shared volume. There's possible bug with the authentication if you are using a previously used shared volume when re-creating the container.  


### Access the DB

#### In a browser

##### Get the DB IP

`docker network inspect <network-name>`

example output:
```
[
    {
        "Name": "<network-name>",
        "Id": "<ID>",
        "Created": "2020-01-16T22:58:51.044138424Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "<IP>",
                    "Gateway": "<IP>"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "<ID>": {
                "Name": "<name-container>",
                "EndpointID": "<ID>",
                "MacAddress": "<MAC_ADRESS>",
                "IPv4Address": "<DB_IP>",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

##### Connect to the db
The DB ip will be listed for the container in the field `IPv4Address`.  

The port used by neo4j is `7474`. If you open your browser and go to `DB_IP:7474` you should access to your NEO4J login screen.

#### Command line

```bash
docker exec -it <name-container> bash
```
after entering the container, to access the db:  
```bash
cypher-shell
```


### Troubleshooting

#### Increase memory

It's possible that neo4j won't have enough memory to perform certain operation. To increase it, perform the following operation:  
*on host*
```bash
docker exec -it <name-container> bash
```
after entering the container, you should be in the folder `/var/lib/neo4j`  
*in container*
```bash
sed -i "/dbms.memory.heap.max_size/c\dbms.memory.heap.max_size=<XXX>m" conf/neo4j.conf 
```
Change `<XXX>` by the amount of memory you want to allocate. Default one should be 512m.  
*on host*
```bash
docker restart <name-container>
```
