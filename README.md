Fully dockerized Gitlab Runner
------------------------------
This image registers and runs a single gitlab runner.

If you need multiple runners, simply start multiple containers.

We have mainly being using this images to test projects by building running docker containers.

The usage examples below focus on running this image with the necessary configuration to let you
spawn docker images from within this image.

Note that we share the docker.sock instead of using some dind image. please read [this blog](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) post to understand our motivations to avoid Docker in Docker strategy.


Manual usage example
-------------
```
docker run -d --name gitlab-runner \
-v /var/run/docker.sock:/var/run/docker.sock \
-v `which docker`:`which docker` \
-v /home/gitlab-runner:/home/gitlab-runner \
gitlab-runner \
-u https://ci.gitlab.com/ \
-r yourregistrationtoken \
-d 'Your Runner Name' \
-t 'your, tags'
```



Volumes
-------
Mount the docker socket so that builds can spawn docker containers through the host's docker.
```
/var/run/docker.sock:/var/run/docker.sock
```

Mount the docker binary so that the versions match. Adapt this if the docker resides in another directory.
```
/usr/bin/docker:/usr/bin/docker
```

Mount the work directory. The directory must be the same on the host and inside the container, so that new containers can mount volumes from the current build using `pwd` for example.
```
/home/gitlab-runner:/home/gitlab-runner
```

We actually do no recommend mounting the config directory /etc/gitlab-runner as this container is intended to be ephemeral and run a single runner.

Tutum deployment
-----

### Run command
```
-u https://ci.gitlab.com/ -r yourregistrationtoken -d 'Your Runner Name' -t 'your, tags'
```

### Mount volumes
In tutum you have to mount the volumes via the gui

* /var/run/docker.sock:/var/run/docker.sock
* /usr/bin/docker:/usr/bin/docker
* /home/gitlab-runner:/home/gitlab-runner

Note that you can not use `which docker` for mounting the docker binary. So you might have to adapt the location of the docker binary depending on your host's docker binary location. The easiest way to check that your docker is accessible is to open a terminal in your container (possible through the Tutum gui), and type `docker info`.
