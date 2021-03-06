# rescuekerala

[![Build Status - Travis][0]][1]

Website for coordinating the rehabilitation of the people affected in the 2018 Kerala Floods.

[![Join Kerala Rescue Slack channel](https://i.imgur.com/V7jxjak.png)](http://bit.ly/keralarescueslack)

# Kerala Rescue

Website for coordinating rehabilitation of people affected in the 2018 Kerala Floods.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

You will need to have following softwares in your system:

- [Python 3](https://www.python.org/downloads/)
- [Postgres](https://www.postgresql.org/download/)
- [git](https://git-scm.com/downloads)

### Installing

#### Setting up a development environment

1. Create database and user in postgres for kerala rescue and give privileges.

```
psql user=postgres
Password:
psql (10.4 (Ubuntu 10.4-0ubuntu0.18.04))
Type "help" for help.

postgres=# CREATE DATABASE rescuekerala;
CREATE DATABASE
postgres=# CREATE USER rescueuser WITH PASSWORD 'password';
CREATE ROLE
postgres=# GRANT ALL PRIVILEGES ON DATABASE rescuekerala TO rescueuser;
GRANT
postgres=# \q

```

2. Clone the repo.
```
git clone https://github.com/IEEEKeralaSection/rescuekerala.git
cd rescuekerala
```

3. Copy the sample environment file and configure it as per your local settings.

```
cp .env.example .env
```

Note: If you cannot copy the environment or you're facing any difficulty in starting the server, copy the settings file from
https://github.com/vigneshhari/keralarescue_test_settings for local testing.

3. Install dependencies.

```
pip3 install -r requirements.txt
```

4. Run database migrations.

```
python3 manage.py migrate
```

5. Setup static files.
```
python3 manage.py collectstatic
```


6. Run the server.

```
python3 manage.py runserver
```
7. Now open localhost:8000 in the browser

#### Setting up the dev/test environment as docker container
If you use Docker and would like to do the development/testing in a Docker container, the following steps can be followed:

1. Clone the repoi, setup the env file and settings.py file (under floodrelief) as mentioned above (refer above sections)

2. Once the directory is ready, change the working directory to "rescuekerala" directory, create a file named dbsetup.sql with the following contents
```
CREATE DATABASE rescuekerala;
CREATE USER rescueuser WITH PASSWORD 'password';
GRANT ALL PRIVILEGES ON DATABASE rescuekerala TO rescueuser;
```

3. In the rescuekerala directory, create a file named Dockerfile with the following contents:
```
FROM postgres:alpine
RUN apk update && apk upgrade && \
    apk add --no-cache alpine-sdk && \
    apk add --no-cache bash git openssh && \
    apk add --no-cache python3 && \
    python3 -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    apk add postgresql-dev gcc python3-dev musl-dev && \
    rm -r /root/.cache
RUN pip install psycopg2
COPY dbsetup.sql /docker-entrypoint-initdb.d/
COPY requirements.txt /tmp/requirements.txt
RUN pip install -r /tmp/requirements.txt
```

The above list is a draft list and could be optimized based on further testing! Feel free to udpate.

4. Build docker image. Example (the name can be anything)
```
docker build -t rescuekerala_test .
```

5. Run the docker image
```
docker run -v $(pwd):/rescuekerala -d --rm -p 8000:8000 -e POSTGRES_PASSWORD=postgres --name rescuekerala_test rescuekerala_test
```

6. Connect to the running container, initialize and run the webserver
```
docker exec -it <container-id> /bin/bash
```

In the shell that comes up, run the following in sequence:
```
cd /rescuekerala
python3 manage.py migrate
python3 manage.py collectstatic
python3 manage.py runserver 0.0.0.0:8000
```

7. Now you can open the site in a browser (localhost:8000) and continue test/development. Since the local directory (rescuekerala) is mapped to the container as "/rescuekerala", whatever changes that is made in the host directory are reflected inside the container too. When the source files are changed in the host machine, the webserver typically restarts automatically, the UI needs to be refreshed.

8. To stop the container, use "docker stop <container-id>" 

## Running tests

When running tests, Django creates a test replica of the database in order for the tests not to change the data on the real database. Because of that you need to alter the Postgres user that you created and add to it the `CREATEDB` priviledge:

```
ALTER USER rescueuser CREATEDB;
```

To run the tests, run this command:

```
python3 manage.py test --settings=floodrelief.test_settings
```

## How can you help?

### By testing

We have a lot of [Pull Requests](https://github.com/IEEEKeralaSection/rescuekerala/pulls) that requires testing. Pick any PR that you like, try to reproduce the original issue and fix. Also join `#testing` channel in our slack and drop a note that you
are working on it.

## Testing Pull Requests
1. Checkout the Pull Request you would like to test by
      ```
      git fetch origin pull/ID/head:BRANCHNAME`
      git checkout BRANCHNAME
     ```    
2. Example
    ```
    git fetch origin pull/406/head:jaseem  
    git checkout jaseem1
    ```
3. Run Migration
    
Please find issues that we need help [here](https://github.com/IEEEKeralaSection/rescuekerala/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22). Go through the comments in the issue to check if someone else is already working on it. Don't forget to drop a comment when you start working on it.

[0]: https://travis-ci.org/IEEEKeralaSection/rescuekerala.svg?branch=master
[1]: https://travis-ci.org/IEEEKeralaSection/rescuekerala
