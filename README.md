# bdp2-cowsay
This repository contains a Dockerfile to build a Docker image that derives from the ubuntu image and has the program cowsay installed in it. Exercise part 1 of the Containers slides part 2 of the BDP2 course.

Written by Laia Torres Masdeu.

**List of contents**
1. Create a new local git repo on VM1
2. Create a Dockerfile
3. Push the repo on VM1 to bdp2-cowsay on GitHub.
4. Build the image on VM1 out of the Dockerfile
5. Run the image interactively.

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

