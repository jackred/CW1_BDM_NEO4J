# Coursework Big Data Management

## NEO4J

### Installation

#### Docker

##### Create a network

`docker network create <network-name>`

#### Use docker-compose

- add authentication information in `auth.env` (password), or set `NEO4J_AUTH=none`
- check the path for the volumes (default: `./data`) and the network name (that you created before)
- run `docker-compose up`

##### Run a NEO4J container

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
