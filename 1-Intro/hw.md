## Labs: Basic Docker Commands

1. How many containers are running on this host?

* Run the command `docker ps` and count the number of running containers.

2. How many images are available on this host?

* Run the command `docker images` and count the number of available images.

3. Run a container using the redis image

* Run the command `docker run hello-world`

4. Stop the container you just created

* Hit `CTRL+C` if you are on the container's terminal. Or else run `docker stop <container-id>`

5. How many containers are RUNNING on this host now?

* Run the command `docker ps` and count the number of containers that are running.

6. How many containers are PRESENT on the host now? (Including both Running and Not Running ones)

* Run the command `docker ps -a`

7. What is the image used to run the `hello-world` container?

* Run the `docker ps` command and check under the `IMAGE` column.

8. What is the name of the container created using the `nginx` image?(if there is bi nginx image; download it with `docker run`)

* Run the `docker ps` command and look at the `NAMES` column.

9. What is the ID of the container that uses the `nginx` image and is not running?


* Run the `docker ps -a` command and identify the ID of the container that uses `nginx` image.

10. Stop the contaienr that is created by `nginx` image. What is the state of the stopped container?

* Run the command `docker ps -a` and look at the `STATUS` column.

11. Delete all containers from the Docker Host.Both Running and Not Running ones. Remember you may have to stop containers before deleting them.

* To stop containers run the command `docker stop <container id | container name>`
And then to delete them run `docker rm <container id | container name>`

12. Delete the `hello-world` Image.

* Run the command `docker rmi hello-world`

13. You are required to pull a docker image which will be used to run a container later. Pull the image `nginx:1.14-alpine`

- **Only pull the image, do not create a container.**

* Run the command `docker pull nginx:1.14-alpine`

14. Run a container with the `nginx:1.14-alpine` image and name it `webapp`

* Run the command `docker run -d --name webapplikasyon nginx:1.14-alpine` and check the status of created container by `docker ps` command.
