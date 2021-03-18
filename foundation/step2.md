# Create docker-compose file

## Create docker file and build the docker image  

The docker file starts from amazoncorretto that includes the jvm needed.  
It will add python, pandas and verify the installation of the packages by doing: `python3 -c "import numpy as np"`  

```
cat <<File > Dockerfile
FROM amazoncorretto:8

RUN yum -y update
RUN yum -y install yum-utils
RUN yum -y groupinstall development

RUN yum list python3*
RUN yum -y install python3 python3-dev python3-pip python3-virtualenv

RUN python -V
RUN python3 -V

ENV PYSPARK_DRIVER_PYTHON python3
ENV PYSPARK_PYTHON python3

RUN pip3 install --upgrade pip
RUN pip3 install numpy pandas

RUN pip3 install py4j
RUN pip3 install pypandoc
RUN pip3 install pyspark

RUN python3 -c "import numpy as np"

ENTRYPOINT ["/bin/bash"]
File
```{{execute}}

Build the docker image and tag it `mypyspark`: 
```
docker build . -t mypyspark
```{{execute}}

Verify again the docker images by: `docker images`{{execute}}

Now there's a new docker image named `mypyspark` with tag `latest`. You should see output like this:  
```
$ docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
mypyspark                  latest              2f55e3a13ec4        15 seconds ago      2.01GB
amazoncorretto             8                   1bf2504e87fa        9 hours ago         346MB
...
``

## Create docker-compose file  

This basic docker-compose file is doing the following:
* it runs a single container, based on the image we just builded
* there's 1 port mapping, that will map the container port 4040, to localhost 80. Doing that we will be able to view the spark History page
* there's 1 volume mapping, to push some a single file named `raw_tweets.txt` to the container under the name: `raw_tweets.json`


```
cat <<composeFile > docker-compose.yml
version: '2.1'
services:
    pyspark:
        container_name: mypyspark
        image: mypyspark
        ports:
            - "80:4040"
        tty: true
        volumes:
        - ./raw_tweets.txt:/opt/raw_tweets.json
composeFile
```{{execute}}
  
  
Let's start what's defined in the docker-compose file in detachmed mode:
```
docker-compose up -d
```{{execute}}

Now we can start a bash shell inside the container with the command:
```
docker exec -it mypyspark bash
```{{execute}}