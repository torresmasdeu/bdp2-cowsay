# bdp2-cowsay
This repository contains a Dockerfile to build a Docker image that derives from the ubuntu image and has the program cowsay installed in it. Exercise part 1 of the Containers slides part 2 of the BDP2 course.

Written by Laia Torres Masdeu.

**List of contents**
- [1. Part 1](#1-part-1)
    - [1.1. Create a new local git repo on VM1](#11-create-a-new-local-git-repo-on-vm1)
    - [1.2. Create a Dockerfile](#12-create-a-dockerfile)
    - [1.3. Push the repo on VM1 to bdp2-cowsay on GitHub](#13-push-the-repo-on-vm1-to-bdp2-repo-on-github)
    - [1.4. Build the image on VM1 out of the Dockerfile](#14-build-the-image-on-vm1-out-of-the-dockerfile)
    - [1.5. Run the image interactively](#15-run-the-image-interactively)
- [2. Part 2](#2-part-2)
    - [2.1. Process a message from an environment variable](#21-process-a-message-from-an-environment-variable)
    - [2.2. Run the image non-interactively](#22-run-the-image-non-interactively)
    - [2.3. Process a set of messages from a file](#23-process-a-set-of-messages-from-a-file)
    - [2.4. Specify flags to the `cowsay` command](#24-specify-flags-to-the-cowsay-command)
    - [2.5. Process a set of messages from a file with a wait time](#25-process-a-set-of-messages-from-a-file-with-a-wait-time)
  
## 1. Part 1
### 1.1. Create a new local git repo on VM1
First of all, create a directory for this exercise and go there:
```
mkdir -p ~/containers/cowsay
cd ~/containers/cowsay
```

And initialise git in this directory to create the local repo:
```
git init
```

### 1.2. Create a Dockerfile
Create the Dockerfile (I used vi `vi Dockerfile`) and include the following lines:
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay
```

### 1.3. Push the repo on VM1 to bdp2-repo on GitHub
Once the Dockerfile is created, the file has to be added to git:
```
git add Dockerfile
```

Check the status, to ensure it has been added:
```
git status
```

And commit the changes:
```
git commit -m "First commit of my cowsay Dockerfile"
```

And check again the status (`git status`) to check that all changes are committed.

Next, you have to tell git the location of the "origin" of the remote repo, the GitHub repository (already created on GitHub) and push the changes to GitHub. It asks for the GitHub username and password (GitHub Personal Access Token).
```
git remote add origin https://github.com/torresmasdeu/bdp2-cowsay.git
```

And finally, push everything that has been committed in the "master" branch of the local repo to the "origin":
```
git push -u origin master
```

### 1.4. Build the image on VM1 out of the Dockerfile
To build the image on VM1 from the Dockerfile, just run the `docker build` command, indicating the name of the image (`-t`) and the path of the Dockerfile that contains the information to build the image (`.`):
```
docker build -t my-cowsay .
```

### 1.5. Run the image interactively
Once it is built, run it interactivelly (`-it`) by using the `docker run` command. `--rm` removes the container once you exit it:
```
docker run --rm -it my-cowsay
```
Finally, check that `cowsay` works:
```
/usr/games/cowsay "Hello BDP2 Students"
```

This is the output:
```
 _____________________
< Hello BDP2 Students >
 ---------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

## 2. Part 2
### 2.1. Process a message from an environment variable
An environment variable can be specified in two ways:
#### 1. In the `docker run` command, with the `-e` flag, using the original Dockerfile.
```
docker run --rm -it -e MESSAGE="This is a message from an environment variable not specified in the Dockerfile" my-cowsay
```
And then specifying the variable name as the message to pass to the `cowsay` command:
```
/usr/games/cowsay $MESSAGE
```
This is the output:
```
 _______________________________________
/ This is a message from an environment \
| variable not specified in the         |
\ Dockerfile                            /
 ---------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
#### 2. Within the Dockerfile (with the `ENV` command). In this case, a new Dockerfile (`vi Dockerfile`) has to be made:
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay

ENV MESSAGE="This is a message from an environment variable!"
```
This is the `docker run` command:
```
docker run --rm -it my-cowsay
```

And the output (the `cowsay` command is the same as in the previous case):
```
 _______________________________________
/ This is a message from an environment \
\ variable!                             /
 ---------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
Both cases work in the same way. 

If an environment variable is defined in the Dockerfile (second case), it can be overwritten using the `-e` flag when executing the docker run command.
```
docker run --rm -it -e MESSAGE="This is a message from the docker run command" my-cowsay
```

And the output:
```
 _______________________________________
/ This is a message from the docker run \
\ command                               /
 ---------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
In all the cases, the environment variable used is called after running the cowsay command in interactive mode.

### 2.2. Run the image non-interactively
To be able to run the image non-interactively, the `cowsay` command must be run from within the Dockerfile. This can be done using either the `CMD` or `ENTRYPOINT` instructions. I have decided to use the former. 
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay

CMD /usr/games/cowsay $MESSAGE
```
I have decided to specify the `MESSAGE` environment variable directly from the `docker run` command:
```
docker run --rm -e MESSAGE="This is a message run non-interactively" my-cowsay
```

And the output:
```
 _________________________________________
< This is a message run non-interactively >
 -----------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

### 2.3. Process a set of messages from a file
To read the messages from a file, I have decided to iterate through the lines of the file and execute the `cowsay` command for each of the contents of each line (`while read line`). The name of the file, which is the same on the VM and in the docker container, is specified by the `COWFILE_NAME` environment variable, whereas the directory to the location of the file in the VM is specified by the `COWFILE_DIR` environment variable.

This is the Dockerfile:
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay
COPY $COWFILE_DIR/$COWFILE_NAME /usr/games/

CMD while read -r line; do /usr/games/cowsay "$line"; done </usr/games/$COWFILE_NAME
```

The command to run it:
```
docker run --rm -e COWFILE_DIR="~/containers/cowsay/part2" -e COWFILE_NAME="cowsay_messages.txt" my-cowsay
```

And the output:
```
 _______
< hello >
 -------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
 ______________
< good morning >
 --------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
 ____________________
< how are you doing? >
 --------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
### 2.4. Specify flags to the `cowsay` command
To specify the flag to the cowsay command, I have used the `FLAG` environment variable. This is the Dockerfile:
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay

CMD /usr/games/cowsay $FLAG $MESSAGE
```

As before, I have specified it directly from the `docker run` command:
```
docker run --rm -e FLAG='-t' -e MESSAGE="I'm a tired cow" my-cowsay
```

And the ouput:
```
 _________________
< I'm a tired cow >
 -----------------
        \   ^__^
         \  (--)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

### 2.5. Process a set of messages from a file with a wait time
I have defined the wait time between each line specifying the `WAIT_TIME` environment variable using the sleep command. To provide a default time, I have given `WAIT_TIME` a value in the Dockerfile:
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay
COPY $COWFILE_DIR/$COWFILE_NAME /usr/games/

ENV WAIT_TIME=1

CMD while read -r line; do /usr/games/cowsay "$line"; sleep $WAIT_TIME; done </usr/games/$COWFILE_NAME
```

Which is run with the following command:
```
docker run --rm -e COWFILE_DIR="~/containers/cowsay" -e COWFILE_NAME="cowsay_messages.txt" my-cowsay
```

To overrun it, I have specified the new value of `WAIT_TIME` in the docker run command:
```
docker run --rm -e COWFILE_DIR="~/containers/cowsay" -e COWFILE_NAME="cowsay_messages.txt" -e WAIT_TIME="2" my-cowsay
```

To ensure it has worked, I have calculated the time the `docker run` command takes by using the `date` command with the `%s` option, which displays the Epoch time (time that has elapsed since January 1, 1970) in seconds, before and after the `docker run` command, and then calculated the time elapsed to run it by subtracting the end time to the begin time (4 seconds with a wait time of 1 second and 7 seconds with a wait time of 2 seconds):
```
start=`date +%s`
command
end=`date +%s`

runtime=$((end-start))

echo 'It takes' $runtime 'seconds'
```
