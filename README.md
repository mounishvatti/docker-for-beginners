# docker-for-beginners

```warp-runnable-command
cd Documents
```
### Now let us make a folder named DockerBeginner
```warp-runnable-command
mkdir DockerBeginner
```
### Navigate to the directory
```warp-runnable-command
cd DockerBeginner
```
### clone a repository into this folder
```warp-runnable-command
git clone https://github.com/dockersamples/linux_tweet_app
```
```warp-runnable-command
ls
```
```warp-runnable-command
cd linux_tweet_app
```
### **Run a single task in an Alpine Linux container**
```warp-runnable-command
docker container run alpine hostname
```
### List all containers
```warp-runnable-command
docker container ls -all
```
### **Run an interactive Ubuntu container**
```warp-runnable-command
docker container run --interactive --tty --rm ubuntu bash
```
### list the contents of root directory in the container
```warp-runnable-command
ls /
```
### Show running processes in the container
```warp-runnable-command
ps aux
```
### Shows which linux distro is running
```warp-runnable-command
cat etc/issue
```
### leave the session
```warp-runnable-command
exit
```
## **Run a background MySQL container**
### Run a new MySQL container
```warp-runnable-command
docker container run \
 --detach \
 --name mydb \
 -e MYSQL_ROOT_PASSWORD=<your-secret-password> \
 mysql:latest
```
### List the running containers
```warp-runnable-command
docker container ls
```
### check logs from MySQL Docker Container
```warp-runnable-command
docker container logs mydb
```
### look at the processes running inside the container
```warp-runnable-command
docker container top mydb
```
```warp-runnable-command
docker exec -it mydb \
mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
```
### Connect to a new shell process inside an already running container
```warp-runnable-command
docker exec -it mydb sh
```
### check version number
```warp-runnable-command
 mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
```
```warp-runnable-command
exit
```
***
# **Task 2\: Package and run a custom app using Docker**
```warp-runnable-command
cd ~/linux_tweet_app
```
### Display contents of the Dockerfile
```warp-runnable-command
cat Dockerfile
```
```warp-runnable-command
export DOCKERID=mounishvatti
```
```warp-runnable-command
echo $DOCKERID
```
Use the `docker image build` command to create a new Docker image using the instructions in the Dockerfile\.
```warp-runnable-command
docker image build --tag $DOCKERID/linux_tweet_app:1.0 .
```
Use the `docker container run` command to start a new container from the image you created\.
```warp-runnable-command
docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 $DOCKERID/linux_tweet_app:1.0
```
### Now go to localhost on your default browser
### Once you’ve accessed your website\, shut it down and remove it\.
```warp-runnable-command
docker container rm --force linux_tweet_app
```
# **Task 3\: Modify a running website**
### Start our web app with a bind mount
use the `--mount` flag to mount the current directory on the host into `/usr/share/nginx/html`inside the container\.
```warp-runnable-command
mounishvatti@Mounishs-MacBook-Air linux_tweet_app %
 docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html \
 $DOCKERID/linux_tweet_app:1.0
```
### Modifying the website
Copy a new `index.html` into the container\.
```warp-runnable-command
cp index-new.html index.html
```
Stop and remove the currently running container\.
```warp-runnable-command
docker rm --force linux_tweet_app
```
Rerun the current version without a bind mount\.
```warp-runnable-command
mounishvatti@Mounishs-MacBook-Air linux_tweet_app %
docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 $DOCKERID/linux_tweet_app:1.0
```
Stop and remove the current container
```warp-runnable-command
docker rm --force linux_tweet_app
```
# Update the image
To persist the changes you made to the `index.html` file into the image\, you need to build a new version of the image\.
1. Build a new image and tag it as `2.0`
```warp-runnable-command
docker image build --tag $DOCKERID/linux_tweet_app:2.0 .
```
Let’s look at the images on the system\.
```warp-runnable-command
docker image ls
```
Run a new container from the new version of the image\.
```warp-runnable-command
docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 $DOCKERID/linux_tweet_app:2.0
```
Run another new container\, this time from the old version of the image\.
```warp-runnable-command
docker container run \
 --detach \
 --publish 8080:80 \
 --name old_linux_tweet_app \
 $DOCKERID/linux_tweet_app:1.0
```
Running both the versions simultaneously\, V1 on localhost \& V2 on localhost\:8080
## **Push your images to Docker Hub**
List the images on your Docker host\.
```warp-runnable-command
docker image ls -f reference="$DOCKERID/*"
```
Before you can push your images\, you will need to log into Docker Hub\.
```warp-runnable-command
 docker login
```
Push version `1.0` of your web app using `docker image push`\.
```warp-runnable-command
 docker image push $DOCKERID/linux_tweet_app:1.0
```
Now push version `2.0`\.
```warp-runnable-command
 docker image push $DOCKERID/linux_tweet_app:2.0
```
