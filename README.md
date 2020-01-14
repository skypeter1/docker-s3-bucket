# Docker-s3-bucket
Mounts an s3 bucket inside a docker container

This is a simple docker dontainer based on ubuntu that mounts an s3 bucket in a directory in the filesystem of the image, using s3-fuse https://github.com/s3fs-fuse/s3fs-fuse/ 

## Config

Clone the repo in your localhost

```Shell
git clone https://github.com/skypeter1/docker-s3-bucket
```

Then, go to the Dockerfile and modify the next values with yours

First go to line 22 and set the directory that you want to use, mine is var/www 

```Dockerfile
WORKDIR /var/www
```

and also the name of the directory that you will use as your mountpoint on line 23, (I used s3 as name for my example)

```Dockerfile
RUN mkdir s3
```

Include your aws credentials on line 26 and 28, for more info about how to create AWS secret access key id you can check 
https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys

```Dockerfile
ARG AWS_ACCESS_KEY=YOURAWSACCESSKEY
ARG AWS_SECRET_ACCESS_KEY=YOURAWSSECRETACCESSKEY
```

The idea is to mount an s3 storage inside a directory that we will create or select if it's already avaliable on our current ubuntu filesystem. 
Enter the name of the directory that you have choose as the mountpoint for your s3 bucket

```Dockerfile
ARG S3_MOUNT_DIRECTORY=/var/www/s3
```

I have included a start-script.sh file that will kickstart the mounting from within the container
Bear in mind that this will only work if you run docker on privileged mode

## Build the image

Once you've modified the Dockerfile let's build the image
Go to the root of the directory and 

```Shell
docker build--no-cache .
```

You will also want to add a tag to your image id once you've built it like:

```Shell
docker tag ff47cca76424 mydockerhubrepo/s3:latest
```

Then run the container in privileged mode like this

```Shell
docker run -d --privileged -i ff47cca76424 bash
```

If you want to check the connection just ssh into to the container using the container id (you can check this running docker ps)

```Shell
docker exec -it b6c43f7d1d72 bash
```

and check the mounted filesystem

```Shell
df -h
```

You should be able to see a s3fs type of filesystem and mounted on your selected location, in the case of this example /var/www/s3

Go to your directory and create a simple index.html

```Shell
cd /var/www/s3
echo Hello world > index.html
```

Now go to the s3 console on AWS and you should be able to see your recently created file
