# How to Run Deep Learning on a PC Running Windows, MacOS, or Linux with Devcontainers

I have been working to make Deep Learning Accessible on Windows Machines by providing a Linux Docker Image on top of WSL Linux and connecting to the GPU.

I am currently setting up a UQ Lab Machine with an RTX2080 which is a pretty good GPU although the memory is a bit small. 

So here are the steps to getting this system up and running.

First we need to make some changes to our Windows host.  There are only three software tools we need to install.

1. Windows Subsystem for Linux with a Ubuntu image.
2. Docker Desktop for Windows
3. Nvidia Container Toolkit installed in the Ubuntu WSL image, not Windows. \(only required if you have a GPU\)

Note that if you are running on Linux or a Mac, you should already have the Linux Kernel installed, so you may simply need to install docker.  If required, also install the Nvidia Container Toolkit to allow GPU access from the container.

## So Let's Get Started on WSL

First check that Windows Subsystem for Linux is ticked in Windows Features as per below.

![Alt text](/images/image.png)

Now open a command window (cmd) in administrator mode and install wsl.  Then install the Ubuntu-22.04 distribution with the following instructions based on these [Microsoft Instructions](https://learn.microsoft.com/en-us/windows/wsl/install).

You must use WSL version 2.  The following instuction will ensure you are using WSL Version 2 for all new projects.

{% include codeHeader.html %}
```console
wsl –set-default-version 2 
```
Now as a first step, let`s update WSL to the latest version. 

{% include codeHeader.html %}
```console
 wsl --update
```
 Install WSL and Ubuntu.

{% include codeHeader.html %}
```console
 wsl --install
```
If this command fails, you may already have Ubuntu installed. If so, skip to the next command. If it asks for a Linux username and password, I suggest you use your UQ credentials. 

Now install Ubuntu-22.04.

{% include codeHeader.html %}
```console
 wsl --install --distribution Ubuntu-22.04
```

This will take a few minutes and you see the following when done.

![Alt text](/images/image-1.png)

The console will open a Linux window as per below.

![Alt text](/images/image-2.png)

Select a Linux username and password.  I suggest you use your UQ credentials. 
 
![Alt text](/images/image-3.png)

You now have a Linux machine that you can access just like an app on your windows machine.  Just type `wsl` to enter linux and `exit` to go back to Windows. Alternatively, access Ubuntu directly from the recently added applications. It is best to pin Ubuntu to the Taskbar for easy access. 

![Alt text](/images/image-4.png)

If all is well type 

{% include codeHeader.html %}
```console
 wsl -l -v
```

and you should see the following.  The * indicates the default distribution is Ubuntu-22.04 and it is running WSL version 2.   If the default distribution is not Ubuntu-22.04, then use this command in windows console to set the default distribution. 

{% include codeHeader.html %}
```console
wsl --setdefault Ubuntu-22.04
```
If a your Ubuntu distribution is on WSL 1 instead of 2, you can convert it as follows:

{% include codeHeader.html %}
```console
wsl --set-version Ubuntu-22.04 2 
```

![Alt text](/images/image-9.png)

# Now we Install Docker Desktop for Windows

Simply follow the

[Docker Installation Instructions](https://docs.docker.com/desktop/install/windows-install/)

and install the software. Do not attempt to install from the Microsoft store. Docker will  ask you to restart your PC to complete installation. 

![Alt text](/images/image-5.png)

Next accept the Docker subscription agreement and Docker Desktop will open and ask you to setup or sign into your account.

![Alt text](/images/image-6.png)

Once you have done that, you are in.

![Alt text](/images/image-8.png)

Now you will need to configure Docker desktop. Go to Settings and select `Resources/WSL integration.` Make sure the sliders are set as follows to allow Docker to integrate to both the Ubuntu images. Check these sliders occasionally as they sometimes get reset. 

![Alt text](/images/image-10.png)

Next we need to upgrade the shared memory allocation. Select `Docker Engine` and edit the json conguration file as follows.

{% include codeHeader.html %}
```json
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "default-shm-size": "2g",
  "experimental": false
}
```

![Alt text](/images/image-11.png)

Then click `Apply and Restart.`


# Install Nvidia Container Toolkit in Ubuntu 22.04 WSL

Finally, if we have a GPU we need to install Nvidia Container toolkit in Ubuntu.  This allows our containers to access the GPU hardware.

First open Ubuntu from the windows command prompt by typing

{% include codeHeader.html %}
```console
 wsl
