#### Docker File Creation 
```
FROM httpd:2.2
    
RUN mkdir /mydir

WORKDIR /mydir

RUN date > date.txt

COPY  ./content/  /usr/local/apache2/htdocs/

CMD ["httpd-foreground"]
```
#### Building images
```
[root@ip-172-31-32-178 ~]# docker build -t image:1 .
Sending build context to Docker daemon  5.603MB
Step 1/6 : FROM httpd:2.2
 ---> e06c3dbbfe23
Step 2/6 : RUN mkdir /mydir
 ---> Running in 2a168250ab08
Removing intermediate container 2a168250ab08
 ---> 268316f22e04
Step 3/6 : WORKDIR /mydir
 ---> Running in 982aeb9ac8b7
Removing intermediate container 982aeb9ac8b7
 ---> e75ee09cb90b
Step 4/6 : RUN date > date.txt
 ---> Running in 726b930159bf
Removing intermediate container 726b930159bf
 ---> b7e221c5780f
Step 5/6 : COPY ./content/ /usr/local/apache2/htdocs/
 ---> 31095015858f
Step 6/6 : CMD ["httpd-foreground"]
 ---> Running in 6320916cc111
Removing intermediate container 6320916cc111
 ---> b4930ba5b4a0
Successfully built b4930ba5b4a0
Successfully tagged image:1
[root@ip-172-31-32-178 ~]# docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
image               1                   b4930ba5b4a0        8 seconds ago       177MB
httpd               2.2                 e06c3dbbfe23        23 months ago       171MB
```
#### Creating container with new inage
```
[root@ip-172-31-32-178 ~]# docker container run -d --rm -p 80:80 --name tuttu image:1
46492b1c355437683a7832954ca7365e2af85994a85327397f907f000caf14be
[root@ip-172-31-32-178 ~]# docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                NAMES
46492b1c3554        image:1             "httpd-foreground"   48 seconds ago      Up 47 seconds       0.0.0.0:80->80/tcp   tuttu
[root@ip-172-31-32-178 ~]# docker exec tuttu pwd
/mydir
```
#### To check logs of the container 
```
[root@ip-172-31-32-178 ~]# docker logs tuttu
httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2 for ServerName
[Tue Dec 17 17:16:13 2019] [warn] Init: Session Cache is not configured [hint: SSLSessionCache]
httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2 for ServerName
[Tue Dec 17 17:16:13 2019] [notice] Digest: generating secret for digest authentication ...
[Tue Dec 17 17:16:13 2019] [notice] Digest: done
[Tue Dec 17 17:16:14 2019] [notice] Apache/2.2.34 (Unix) mod_ssl/2.2.34 OpenSSL/1.0.1t DAV/2 configured -- resuming normal operations
92.118.37.64 - - [17/Dec/2019:17:16:39 +0000] "GET / HTTP/1.0" 200 16328
```
#### To check docker port 
```
[root@ip-172-31-32-178 ~]# docker port tuttu
80/tcp -> 0.0.0.0:80
```
#### Re-tag a docker image 
```
See 'docker --help'
[root@ip-172-31-32-178 ~]# docker tag image:1 tuttu7/image:latest
[root@ip-172-31-32-178 ~]# docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tuttu7/image        1                   b4930ba5b4a0        8 minutes ago       177MB
tuttu7/image        latest              b4930ba5b4a0        8 minutes ago       177MB
image               1                   b4930ba5b4a0        8 minutes ago       177MB
httpd               2.2                 e06c3dbbfe23        23 months ago       171MB
```
#### Upload / Pushing Docker image to Docker Hub https://hub.docker.com/
```
[root@ip-172-31-32-178 ~]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: tuttu7
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[root@ip-172-31-32-178 ~]# docker push tuttu7/image:latest
The push refers to repository [docker.io/tuttu7/image]
94994b28080c: Pushed 
f08a805fe8c8: Pushed 
c5f89530cbb7: Pushed 
ab5efd5aec77: Mounted from library/httpd 
9058feb62b4a: Mounted from library/httpd 
3f7f50ced288: Mounted from library/httpd 
71436bd6f1c4: Mounted from library/httpd 
4bcdffd70da2: Mounted from library/httpd 
latest: digest: sha256:121c28bb4135c6f445c4422d864806af9333efa1487af98562eb36658203df8f size: 1991
```
#### Creating new version of the container 

- You just need to make any changes in the website contents and build a new image 
```
[root@ip-172-31-32-178 ~]# docker build -t image2 .
Sending build context to Docker daemon  5.605MB
Step 1/6 : FROM httpd:2.2
 ---> e06c3dbbfe23
Step 2/6 : RUN mkdir /mydir
 ---> Using cache
 ---> 268316f22e04
Step 3/6 : WORKDIR /mydir
 ---> Using cache
 ---> e75ee09cb90b
Step 4/6 : RUN date > date.txt
 ---> Using cache
 ---> b7e221c5780f
Step 5/6 : COPY ./content/ /usr/local/apache2/htdocs/
 ---> d8da83abd0b6
Step 6/6 : CMD ["httpd-foreground"]
 ---> Running in 5563b230d422
Removing intermediate container 5563b230d422
 ---> 6fa1b5254c2e
Successfully built 6fa1b5254c2e
Successfully tagged image2:latest


[root@ip-172-31-32-178 ~]# docker tag image2 tuttu/image:latest

[root@ip-172-31-32-178 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tuttu/image         latest              6fa1b5254c2e        2 minutes ago       177MB
image2              latest              6fa1b5254c2e        2 minutes ago       177MB
image               1                   b4930ba5b4a0        23 minutes ago      177MB
tuttu7/image        1                   b4930ba5b4a0        23 minutes ago      177MB
tuttu7/image        latest              b4930ba5b4a0        23 minutes ago      177MB
httpd               2.2                 e06c3dbbfe23        23 months ago       171MB


[root@ip-172-31-32-178 ~]# docker push tuttu7/image:latest
The push refers to repository [docker.io/tuttu7/image]
4f9b9c7688d0: Pushed 
f08a805fe8c8: Pushed 
c5f89530cbb7: Pushed 
ab5efd5aec77: Pushed 
9058feb62b4a: Pushed 
3f7f50ced288: Pushed 
71436bd6f1c4: Pushed 
4bcdffd70da2: Pushed 
latest: digest: sha256:dcbdf82dc5332c8d6d94054110a39d6bf648d791d2f6e9c8b871a62c0e5d5448 size: 1991

[root@ip-172-31-32-178 ~]# docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                NAMES
46492b1c3554        image:1             "httpd-foreground"   27 minutes ago      Up 27 minutes       0.0.0.0:80->80/tcp   tuttu

[root@ip-172-31-32-178 ~]# docker container stop tuttu
tuttu

[root@ip-172-31-32-178 ~]# docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

[root@ip-172-31-32-178 ~]# docker run -d --rm -p 80:80 --name tuttu tuttu7/image:latest
e2d081bf0a36a81264d8154de6134a026c67c7ed2929b16c0d170ce53ded0747

[root@ip-172-31-32-178 ~]# docker container ls
CONTAINER ID        IMAGE                 COMMAND              CREATED             STATUS              PORTS                NAMES
e2d081bf0a36        tuttu7/image:latest   "httpd-foreground"   4 seconds ago       Up 4 seconds        0.0.0.0:80->80/tcp   tuttu
```



