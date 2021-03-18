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
Dockerfile
```{{execute}}

Build the docker image and tag it `mypyspark`: `docker build . -t mypyspark`

Verify again the docker images by: `docker images`{{execute}}

## Create docker-compose file
```
cat <<composeFile > docker-compose.yml
version: '2.1'
services:
    pyspark:
        image: mypyspark
        ports:
            - "80:4040"
        tty: true
        volumes:
        - ../raw_tweets.txt:/opt/raw_tweets.json
composeFile
```{{execute}}