```

You can check that you have opened Ubuntu-22.04 by using the lsb_release command.

{% include codeHeader.html %}
```console
lsb_release -a
```

![Alt text](/images/image-12.png)

Now copy the commands to install Nvidia Container Toolkit from the [Nvidia Installation Instructions](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html).



{% include codeHeader.html %}
```console
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
&& curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
  sed `s#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g`| \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list \
&& \
  sudo apt-get update

```

{% include codeHeader.html %}
```console
sudo apt-get install -y nvidia-container-toolkit
```

{% include codeHeader.html %}
```console
sudo nvidia-ctk runtime configure --runtime=docker
```

# Open fastai Deep Learning Software from Github in Container

Now open the windows console and clone the fastai repository to C drive, or another local disk.  Do not use Google Drive or H Drive (mounted) or you will have container mount permission difficulties later. This repository contains [Jeremy Howard's](https://en.wikipedia.org/wiki/Jeremy_Howard_(entrepreneur)) fantastic fastai course delivered at UQ in 2022.

{% include codeHeader.html %}
```console
c:
git clone https://github.com/lovellbrian/course22
```
Make sure you have Visual Studio Code (or pycharm) installed. 

If not fetch from Visual Studio Code from [here](https://code.visualstudio.com/download).

Open VS Code 

![Alt text](/images/image-13.png)

Then open your cloned folder C:course22 in VS Code using `Open Folder.`

VS Code may ask a few questions such as asking to install the devcontainers extension. Accept the suggestions. Eventually, it will ask you for permission to `Reopen in a Container.`  This will now create a new container to run your code.  Please click on `Show Log` to see the software being installed live. 

![Alt text](/images/image-16.png)

Enjoy the scrolling text or go make yourself a coffee.  This will take 15 or more minutes on the first run.  The next run will be a few seconds. 

Now open the Notebook `00-is-it-a-bird-creating-a-model-from-your-own-data.ipynb.`
Click on `Run All` at the top of the screen.  It will then ask you to choose a kernel source.  Select Python Environments and the recommended version of Python.  Now the notebook should be running. 

First, the notebook will fetch one bird image and rhen one forest image from the internet.  Next it will download 200 birds and 200 non-birds to build a training set which should take about 7 minutes. After some clean up steps, the notebook will run deep-learning code to train a RESNET-18 classifier network.  All learning is perfomed in vision learner. Note the graphics which shows you the learning progress. We are running 3 epochs and 6 batches per epoch. You will likely see that the error rates are very low approaching 0.  

Training will take several minutes using the CPU only which is the default (less than 1 minute on our lab machine GPUs).  Finally, the notebook will check the original bird image to see if it is a bird.  Pretty cool, huh.

Next we will run the same example using the GPU instead of the CPU. Now to perform this step you will need access to a PC with GPU installed. An easy way to check for this is to run the command.

{% include codeHeader.html %}
```console
nvidia-smi
```
This will give you an output like this on our lab machines.  This shows that we have one NVIDIA GeForce RTX 2080 GPU Card with 8Gb of Memory in slot 0.

![Alt text](/images/image-18.png)

All we need to do is to change the files in .devcontainer so they are the same as .devcontainerGPU.   There are a number of ways to do this, but a convenient way is to simply swap to the gpu branch of the repository. 

At the bottom left of the screen, you will see the word `master.`  Click on this and select the gpu branch of the repository.  The master and the cpu branch should be identical, but the content of .devcontainer is different for the gpu branch. Now select View/Command Palette and select Dev Containers: `Rebuild and Reopen in Container.`  This will load the GPU Container which will take a few minutes once again.  Time for your next coffee -  I`m grabbing one now. 

![Alt text](/images/image-17.png)

When we run with the gpu image the code is much faster as the GPU does most of the work. You can use the following command to monitor the GPU.

{% include codeHeader.html %}
```console
nvtop
```
Notice how the GPU is working when the training code starts.

![Alt text](/images/image-19.png)

Why is the GPU only showing about 50% load? This means it does not have enough work to do.  So how do we give it more work? Perhaps we need to increase the batch size. 

Try increasing the batch size to speed up your learning (not telling how, but you need to insert bs=128 somewhere). The default batch size is 64.  Make sure you have upgraded your shm memory in Docker to avoid crashing. Try batch sizes of, say, 16, 32, 64, 128, and 256. Here is 256.  Which gives the fastest learning. Please try to explain what is going on.

![Alt text](/images/image-20.png)

Enjoy!

Happy coding on your personal Linux GPU Container. 

Brian
![Lovell Portrait](/images/Lovell_portrait_small.jpg "Brian Lovell")


  <script src="/assets/scripts/copyCode.js"> </script>