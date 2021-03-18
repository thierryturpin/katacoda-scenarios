# Get Docker and docker-compose

We are running on Ubuntu, see: `cat /etc/*release*`{{execute}}  

Install docker 

```
apt-get install docker-ce docker-ce-cli containerd.io
```{{execute}}

And verify the installation by executing: `docker info`{{execute}}

Install docker-compose
```
sudo apt install docker-compose
```{{execute}}

And verigy the installtion by executing: `docker-compose -info`{{execute}}