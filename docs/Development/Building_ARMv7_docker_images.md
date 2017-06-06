## Overview
Generally trying to use the official Dockerfiles. As can be seen, the approach is typically very straightforward. Using armhf docker images as base.



### Python3
```
git clone https://github.com/docker-library/python
cd $(ls -d python/*/ | sort | tail -n 1)alpine #Basically cd's to latest available version in repo
sed -i 's/FROM /FROM armhf\//g' Dockerfile
sed -i 's/\&\& gpg --keyserver/\&\& gpg-agent --daemon \&\& gpg --keyserver/g' Dockerfile
TAG=$(sed -n -e 's/ENV PYTHON_VERSION //p' Dockerfile)
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:python_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:python_arm_${TAG}
```
Added `gpg-agent --daemon` to fix `gpg: can't connect to the agent: IPC connect call failed` error.


### Python2
```
git clone https://github.com/docker-library/python
cd $(ls -d python/2*/ | sort | tail -n 1)alpine
sed -i 's/FROM /FROM armhf\//g' Dockerfile
sed -i 's/\&\& gpg --keyserver/\&\& gpg-agent --daemon \&\& gpg --keyserver/g' Dockerfile
TAG=$(sed -n -e 's/ENV PYTHON_VERSION //p' Dockerfile)
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:python_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:python_arm_${TAG}
```

### Java
```
git clone https://github.com/docker-library/openjdk
cd openjdk/8-jdk/alpine
sed -i 's/FROM /FROM armhf\//g' Dockerfile
TAG=$(sed -n -e 's/ENV JAVA_VERSION //p' Dockerfile)
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:openjdk_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:openjdk_arm_${TAG}
```

### Certbot
```
git clone https://github.com/certbot/certbot
cd certbot
sed -i 's/FROM python:2-alpine/FROM registry.gitlab.com\/rihardst\/cloud_project_infrastructure:python_arm_latest2/g' Dockerfile
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:certbot_arm .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:certbot_arm
```
Using previously built Python2 as base image. Everything else the same


## Automated builds:
### Netdata
```
git clone https://github.com/firehol/netdata
cd netdata
sed -i 's/FROM /FROM armhf\//g' Dockerfile
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:docker_netdata_arm .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:docker_netdata_arm
```

### PostgreSQL
```
git clone https://github.com/docker-library/postgres/
cd postgres/9.6/alpine
sed -i 's/FROM /FROM armhf\//g' Dockerfile
TAG=$(sed -n -e 's/ENV PG_VERSION //p' Dockerfile)
docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com/rihardst/cloud_project_infrastructure
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:postgres_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:postgres_arm_${TAG}
```
Check out the login with gitlab-ci-token, which allows to push to the Gitlab container registry

### Nginx
```
git clone https://github.com/nginxinc/docker-nginx
cd docker-nginx/mainline/alpine
sed -i 's/FROM /FROM armhf\//g' Dockerfile
TAG=$(sed -n -e 's/ENV NGINX_VERSION //p' Dockerfile)
docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com/rihardst/cloud_project_infrastructure
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:nginx_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:nginx_arm_${TAG}
```