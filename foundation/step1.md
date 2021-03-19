# Step 1

We are running on Ubuntu, see: `cat /etc/*release*`{{execute}}  

Install docker 

```
apt-get install docker-ce docker-ce-cli containerd.io
```{{execute}}

And verify the installation by executing: `docker info`{{execute}}

Install docker-compose
```
sudo apt install docker-compose -y
```{{execute}}

And verify the installtion by executing: `docker-compose -info`{{execute}}

Check the currently installed docker images: `docker images`{{execute}}