# How to Run Deep Learning on a Windows PC with Devcontainers

I have been working to make Deep Learning Accessible on Windows Machines by providing a Linux Docker Image on top of WSL Linux and connecting to the GPU.

I am currently seeting up a Lab Machine with an RTX2080 which is a pretty good GPU.

So here are the steps to getting this up and running.

First we need to make some changes to our Windows host.  There are only three software tools we need to install.

1. Windows Subsystem for Linux with a Ubuntu image.
2. Docker Desktop for Windows
3. Nvidia Container Toolkit installed in the Ubuntu WSL image, not windows. 

## So Let's Get Started on WSL

First check that Windows Subsystem for Linux is ticked in Windows Features as per below.

![Alt text](image.png)

Now open a command window (cmd) and install wsl using the Ubuntu distribution with these instructions.
https://learn.microsoft.com/en-us/windows/wsl/install

 ```console
 wsl --install --distribution Ubuntu-22.04
 ```
 This will take a few minutes and you see the following when done.

 ![Alt text](image-1.png)

The console will open a Linux window as per below.

![Alt text](image-2.png)

Select a Linux username and password.  I suggest you use your UQ credentials. 
 
![Alt text](image-3.png)

You now have a Linux machine that you can access just like an app on your windows machine.  Just type wsl to enter linux and exit to go back to windows. Alternatively, Access Ubuntu directly from the recently added applications. It is best to pin Ubuntu to the Taskbar. 

![Alt text](image-4.png)

# Now we Install Docker Desktop for Windows

Simply go to

https://docs.docker.com/desktop/install/windows-install/

and install the software.
