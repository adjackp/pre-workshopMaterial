---
title: "Setting up the ATLAS Environment"
teaching: 30
exercises: 30
questions:
- What is a "release"?
- How can I configure my laptop to use "ATLAS code"?
objectives:
- Setup your working environment with Docker
- Load the ATLAS release through an image
keypoints:
- A software release (e.g. `AnalysisBase`) is necessary to analyze ATLAS data.
- You will learn much more about the details of an ATLAS release on Tuesday!
- Docker will serve as the "portal" for all ATLAS work throughout this tutorial. It can be thought of a "virtual machine".
- You will learn much more about Docker on Thursday!
---

## Setting up "ATLAS"

Your collaborators in ATLAS produce a lot of amazing and powerful code that allows you to
perform complex tasks and carry out amazing analyses.  This code is generally referred to
as "[athena](https://gitlab.cern.ch/atlas/athena)". You can browse this code by clicking on
the previous link.  However, to be able to use this code you have to "get access" to it.
This is done by building a basic version of athena and producing a bunch of libraries
and header files which are subsequently bundled into what is referred to as a **release**.

You will learn much more about the structure of this release when we learn about [CMake](https://cmake.org)
on the second day of the workshop.  However, for now, you can view this "release" as a
pre-requisite to be able to write code to analyze ATLAS data (and Monte Carlo simulation!).

There are a few ways to get access to this pre-compiled codebase.  For the majority of this
workshop we will be using [Docker](https://www.docker.com/) to do this and working in an ```AnalysisBase```
release container.  You will learn more about the details of Docker later in the week of the workshop
and what is presented here is only the minimal setup which will allow you to work through the tutorial.



## Installing Docker

General information about Docker and details of installation can be found here: [Docker Installation](https://docs.docker.com/install/)
Depending on the operating system you are using on the local machine, links to specific instructions on the Docker website can be found below.


<div id="DockerOS"> {% comment %} Start of 'DockerOS' section. {% endcomment %}
  <div>
    <ul class="nav nav-tabs nav-justified" role="tablist">
      <li role="presentation" class="active"><a data-os="windows" href="#docker-windows" aria-controls="Windows" role="tab" data-toggle="tab">Windows</a></li>
      <li role="presentation"><a data-os="macos" href="#docker-macos" aria-controls="MacOS" role="tab" data-toggle="tab">MacOS</a></li>
      <li role="presentation"><a data-os="linux" href="#docker-linux" aria-controls="Linux" role="tab" data-toggle="tab">Linux</a></li>
    </ul>

    <div class="tab-content">
      <article role="tabpanel" class="tab-pane active" id="docker-windows">
        <p>
          <font color="red">It is usually recommended that you </font> <strong> DO NOT </strong> <font color="red">use Windows. It has caused problems in the past, very few individuals
          use this OS within the HEP community as most tools are designed for Unix-based systems. 
          If you do have a Windows machine, consider making your computer a dual-boot machine </font>-
          <a href="https://opensource.com/article/18/5/dual-boot-linux">Link to Directions</a>
          <br/>
          <br/>
          However, the support for Docker on Windows and has proven to be a viable option in recent Windows updates. Please follow the instructions below to try this setup.
          <br/>
          <br/>
          Download Docker for Windows <a href="https://docs.docker.com/docker-for-windows/install/">instructions</a>.<br>
          <br/>
          Docker Desktop for Windows is the Community Edition (CE) of Docker for Microsoft Windows. To download Docker Desktop for Windows, head to <a href="https://hub.docker.com/editions/community/docker-ce-desktop-windows">Docker Hub</a>. <br>
          <br/>
          Please read the relevant information on these pages, it should take no more than 5 minutes.
          <br/>
        </p>
      </article>
      <article role="tabpanel" class="tab-pane" id="docker-macos">
        <p>
          <br/>
          Download Docker for MacOS <a href="https://docs.docker.com/docker-for-mac/install/">instructions</a>.<br>
          <br/>
          Docker is a full development platform for creating containerized apps, and Docker Desktop for Mac is the best way to get started with Docker on a Mac. To download Docker Desktop for MacOS, head to <a href="https://hub.docker.com/editions/community/docker-ce-desktop-mac">Docker Hub</a>. <br>
          <br/>
          Please read the relevant information on these pages, it should take no more than 5 minutes.
          <br/>
          Another common way to install packages on Mac OSX is via the <a href="https://brew.sh/">homebrew</a> package manager.  In the case of docker, you can easily install
          docker by setting up homebrew and executing <code>brew cask install docker</code>.
          <br/>
        </p>
      </article>
      <article role="tabpanel" class="tab-pane" id="docker-linux">
        <p>
          <br/>
          Downloading and installing Docker for Linux may be slightly more difficult but please contact the organisers or tutors as soon as possible so they can help with any problems.<br>
          <br/>
          Here are the instructions for two popular Linux distributions:
          <li><a href="https://docs.docker.com/engine/install/centos/">CentOS</a></li>
          <li><a href="https://docs.docker.com/engine/install/ubuntu/">Ubuntu</a></li>
          <br/>
          Instructions for other Linux distributions can be found on the Docker docs pages.
          <br/>
          <br/>
          <font color="red">Be sure to read the Docker documentation on post-installation steps for Linux and managing Docker
          as a </font> <a href="https://docs.docker.com/engine/install/linux-postinstall/">non-root user</a>.
          <font color="red">This will allow you to edit files when you have started up the Docker container from your image. </font>
          <br/>
        </p>
      </article>
    </div>
  </div>
</div> {% comment %} End of 'docker' section. {% endcomment %}

> ## If something doesn't work
> Docker is a standard industry tool.  The directions above should "just work".  That said, if you spend **one hour** working
> by yourself and still find that you are having issues with the basic setup, **ASK SOMEONE**!!!  We have created a mattermost channel
> ([Link to Mattermost Channel](https://mattermost.web.cern.ch/signup_user_complete/?id=qp87x1fco7rj88k44rjhgmmube)) where you
> can dynamically ask tutors and other participants questions.  This pertains to setting up Docker as well as _every other portion of the bootcamp_.
>
> And more broadly, this is a very good general rule of thumb to get into.  If you are struggling with something, particularly if it is technical, don't needlessly
> waste your time thinking that "this is a stupid question, so I'm not allowed to ask".  There will be something to help you and that is
> what collaboration is all about anyways. So do one of the following :
> - Ask the person sitting next to you
> - Ask that helpful person on your analysis team or working group
> - Ask a skype channel or mattermost chat
> - Ask a mailing list
>
> When you are done, just be sure to say _"thank you"_. #teamworkmakesthedreamwork
{: .callout}


## Download an ATLAS AnalysisBase Release

Start by downloading the Docker image for the release you want using the `docker pull`. The Docker images get managed by
Docker behind the scenes (Again, more details in the Docker session), so you don’t have to worry about that yourself:

```bash
docker pull atlas/analysisbase:21.2.125
```

The image should be around 4GB in size, so make sure you have the space available on your hard drive.

> ## Latest Release
> These instructions will use a numbered release, which should be your default to work with.
>
> If you want to use the absolutely latest, bleeding edge version you can replace ```atlas/analysisbase:21.2.125```
> with ```atlas/analysisbase``` or ```atlas/analysisbase/latest```.  However, doing so is not recommended for this bootcamp.
> Though some times it can be fun to live on the edge, so go ahead, try to pull the very latest ATLAS `AnalysisBase` image.
>
{: .challenge}

## Starting the Release

Let's now test to see whether this worked.  Now that you have pulled the release image, you can use it to setup the ATLAS environment release.
This is done using the `docker run` command.

```bash
docker run --rm -it atlas/analysisbase:21.2.125 bash
```
This will change the look of command prompt and you should now see something similar to `[bash][atlas]:~ >` as your prompt.  If you
list the files in this directory, you should see one file called `release_setup.sh` if you're in your 'home' directory `/home/atlas/`.  Sourcing this file will
load the release that is contained within the image.  Try is
```bash
# If you're not there already
cd /home/atlas/
source release_setup.sh
```
If this worked properly and you have setup docker correctly, then you should see the following

```bash
[bash][atlas]:~ > source release_setup.sh 
Configured GCC from: /opt/lcg/gcc/8.3.0-cebb0/x86_64-centos7/bin/gcc
Configured AnalysisBase from: /usr/AnalysisBase/21.2.125/InstallArea/x86_64-centos7-gcc8-opt
[bash][atlas AnalysisBase-21.2.125]:~ > 
```

> ## The CVMFS Way
> One other common way to setup the analysis release is to use the [CernVM File System](https://cernvm.cern.ch/portal/filesystem)
> which is available on nearly all clusters within the collaboration.  In this case, you use the `setupATLAS` alias command
> to initialize the `asetup` ("_athena_(a) setup") command which is subsequently used to create the surrounding ATLAS coding environment
> by giving you access to the precompiled base release.  This would typically look something like
> ```
> -bash-4.2$ setupATLAS
> lsetup               lsetup <tool1> [ <tool2> ...] (see lsetup -h):
>  lsetup agis          ATLAS Grid Information System
>  lsetup asetup        (or asetup) to setup an Athena release
>  lsetup atlantis      Atlantis: event display
> ...
> -bash-4.2$ asetup AnalysisBase,21.2.125,here
> Using AnalysisBase/21.2.125 [cmake] with platform x86_64-slc6-gcc62-opt
>   at /cvmfs/atlas.cern.ch/repo/sw/software/21.2
> Test area: /afs/cern.ch/user/m/meehan/delme
> -bash-4.2$
> ```
> This is achieving precisely the same goal as what we are achieving with Docker in this bootcamp.  The primary benefit is that using Docker
> allows you to comfortably use your laptop for all of your work.  So, cluster down? (No Problem!), poor internet connection? (No Problem), ...
> you can now work all the time!
{: .callout}

{% include links.md %}




