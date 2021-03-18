# Build docker image and create compose file
  
## Docker and Docker file  

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

Build the docker image and tag it `mypyspark` note there are 17 steps defined in your Dockerfile: 
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
```

## Docker-compose and docker-compose.yml file  

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

Let's add some data to `raw_tweets.txt`
```
cat <<tweets > raw_tweets.txt
{"created_at": "Fri Aug 10 10:31:12 +0000 2018", "id": 1027864936314228736, "id_str": "1027864936314228736", "text": "[Tutorial] This tutorial explains how to manually provision an AWS EC2 virtual machine using Terraform.\u2026 https://t.co/0Ca3WuMbQ9", "display_text_range": [0, 140], "source": "<a href=\"http://www.hubspot.com/\" rel=\"nofollow\">HubSpot</a>", "truncated": true, "in_reply_to_status_id": null, "in_reply_to_status_id_str": null, "in_reply_to_user_id": null, "in_reply_to_user_id_str": null, "in_reply_to_screen_name": null, "user": {"id": 1689418998, "id_str": "1689418998", "name": "Shippable", "screen_name": "BeShippable", "location": "Seattle, WA", "url": "http://shippable.com", "description": "Accelerate & Scale DevOps With Assembly Lines. Support for multiple Operating Systems, including Windows, Mac OS, and CentOS.", "translator_type": "none", "protected": false, "verified": false, "followers_count": 2452, "friends_count": 4989, "listed_count": 188, "favourites_count": 1362, "statuses_count": 2639, "created_at": "Wed Aug 21 23:10:56 +0000 2013", "utc_offset": null, "time_zone": null, "geo_enabled": false, "lang": "en", "contributors_enabled": false, "is_translator": false, "profile_background_color": "C0DEED", "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png", "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png", "profile_background_tile": false, "profile_link_color": "1DA1F2", "profile_sidebar_border_color": "C0DEED", "profile_sidebar_fill_color": "DDEEF6", "profile_text_color": "333333", "profile_use_background_image": true, "profile_image_url": "http://pbs.twimg.com/profile_images/704381883375718400/cmLmDBlh_normal.jpg", "profile_image_url_https": "https://pbs.twimg.com/profile_images/704381883375718400/cmLmDBlh_normal.jpg", "profile_banner_url": "https://pbs.twimg.com/profile_banners/1689418998/1456772686", "default_profile": true, "default_profile_image": false, "following": null, "follow_request_sent": null, "notifications": null}, "geo": null, "coordinates": null, "place": null, "contributors": null, "is_quote_status": false, "extended_tweet": {"full_text": "[Tutorial] This tutorial explains how to manually provision an AWS EC2 virtual machine using Terraform. https://t.co/GzxE7Dt2d6 #devops #terraform #EC2 #AWS https://t.co/4F7M7rfc3m", "display_text_range": [0, 156], "entities": {"hashtags": [{"text": "devops", "indices": [128, 135]}, {"text": "terraform", "indices": [136, 146]}, {"text": "EC2", "indices": [147, 151]}, {"text": "AWS", "indices": [152, 156]}], "urls": [{"url": "https://t.co/GzxE7Dt2d6", "expanded_url": "https://hubs.ly/H0dlCMq0", "display_url": "hubs.ly/H0dlCMq0", "indices": [104, 127]}], "user_mentions": [], "symbols": [], "media": [{"id": 1027864934665871360, "id_str": "1027864934665871360", "indices": [157, 180], "media_url": "http://pbs.twimg.com/media/DkO1tlPW4AAeBwM.jpg", "media_url_https": "https://pbs.twimg.com/media/DkO1tlPW4AAeBwM.jpg", "url": "https://t.co/4F7M7rfc3m", "display_url": "pic.twitter.com/4F7M7rfc3m", "expanded_url": "https://twitter.com/BeShippable/status/1027864936314228736/photo/1", "type": "photo", "sizes": {"thumb": {"w": 150, "h": 150, "resize": "crop"}, "medium": {"w": 1200, "h": 608, "resize": "fit"}, "small": {"w": 680, "h": 344, "resize": "fit"}, "large": {"w": 1256, "h": 636, "resize": "fit"}}}]}, "extended_entities": {"media": [{"id": 1027864934665871360, "id_str": "1027864934665871360", "indices": [157, 180], "media_url": "http://pbs.twimg.com/media/DkO1tlPW4AAeBwM.jpg", "media_url_https": "https://pbs.twimg.com/media/DkO1tlPW4AAeBwM.jpg", "url": "https://t.co/4F7M7rfc3m", "display_url": "pic.twitter.com/4F7M7rfc3m", "expanded_url": "https://twitter.com/BeShippable/status/1027864936314228736/photo/1", "type": "photo", "sizes": {"thumb": {"w": 150, "h": 150, "resize": "crop"}, "medium": {"w": 1200, "h": 608, "resize": "fit"}, "small": {"w": 680, "h": 344, "resize": "fit"}, "large": {"w": 1256, "h": 636, "resize": "fit"}}}]}}, "quote_count": 0, "reply_count": 0, "retweet_count": 0, "favorite_count": 0, "entities": {"hashtags": [], "urls": [{"url": "https://t.co/0Ca3WuMbQ9", "expanded_url": "https://twitter.com/i/web/status/1027864936314228736", "display_url": "twitter.com/i/web/status/1\u2026", "indices": [105, 128]}], "user_mentions": [], "symbols": []}, "favorited": false, "retweeted": false, "possibly_sensitive": false, "filter_level": "low", "lang": "en", "timestamp_ms": "1533897072281"}
{"created_at": "Fri Aug 10 10:31:41 +0000 2018", "id": 1027865059706454016, "id_str": "1027865059706454016", "text": "[Tutorial] This tutorial explains how to manually provision an AWS EC2 virtual machine using Terraform.\u2026 https://t.co/V6NWZHdtEm", "source": "<a href=\"http://www.hubspot.com/\" rel=\"nofollow\">HubSpot</a>", "truncated": true, "in_reply_to_status_id": null, "in_reply_to_status_id_str": null, "in_reply_to_user_id": null, "in_reply_to_user_id_str": null, "in_reply_to_screen_name": null, "user": {"id": 1689418998, "id_str": "1689418998", "name": "Shippable", "screen_name": "BeShippable", "location": "Seattle, WA", "url": "http://shippable.com", "description": "Accelerate & Scale DevOps With Assembly Lines. Support for multiple Operating Systems, including Windows, Mac OS, and CentOS.", "translator_type": "none", "protected": false, "verified": false, "followers_count": 2452, "friends_count": 4989, "listed_count": 188, "favourites_count": 1362, "statuses_count": 2640, "created_at": "Wed Aug 21 23:10:56 +0000 2013", "utc_offset": null, "time_zone": null, "geo_enabled": false, "lang": "en", "contributors_enabled": false, "is_translator": false, "profile_background_color": "C0DEED", "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png", "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png", "profile_background_tile": false, "profile_link_color": "1DA1F2", "profile_sidebar_border_color": "C0DEED", "profile_sidebar_fill_color": "DDEEF6", "profile_text_color": "333333", "profile_use_background_image": true, "profile_image_url": "http://pbs.twimg.com/profile_images/704381883375718400/cmLmDBlh_normal.jpg", "profile_image_url_https": "https://pbs.twimg.com/profile_images/704381883375718400/cmLmDBlh_normal.jpg", "profile_banner_url": "https://pbs.twimg.com/profile_banners/1689418998/1456772686", "default_profile": true, "default_profile_image": false, "following": null, "follow_request_sent": null, "notifications": null}, "geo": null, "coordinates": null, "place": null, "contributors": null, "is_quote_status": false, "extended_tweet": {"full_text": "[Tutorial] This tutorial explains how to manually provision an AWS EC2 virtual machine using Terraform. https://t.co/WneZYf5ELA #devops #terraform #EC2 #AWS", "display_text_range": [0, 156], "entities": {"hashtags": [{"text": "devops", "indices": [128, 135]}, {"text": "terraform", "indices": [136, 146]}, {"text": "EC2", "indices": [147, 151]}, {"text": "AWS", "indices": [152, 156]}], "urls": [{"url": "https://t.co/WneZYf5ELA", "expanded_url": "https://hubs.ly/H0dlCMt0", "display_url": "hubs.ly/H0dlCMt0", "indices": [104, 127]}], "user_mentions": [], "symbols": []}}, "quote_count": 0, "reply_count": 0, "retweet_count": 0, "favorite_count": 0, "entities": {"hashtags": [], "urls": [{"url": "https://t.co/V6NWZHdtEm", "expanded_url": "https://twitter.com/i/web/status/1027865059706454016", "display_url": "twitter.com/i/web/status/1\u2026", "indices": [105, 128]}], "user_mentions": [], "symbols": []}, "favorited": false, "retweeted": false, "possibly_sensitive": false, "filter_level": "low", "lang": "en", "timestamp_ms": "1533897101700"}
tweets
```{{execute}}
  
Let's start what's defined in the docker-compose file in detachmed mode:
```
docker-compose up -d
```{{execute}}

Now we can start a bash shell inside the container with the command:
```
docker exec -it mypyspark bash
```{{execute}}

You can see the docker containers run information by doing:
```
docker ps
```{{execute}}