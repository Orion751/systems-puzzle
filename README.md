# Insight DevOps Engineering Systems Puzzle

## Table of Contents
1. [Understanding the puzzle](README.md#understanding-the-puzzle)
2. [Introduction](README.md#introduction)
3. [Puzzle details](README.md#puzzle-details)
4. [Instructions to submit your solution](README.md#instructions-to-submit-your-solution)
5. [FAQ](README.md#faq)

# Understanding the puzzle

We highly recommend that you take a few dedicated minutes to read this README in its entirety before starting to think about potential solutions. You'll probably find it useful to review the codebase and understand the system at a high-level before attempting to find specific bugs.

# Introduction

Imagine you're on an engineering team that is building an eCommerce site where users can buy and sell items (similar to Etsy or eBay). One of the developers on your team has put together a very simple prototype for a system that writes and reads to a database. The developer is using Postgres for the backend database, the Python Flask framework as an application server, and nginx as a web server. All of this is developed with the Docker Engine, and put together with Docker Compose.

Unfortunately, the developer is new to many of these tools, and is having a number of issues. The developer needs your help debugging the system and getting it to work properly.

# Puzzle details

The codebase included in this repo is nearly functional, but has a few bugs that are preventing it from working properly. The goal of this puzzle is to find these bugs and fix them. To do this, you'll have to familiarize yourself with the various technologies (Docker, nginx, Flask, and Postgres). You definitely don't have to be an expert on these, but you should know them well enough to understand what the problem is.

Assuming you have the Docker Engine and Docker Compose already installed, the developer said that the steps for running the system is to open a terminal, `cd` into this repo, and then enter these two commands:

    docker-compose up -d db
    docker-compose run --rm flaskapp /bin/bash -c "cd /opt/services/flaskapp/src && python -c  'import database; database.init_db()'"

This "bootstraps" the PostgreSQL database with the correct tables. After that you can run the whole system with:

    docker-compose up -d

At that point, the web application should be visible by going to `localhost:8080` in a web browser. 

Once you've corrected the bugs and have the basic features working, commit the functional codebase to a new repo following the instructions below. As you debug the system, you should keep track of your thought process and what steps you took to solve the puzzle.

## Instructions to submit your solution
* Don't schedule your interview until you've worked on the puzzle 
* To submit your entry please use the link you received in your systems puzzle invitation
* You will only be able to submit through the link one time
* For security, we will not open solutions submitted via files
* Use the submission box to enter the link to your GitHub repo or Bitbucket ONLY
* Link to the specific repo for this project, not your general profile
* Put any comments in the README inside your project repo

# FAQ

Here are some common questions we've received. If you have additional questions, please email us at `devops@insightdata.com` and we'll answer your questions as quickly as we can (during PST business hours), and update this FAQ. Again, only contact us after you have read through the Readme and FAQ one more time and cannot find the answer to your question.

### Which Github link should I submit?
You should submit the URL for the top-level root of your repository. For example, this repo would be submitted by copying the URL `https://github.com/InsightDataScience/systems-puzzle` into the appropriate field on the application. **Do NOT try to submit your coding puzzle using a pull request**, which would make your source code publicly available.

### Do I need a private Github repo?
No, you may use a public repo, there is no need to purchase a private repo. You may also submit a link to a Bitbucket repo if you prefer.

### What sort of system should I use to run my program (Windows, Linux, Mac)?
You should use Docker to run and test your solution, which should work on any operating system. If you're unfamiliar with Docker, we recommend attending one of our Online Tech Talks on Docker, which you should've received information about in your invitation. Alternatively, there are ample free resources available on docker.com.

### How will my solution be evaluated?
While we will review your submission briefly before your interview, the main point of this puzzle is to serve as content for discussion during the interview. In the interview, we'll evaluate your problem solving and debugging skills based off how you solved this puzzle, so be sure to document your thought process.

### This eCommerce site is ugly...should I improve the design?  
No, you should focus on the functionality. Your engineering team will bring on a designer and front-end developer later in the process, so don't worry about that aspect in this puzzle. If you have extra time, it would be far better to focus on aspects that make the code cleaner and easier to use, like tests and refactoring.

### Should I use orchestration tools like Kubernetes?
While technologies like Kubernetes are quite powerful, they're likely overkill for the simple application in this puzzle. We recommend that you stick to Docker Compose for this puzzle.

# Solution

## Docker Setup

### Docker Engine

I already have Docker installed on my Ubuntu 16.04 system as part of the Web Dev Special Interest Group (SIG) of NJIT's ACM chapter, albeit I haven't used it in awhile.  So, I began by checking against docker.com to confirm that my install was up-to-date.  In the release notes, I see the following command recommended due to recent changes, so I used it:
```bash
sudo apt install docker-ce docker-ce-cli containerd.io
```

Apt tells me there's nothing to install, so all seems good.

### Docker Compose

I noticed that docker-compose didn't come up from fish shell's autocomplete, suggesting that it was not installed.  `docker-compose --version` confirmed this to be true.

While docker.com suggests installing Compose on Linux via curl, I instead use `sudo apt install docker-ce docker-ce-cli containerd.io` as suggested by my terminal so I can update it with the rest of my system.  If I run into any issues, it might be worth investigating this.

## Running the container

Upon running `docker-compose up -d db`, I get the following error message:
```
ERROR: Version in "./docker-compose.yml" is unsupported. You might be seeing this error because you're using the wrong Compose file version. Either specify a version of "2" (or "2.0") and place your service definitions under the `services` key, or omit the `version` key and place your service definitions at the root of the file to use version 1.
For more on the Compose file format versions, see https://docs.docker.com/compose/compose-file/
```

I instinctly run the command again to confirm the same result.

This gives me two items to investigate: the command syntax and the error message.  I start with the former by seeing what `docker-compose help` gets me:
```
Define and run multi-container applications with Docker.

Usage:
  docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
  docker-compose -h|--help

Options:
  -f, --file FILE             Specify an alternate compose file (default: docker-compose.yml)
  -p, --project-name NAME     Specify an alternate project name (default: directory name)
  --verbose                   Show more output
  -v, --version               Print version and exit
  -H, --host HOST             Daemon socket to connect to

  --tls                       Use TLS; implied by --tlsverify
  --tlscacert CA_PATH         Trust certs signed only by this CA
  --tlscert CLIENT_CERT_PATH  Path to TLS certificate file
  --tlskey TLS_KEY_PATH       Path to TLS key file
  --tlsverify                 Use TLS and verify the remote
  --skip-hostname-check       Don't check the daemon's hostname against the name specified
                              in the client certificate (for example if your docker host
                              is an IP address)

Commands:
  build              Build or rebuild services
  bundle             Generate a Docker bundle from the Compose file
  config             Validate and view the compose file
  create             Create services
  down               Stop and remove containers, networks, images, and volumes
  events             Receive real time events from containers
  exec               Execute a command in a running container
  help               Get help on a command
  kill               Kill containers
  logs               View output from containers
  pause              Pause services
  port               Print the public port for a port binding
  ps                 List containers
  pull               Pulls service images
  push               Push service images
  restart            Restart services
  rm                 Remove stopped containers
  run                Run a one-off command
  scale              Set number of containers for a service
  start              Start services
  stop               Stop services
  unpause            Unpause services
  up                 Create and start containers
  version            Show the Docker-Compose version information
```

This line tells us the purpose of the command we've been trying to run:
```
   up                 Create and start containers
```

In attempt to investigate further, I find the output of `docker-compose --help` to be identical to `docker-compose help`. perhaps a means to maximize the discoverability of this info.  I was hoping for more to better understand the `-d` option, which isn't listed with the likes of `-f, --file FILE` or `--verbose`.  Time to try `man docker-compose`.

Surely enough, man gives me the info on the `-d` option that I'm looking for.
```
   up
       Build, (re)create, start and attach to containers.  If there are existing containers for a service, `docker-compose up` will stop and recreate them (preserving mounted volumes  with  volumes-
       from), so that changes in `docker-compose.yml` are picked up. If you do not want existing containers to be recreated, `docker-compose up --no-recreate` will re-use existing containers.

       --allow-insecure-ssl
              Allow insecure connections to the Docker registry.

       -d     Detached mode. Run container in the background, print new container name.
```

I notice that there's an identical entry for `-d` under the `run` subcommand.

I tried `docker-compose help up` to see if I could've arrived to the same conclusion a little quicker next time.  This is indeed true.

So far, it's clear that the intention of this command is to start up the container as a background process, and there's some kind of version conflict prevent Compose from doing its job.

I now turn my attention to the error message.  I cat the "./docker-compose.yml" file mentioned:
```yml
version: '3'
services:
  db:
    image: "postgres:9.6.5"
    volumes:
      - "dbdata:/var/lib/postgresql/data"
    env_file:
      - env_file
    networks:
      - db_network
  flaskapp:
    build: .
    env_file:
      - env_file
    volumes:
      - .:/opt/services/flaskapp/src
    networks:
      - db_network
      - web_network
    depends_on:
      - db
  nginx:
    image: "nginx:1.13.5"
    ports:
      - "80:8080"
    volumes:
      - ./conf.d:/etc/nginx/conf.d
    networks:
      - web_network
    depends_on: 
      - flaskapp
networks:
  db_network:
    driver: bridge
  web_network:
    driver: bridge
volumes:
  dbdata:
```

My takeaways:
1. `db` refers to the PostgreSQL service.
2. The first line is probably what's triggering the error.

My suspicion is that the version number was mistyped.  I think I'll follow the error's suggestion of changing it to "2".  Recalling the terminology of dictionaries in Python to the best of my ability, I'd wager to say that the services key + definitions part of the error's instructions have already been met.  If I run into more issues, I can look back at this and clarify how YAML files work later.

After this change, the `docker-compose up -d d` command gives me this:
```
Creating network "systemspuzzle_web_network" with driver "bridge"
Creating network "systemspuzzle_db_network" with driver "bridge"
Creating volume "systemspuzzle_dbdata" with default driver
Pulling db (postgres:9.6.5)...
ERROR: Get https://registry-1.docker.io/v2/: dial tcp: lookup registry-1.docker.io on [::1]:53: read udp [::1]:51505->[::1]:53: read: connection refuse
```

Seems like we've made some progress.  Two networks and a volume were created.  Our error now appears to be related to tcp.

In spite of expecting the same output, I run the command again.  This time, it seems to work!
```
Pulling db (postgres:9.6.5)...
9.6.5: Pulling from library/postgres
85b1f47fba49: Pull complete
2d4904bea61e: Pull complete
92fb981a71b7: Pull complete
4dda1edd3e9b: Pull complete
5ea002fc8280: Pull complete
feade6b1bbeb: Pull complete
16825a5c9040: Pull complete
a4101e46b1e2: Pull complete
078d6d550d3d: Pull complete
ac9086e062cf: Pull complete
7718e622f74c: Pull complete
111246a411c9: Pull complete
Digest: sha256:2f2b1f4d9d83db7378584d7f41b15a49b2cea25956af67698f8ea80e3bdc28ba
Status: Downloaded newer image for postgres:9.6.5
Creating systemspuzzle_db_1
```
It appears as though it continued where it left off to get to the database and was successful this time.  Whoohoo!

Now time for `docker-compose run --rm flaskapp /bin/bash -c "cd /opt/services/flaskapp/src && python -c  'import database; database.init_db()'"`:
```
Building flaskapp
Step 1/9 : FROM python:3
3: Pulling from library/python
90fe46dd8199: Pull complete
35a4f1977689: Pull complete
bbc37f14aded: Pull complete
74e27dc593d4: Pull complete
4352dcff7819: Pull complete
deb569b08de6: Pull complete
98fd06fa8c53: Pull complete
7b9cc4fdefe6: Pull complete
e8e1fd64f499: Pull complete
Digest: sha256:adcfb73e4ca83b126cc3275f3851c73aecca20e59a48782e9ddebb3a88e57f96
Status: Downloaded newer image for python:3
 ---> a6be143418fc
Step 2/9 : ENV PYTHONUNBUFFERED 1
 ---> Running in 9a64c7c64435
Removing intermediate container 9a64c7c64435
 ---> ec4abc513dcd
Step 3/9 : RUN mkdir -p /opt/services/flaskapp/src
 ---> Running in d1c14a55cc4e
Removing intermediate container d1c14a55cc4e
 ---> 77a7b8a9d785
Step 4/9 : COPY requirements.txt /opt/services/flaskapp/src/
 ---> e416c0e5275f
Step 5/9 : WORKDIR /opt/services/flaskapp/src
 ---> Running in 1d19d5d300ba
Removing intermediate container 1d19d5d300ba
 ---> 863862fab634
Step 6/9 : RUN pip install -r requirements.txt
 ---> Running in 35890960c04b
Collecting Flask
  Downloading Flask-1.1.2-py2.py3-none-any.whl (94 kB)
Collecting psycopg2
  Downloading psycopg2-2.8.5.tar.gz (380 kB)
Collecting SQLAlchemy
  Downloading SQLAlchemy-1.3.16-cp38-cp38-manylinux2010_x86_64.whl (1.2 MB)
Collecting Flask-WTF
  Downloading Flask_WTF-0.14.3-py2.py3-none-any.whl (13 kB)
Collecting Werkzeug>=0.15
  Downloading Werkzeug-1.0.1-py2.py3-none-any.whl (298 kB)
Collecting itsdangerous>=0.24
  Downloading itsdangerous-1.1.0-py2.py3-none-any.whl (16 kB)
Collecting click>=5.1
  Downloading click-7.1.2-py2.py3-none-any.whl (82 kB)
Collecting Jinja2>=2.10.1
  Downloading Jinja2-2.11.2-py2.py3-none-any.whl (125 kB)
Collecting WTForms
  Downloading WTForms-2.3.1-py2.py3-none-any.whl (169 kB)
Collecting MarkupSafe>=0.23
  Downloading MarkupSafe-1.1.1-cp38-cp38-manylinux1_x86_64.whl (32 kB)
Building wheels for collected packages: psycopg2
  Building wheel for psycopg2 (setup.py): started
  Building wheel for psycopg2 (setup.py): finished with status 'done'
  Created wheel for psycopg2: filename=psycopg2-2.8.5-cp38-cp38-linux_x86_64.whl size=500516 sha256=e89997d77ed5b4a4a049f9561b276a22667dd72059123cbcbee59b739888420e
  Stored in directory: /root/.cache/pip/wheels/35/64/21/9c9e2c1bb9cd6bca3c1b97b955615e37fd309f8e8b0b9fdf1a
Successfully built psycopg2
Installing collected packages: Werkzeug, itsdangerous, click, MarkupSafe, Jinja2, Flask, psycopg2, SQLAlchemy, WTForms, Flask-WTF
Successfully installed Flask-1.1.2 Flask-WTF-0.14.3 Jinja2-2.11.2 MarkupSafe-1.1.1 SQLAlchemy-1.3.16 WTForms-2.3.1 Werkzeug-1.0.1 click-7.1.2 itsdangerous-1.1.0 psycopg2-2.8.5
Removing intermediate container 35890960c04b
 ---> 919e83f958da
Step 7/9 : COPY . /opt/services/flaskapp/src
 ---> b84627e3d42c
Step 8/9 : EXPOSE 5001
 ---> Running in a4653d5a701a
Removing intermediate container a4653d5a701a
 ---> f45161f52e56
Step 9/9 : CMD ["python", "app.py"]
 ---> Running in e87d0fcfb2dc
Removing intermediate container e87d0fcfb2dc
 ---> 9ed120c99816
Successfully built 9ed120c99816
Successfully tagged systemspuzzle_flaskapp:latest
WARNING: Image for service flaskapp was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`
```
Other than a warning that doesn't look to be problematic, this command seems to run smoothly out-of-the-gate.

Next, `docker-compose up -d`:
```
Pulling nginx (nginx:1.13.5)...
1.13.5: Pulling from library/nginx
bc95e04b23c0: Pull complete
110767c6efff: Pull complete
f081e0c4df75: Pull complete
Digest: sha256:004ac1d5e791e705f12a17c80d7bb1e8f7f01aa7dca7deee6e65a03465392072
Status: Downloaded newer image for nginx:1.13.5
systemspuzzle_db_1 is up-to-date
Creating systemspuzzle_flaskapp_1
Creating systemspuzzle_nginx_1

ERROR: for nginx  Cannot start service nginx: driver failed programming external connectivity on endpoint systemspuzzle_nginx_1 (d54a5d395e2946d31369e9498d0f3abd225b53743b79ae30d49812a08021567f): Error starting userland proxy: listen tcp 0.0.0.0:80: bind: address already in use
ERROR: Encountered errors while bringing up the project
```

I repeat the command again, but this time I do get the same error:
```
systemspuzzle_db_1 is up-to-date
systemspuzzle_flaskapp_1 is up-to-date
Starting systemspuzzle_nginx_1

ERROR: for nginx  Cannot start service nginx: driver failed programming external connectivity on endpoint systemspuzzle_nginx_1 (6929a30f46d663c306c431d9e857049549700aa9652616ff238320bec0ec71cf): Error starting userland proxy: listen tcp 0.0.0.0:80: bind: address already in use
ERROR: Encountered errors while bringing up the project
```

Googling this error sent me to https://github.com/nginx-proxy/nginx-proxy/issues/839.  These lines of output from the suggested command of `sudo lsof -nP | grep LISTEN` and glancing through the discussion suggests that apache2 might be using port 80:
```
apache2    1685                   root    4u     IPv6              28794       0t0        TCP *:80 (LISTEN)
apache2    1707               www-data    4u     IPv6              28794       0t0        TCP *:80 (LISTEN)
apache2    1708               www-data    4u     IPv6              28794       0t0        TCP *:80 (LISTEN)
apache2    1710               www-data    4u     IPv6              28794       0t0        TCP *:80 (LISTEN)
apache2    1711               www-data    4u     IPv6              28794       0t0        TCP *:80 (LISTEN)
apache2    1713               www-data    4u     IPv6              28794       0t0        TCP *:80 (LISTEN
```

Per https://github.com/nginx-proxy/nginx-proxy/issues/839#issuecomment-334690146, I run these commands:
```bash
$ sudo systemctl stop apache2
echo manual | sudo tee /etc/init/apache2.override
```
Followed by `docker-compose up -d` again, giving me this promising output:
```
Removing systemspuzzle_nginx_1
systemspuzzle_db_1 is up-to-date
systemspuzzle_flaskapp_1 is up-to-date
Starting b4613dd0ffdb_systemspuzzle_nginx_
```

Only problem: My browser tells me that the connection to localhost:8080 is refused.  Googling this message sends me to this article: https://pythonspeed.com/articles/docker-connection-refused/.  It mentions `docker run`, often with `--rm`, which prompts me to investigate w/ `docker-compose help run`

