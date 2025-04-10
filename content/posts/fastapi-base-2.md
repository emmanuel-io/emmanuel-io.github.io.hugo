---
title: "How to set up a fastapi backend service project"
date: 2022-06-27T01:12:42+02:00
author: "Emmanuel IO"
linktitle: How to set up a fastapi backend service project
# next: /spring-boot-devtools-on-intellij
# prev: /set-hugo-1
weight: 3
image: image/3/selfBlog.png
tags : [
    "fastapi",
    "blog",
    "tutorial",
]
categories : [
    "tutorial"
]
draft: true
---

As a convinced user of [FastAPI](https://fastapi.tiangolo.com/) I will explore in a new serie of articles how to setup a backend service with 2 databases using async interfaces:

1. SQL: PostgreSQL + SQLAlchemy (async).
2. NoSQL: MongoDB + Beanie (async)

N.B. I know there are existing solutions which are readily available from the FastAPI website, the point here is to propose something new with a twist, call it geek.

## **Setting up the root folder**

We will use docker-compose to run the databases in containers.  
To make sure we are able to start and stop the services we need to provide a place where to store data. We could either use docker volumes or map local folders in the containers, we are going to use the later.

I came up with this files and folders structure:

```text
./
├── backend 
|   └── 
├── db-sql
|   └── 
├── db-nosql
|   └── 
├── conf.env
├── docker-compose.yml
└── README.md
```

In more details:

1. **root folder**  
   * README.md  
     This file will contain the informations needed to delpoy this project.  
   * conf.env  
     This file has all environnment variables used in the project.
   * docker-compose.yml  
     This docker-compose file used to run all the differents services.
2. **db-sql**  
   Directory containing data for sql database.
3. **db-nosql**  
   Directory containing data for no-sql database.
4. **backend**  
   Directory which contains everything related to the fastapi backend service itself.

## **Content of the backend folder**

Before checking what we will have in the folder, we need to know what we need:  

* SQL database migration will be handled by alembic.
* Testing of our Python code, we'll use Pytest.
* To create our container we need a Dockerfile. As we need to share code between the tests and the app, we use a multi stage build with different targets, this makes things easier.
* To make sure our Python -code and libraries- doesn't collide with the container system, we'll use virtualenv and pip for dependencies.

The files and folders are then:

```text
backend
├── alembic
├── app
├── scripts
├── tests
├── alembic.ini
├── app.Dockerfile
├── pytest.ini
└── requirements.txt
```

1. **backend service rootfolder**  
2. **alembic**  
   This folders has the alembic configuration with migration files.
3. **app**  
   Directory containing all the code for the web service application.
4. **scripts**  
   Directory containing the scripts used for starting either:
   * The application.
   * The tests.
5. **tests**  
   Directory containing all pytest files.
6. **alembic.ini**  
   The configuration file for alembic.
7. **app.Dockerfile**  
   The docker configuration file for building both the application and the test images using a multi stage build.
8. **pytest.ini**  
   The configuration file for pytest.
9. **requirements.txt**  
   The almighty requirements file for installing all modules with pip.

**Create a docker-compose file**

Now in the root directory, let's start with a minimal docker-compose file.
We'll update as we build up our service.

```text
version: '3.9'

services:

  fastapi-base-pgdb:
    image: "postgres:12-alpine"
    env_file:
      - conf.env
    volumes:
      - ./db-sql/:/var/lib/postgresql/data/
    ports:
      - "5432:5432"
    networks:
      - fastapi-base-network

  fastapi-base-pgadmin:
    image: dpage/pgadmin4:latest
    env_file:
      - conf.env
    ports:
      - "5050:80"
    depends_on:
      - fastapi-base-pgdb
    networks:
      - fastapi-base-network

  fastapi-base-backend:
    container_name: base-app
    image: base-backend-image
    env_file:
      - conf.env
    build:
      context: backend
      dockerfile: app.Dockerfile
      target: application
    ports:
      - "8000:8000"
    networks:
      - fastapi-base-network
    volumes:
      - ./app:/opt/app
      - ./dataset:/opt/dataset
    depends_on:
      - fastapi-base-pgdb

  fastapi-base-testing:
    container_name: base-test
    image: base-testing-image
    env_file:
      - conf.env
    build:
      context: backend
      dockerfile: app.Dockerfile
      target: testing
    networks:
      - fastapi-base-network
    volumes:
      - ./tests_results:/tests_results
    depends_on:
      - fastapi-base-pgdb

networks:
  fastapi-base-network:
    driver: bridge
```

```text
./
├── db-sql
├── db-nosql
├── backend 
│   ├── alembic
|   ├── app
|   ├── scripts
|   ├── tests
|   ├── alembic.ini
|   ├── pytest.ini
|   ├── app.Dockerfile
|   ├── pytest.ini
|   └── requirements.txt
├── README.md
├── config.env
└── docker-compose.yml
```
