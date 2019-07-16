---
title: Setup
---

## Install Docker on local machines

> ## About Docker and Introductions
> For the majority of this workshop we will be using Docker and working in an ```AnalysisBase``` release container so it is required that you download Docker and make an account. There are a lot of pros and cons to this, which we won’t discuss here.
>
> General information about Docker and details of installation can be found here: ([About/Install](https://docs.docker.com/install/))
>
> Depending on the operating system you are using on the local machine, links to specific instructions on the Docker website can be found below.
>
{: .prereq}

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
        </p>
      </article>
      <article role="tabpanel" class="tab-pane" id="docker-linux">
        <p>
          <br/>
          Downloading and installing Docker for Linux may be slightly more difficult but please contact the organisers or tutors as soon as possible so they can help with any problems.<br>
          <br/>
          Here are the instructions for two popular Linux distributions:
          <li><a href="https://docs.docker.com/install/linux/docker-ce/CentOS/">CentOS</a></li>
          <li><a href="https://docs.docker.com/install/linux/docker-ce/ubuntu/">Ubuntu</a></li>
          <br/>
          Instructions for other Linux distributions can be found on the Docker docs pages.
          <br/>
        </p>
      </article>
    </div>
  </div>
</div> {% comment %} End of 'shell' section. {% endcomment %}

> ## Important Note
> Be sure to read the Docker documentation on post-installation steps for Linux and managing Docker as a <a href="https://docs.docker.com/install/linux/linux-postinstall/">non-root user</a>. 
>
> This will allow you to edit files when you have started up the Docker container from your image. 
>
{: .callout}


## Download an ATLAS AnalysisBase Release

You normally start out by downloading the Docker image for the release you want (not strictly necessary, but good practice). The Docker images get managed by Docker behind the scenes (details in the Docker session), so you don’t have to worry about that yourself:

```bash
docker pull atlas/analysisbase:21.2.75
```

The image should be around 4GB in size, so make sure you have the space available.

> ## Latest Release...
> These instructions will use a numbered release, which should be your default to work with. 
>
> If you want to use the absolutely latest, bleeding edge version you can replace <br> ```atlas/analysisbase:21.2.75``` with ```atlas/analysisbase``` or ```atlas/analysisbase/latest```.
>
{: .callout}

## Download the Analysis ROOT File

As previously mentioned the aim of this workshop is to give a background on the computing essentials you may need for your future analyses. However, it would be beneficial to learn these concepts and techniques in the context of a physics analysis. 

We will be working with events from a DAOD ROOT file and the instructions to download this file can be found <strong><a href="https://cernbox.cern.ch/index.php/s/YXbCrQkwnZuc3yU">here</a></strong>.

Please place that somewhere within your working directory.

> ## Setup Checklist
> - Download and installed Docker
> - Downloaded the ATLAS AnalysisBase image
> - Downloaded the ROOT file we will be working with
> 
> If this is all setup, you are free to continue
{: .checklist}


{% include links.md %}
