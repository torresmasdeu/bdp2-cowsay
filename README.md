# bdp2-cowsay
This repository contains a Dockerfile to build a Docker image that derives from the ubuntu image and has the program cowsay installed in it. Exercise part 1 of the Containers slides part 2 of the BDP2 course.

Written by Laia Torres Masdeu.

**List of contents**
1. Create a new local git repo on VM1
2. Create a Dockerfile
3. Push the repo on VM1 to bdp2-cowsay on GitHub
4. Build the image on VM1 out of the Dockerfile
5. Run the image interactively

## 1. Create a new local git repo on VM1
First of all, create a directory for this exercise and go there:
```
mkdir -p ~/containers/cowsay
cd ~/containers/cowsay
```

And initialise git in this directory to create the local repo:
```
git init
```

## 2. Create a Dockerfile
Create the Dockerfile (I used vi `vi Dockerfile`) and include the following lines:
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay
```

## 3. Push the repo on VM1 to bdp2-repo on GitHub
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

## 4. Build the image on VM1 out of the Dockerfile
To build the image on VM1 from the Dockerfile, just run the `docker build` command, indicating the name of the image (`-t`) and the path of the Dockerfile that contains the information to build the image (`.`):
```
docker build -t my-cowsay .
```

## 5. Run the image interactively
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
