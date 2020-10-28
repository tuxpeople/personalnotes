# Bulding Multiarch images with Docker

## Disclaimer

1. This is, by no means, a perfect and/or prodction ready way to to this.
1. I assume docker is already configured for the use of buildx.
1. It's assumed that the variables DOCKER_USERNAME and DOCKER_PASSWORD ar set.

## Simple example
	mkdir test
	cd test
	cat <<EOF > Dockerfile
	FROM debian:buster-slim
	RUN apt-get update \
	  && apt-get install -y curl \
	  && rm -rf /var/lib/apt/lists/*
	ENTRYPOINT [ "curl" ]
	EOF
	docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD"
	docker buildx create --use
	docker buildx build --push --platform linux/arm/v7,linux/arm64/v8,linux/amd64 --tag $DOCKER_USERNAME/multiarch-example:buildx-latest .
    
If you now go to https://hub.docker.com/r/$DOCKER_USERNAME/multiarch-example/tags you should see something like this:

![Multi-Arch image on DockerHub]({{site.baseurl}}/images/multi_arch_image_on_dockerhub.png)

You've been successfull.

## Ressources

Those ressources may be helpful to go further:

https://www.docker.com/blog/multi-arch-images/
https://www.docker.com/blog/multi-arch-build-and-images-the-simple-way/
https://www.docker.com/blog/multi-arch-build-what-about-travis/
https://medium.com/@artur.klauser/building-multi-architecture-docker-images-with-buildx-27d80f7e2408

