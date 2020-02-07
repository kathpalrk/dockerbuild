# How to build the Docker Images automatically when pushed to GitHUb.

Docker Hub integrates with GitHub (and Bitbucket), allowing you to automatically build your container’s image when new code is pushed. The tag for your image can be extracted from your repository’s tags (or branches) and automated tests can be executed to ensure your image was built as expected before the image becomes available for download. In this piece I will show you how to quickly setup such a workflow.

#Create Your GitHub Repo and Add a Dockerfile

The first step is, obviously, to setup your repository on GitHub:

Create a new Repo under GITHUB:

Next, clone your new repository locally, add a sample Dockerfile, and push the changes back to the repository:

for example:- I've choosed "hello-world"

git clone https://github.com/kathpalrk/dockerbuild.git

echo "FROM hello-world" >> Dockerfile

git add Dockerfile

git commit -m "Iniitial Commit "

git push origin  master

As you can see, the Dockerfile we created is as simple as it gets — just using the official hello-world image.

#Enabling Automated Builds on Docker Hub

To enable automated builds you need to connect your Docker Hub account with your account on GitHub. Login to Docker Hub and go to Account Settings > Linked Accounts. Scroll-down to find GitHub and click on the Connect link. You will be taken through the usual OAuth process where you login to GitHub (if not already logged-in) and authorise Docker to access your GitHub repositories. If everything goes well, you should receive a notification from GitHub:

Now it’s time to create the Docker Hub repository and link it to your GitHub repository:

There are a few things going on here, so let us break them down:
Name your repo

Not much to say here, just using a meaningful name. I opt for the same name as my GitHub repository.

	Choose GitHub repo
In the previous steps you connected your Docker Hub to GitHub, so here you should see a list of all your organisations and repositories in them. Choose the repository you created in the previous step.

Set up build rules

Here’s the really interesting part. Since code can be pushed at anytime, you should let Docker Hub know when your image should be build and which tag should have that image assigned to it. You configure both using Build rules.

Just click on Create for now (do not click Create & Build as this will trigger a manual build).

Build Rules
To let Docker Hub know how and when to automatically build your images you can specify Build rules. You may have multiple rules that apply in parallel, effectively having multiple tags assigned to your images with just one git-push:

#The build rules specify how to build source into Dockerimages

Sourcetype:- TAG 
Source:-  /^[0-9.]+$/  

DockerTag:-  {sourceref}
Dockerfile:-  Dockerfile


Select the Branch Options also which would get automatically.

On the above figure we set up two different rules — let’s see what they do.

Tag rule

A “tag rule” allows Docker Hub to start building an image upon discovery of a new tag in your git repository. This should probably be your preferred way to build images for the official releases of your image.

As git tags can be arbitrary and contain anything a developer might choose, Docker Hub allows you to define a Regular Expression, in order to identify which part of a git tag should become part of the tag for the Docker image. In the example above we chose /^[0-9.]+$/ as the regular expression with which the image tag is extracted. Practically speaking, this tells Docker Hub that our git tags are eligible for automated builds, should be in the form of numbers with dots, and that should be the only text of the tag. The image tag that’s extracted and assigned to it is denoted by the {sourceref} attribute entered under Docker Tag.

Branch rule

A “branch rule” allows Docker Hub to start building an image upon activity on a specific branch. The typical use case here is to have such a rule to build a latest image based on the daily activity of your git repository. The branch to be monitored is entered under Source and the tag to be assigned to the Docker images built from it under Docker Tag.


git tag 1.0.0

git push --tags

Indeed, a couple of minutes later Docker Hub has automatically built and tagged the 1.0.0 image:

To also see our latest image being created automatically we need to push a new change in the master branch:

echo "MAINTAINER Rahul Kathpal <kathpalrk@gmail.com> >> Dockerfile

git add DockerFile

git push 

Docker Hub provides you two additional screen on which you can monitor your builds:

Hope you guys enjoyed this & Share you feedback on this.

