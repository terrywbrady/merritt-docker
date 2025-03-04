# See https://github.com/cdluc3/merritt-docker

---

# merritt-docker

_Copyright 2019 Regents of the University of California_
_All rights reserved_

## Purpose
The purpose of this repository is to build docker images for local developer testing of the [Merritt system](https://github.com/cdluc3/mrt-doc/wiki).

## Presentation

https://gitpitch.com/terrywbrady/merritt-docker

## Dependencies
The following dependencies are needed to build and run this repo.  The goal is to build a version of the system that can be run entirely from Docker.

- Docker and Docker Compose install
- Access to the CDL maven repo for a couple of pre-built jars
  - TODO: build these from source in the Dockerfile
- CDL LDAP access
- A local maven repo build of mrt-conf jar files
- Access to storage services
- Access to config properties
  - Locally stored in **/mrt-services/no-track**

## Component Overview

| Component | Image Name | Where the component runs | Notes |
| --------- | ---------- | ------------------------ | ----- |
| Java dependencies | cdluc3/mrt-dependencies | Docker | Base image for other microservices. All service properties are currently mocked in the jar file. |
| Zookeeper | zookeeper | Docker | |
| MySQL     | cdluc3/mrt-database | Docker or Server Instance | |
| LDAP      | N/A | Server Instance | |
| UI        | cdluc3/mrt-dashboard | Docker | Database and LDAP connection info is passed in via an untracked file |
| Ingest    | cdluc3/mrt-ingest | Docker | |
| Storage    | cdluc3/mrt-storage | Docker | |
| Inventory    | cdluc3/mrt-inventory | Docker | |

## Docker Image Publishing
Details about docker image publishing are TBD.

## Git Submodules
This repository uses git submodules to pull in code to be built.

## Credential configuration

Credentials for non-Docker services will be mounted from files within **mrt-services/no-track**.

Files in this directory are not tracked by github.

## Build instructions

Java Dependencies
```bash
cd mrt-dependencies
docker-compose build
```

Services

```bash
cd mrt-services
docker-compose build
```

## Service Configuration Options
| Config Name | LDAP | DB | Storage Node | Note |
| ------------- | ------ | -- | --------------- | ---- |
|Docker DB - Staging Storage | Staging | Docker | Staging - single node recommended by David | In Progress |
|Staging DB & Storage | Staging | Staging | Staging - single node recommended by David | In Progress |
|Docker DB - Isolated Storage | Staging | Docker | Isolated storage node that is distinct from Staging | TBD |
|Isolation| Docker | Docker | Isolated storage node that is distinct from Staging | TBD, create standalone or mock LDAP |


## Docker DB - Staging Storage

### Service Start

```bash
docker-compose -p merritt up
```

To verify running processes and ports
```bash
docker ps -a
```

To view persistent volumes
```bash
docker volume ls
```

To view logs for a specific container
```bash
docker logs ingest
```

Tail view logs for a specific container
```bash
docker logs -f inventory
```

To view the docker network
```bash
docker network ls
```

### Service Stop

```bash
docker-compose -p merritt down
```

## Staging DB & Storage

### Service Start

```bash
docker-compose -f docker-compose.yml -f staging-db.yml -p merritt up
```

### Service Stop

```
docker-compose -f docker-compose.yml -f staging-db.yml -p merritt down
```

## Other useful tasks

### List Zookeeper Queues
`docker exec -it zoo zkCli.sh ls /`

### Dump the ingest queue
`docker exec -it zoo listIngest.sh`

### Dump the inventory queue
`docker exec -it zoo listInventory.sh`

### Mysql Session
`docker exec -it db-container mysql -u user --password=password --database=db-name`
