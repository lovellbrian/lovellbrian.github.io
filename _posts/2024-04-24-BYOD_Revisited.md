# BYOD Devcontainers and My Days and Nights from Hell

It was all going so well. I had my BYOD Devcontainers working perfectly. I could spin up a new environment in seconds and start coding. I was happy. The class seemed to be happy. Everything was good.

Then I got messages from students on the EDstem forum. They were having trouble with the Devcontainers. They had cloned the repository, opened it in VS Code, and clicked the "Reopen in Container" button. The devcontainer started to build, but then it failed. The error message was not helpful. I was flying to Adelaide at the time, so I couldn't really help them. I asked them to try again, but they still had problems.

## The Problem Uncovered

I knew I had not changed any code because the students were working on the assignment, so I knew the fault was external. Somehow the cpu branch was having trouble finding a compatible spacy version. Spacy is a natural language tokeniser. I had not changed the code, so the problem must have been with the dependency settings of some of the packages. It appeared that pip was now having difficulty finding a compatible version of spacy.  

When this happens in pip, it activates pip's dependency resolver. This is a new feature in pip 20.3. It is supposed to be better at finding compatible versions of packages &mdash; but it was failing.  What was happening was that pip decided to to build every version of spacy from source with version < 4.0 to find the most compatible candidate. 

So starting at version '3.7.4', each version was compiled until we reach 3.06 which does not compile. It seems everyone knows it doesn't compile and this version should have been **yanked** or withdrawn.  But it wasn't.  

```console
'3.7.4', '3.7.3', '3.7.2', '3.7.1', '3.7.0', '3.6.1,3.7.0.dev0', 
'3.6.0', '3.6.0.dev1', '3.6.0.dev0', '3.5.4', '3.5.3', '3.5.2',
'3.5.1', '3.5.0', '3.4.4', '3.4.3', '3.4.2', '3.4.0,3.4.1',
'3.3.3', '3.3.2', '3.3.1', '3.3.0', '3.3.0.dev0', '3.2.6', '3.2.5',
'3.2.4', '3.2.3', '3.2.2', '3.2.1', '3.2.0', '3.1.7', '3.1.6',
'3.1.5', '3.1.4', '3.1.3', '3.1.2','3.1.1', '3.1.0', '3.0.9',
'3.0.8', '3.0.7', '3.0.6'
 ```

Instead, 3.0.6 was breaking my container build during the pip backtracking process. Effectively, the build simply decided to take a new road to the same build destination and hit a spacy landmine along the way. I had to find a way to fix this. I could not have my students waiting for a solution. 

## Pinning the build

Fortunately I had frozen a previous build with a consistent set of packages on dockerhub.  So I ran `pip freeze > requirements.txt` in the cpufrozen branch and then committed this file to the cpu branch. This file would now specify the exact versions of every package. This is called **pinning the build.** 

This eventually worked, although I did have a couple of new errors to fix.  We required some extra libraries to be installed using the apt-get command. 

```bash
apt-get install -y libcairo2-dev pkg-config python3-dev
apt-get install -y libgirepository1.0-dev
```

Next I had to fix the gpu and gpufrozen branches while I was on my laptop in Adelaide. I now needed a gpu for testing, so I RDPed into my desktop machine.  The problem was that my desktop machine WSL was crashing whenever I tried to build the container for no sensible reason.  It used to work!  After a lot of trial and error I discovered that the problem was due to Docker Desktop settings.  I had to increase the memory and swap space to 8GB in a .wslconfig file in my windows %USER_PROFILE% directory.  This fixed the problem.  WSL was simply running out of memory.

The gpu solution was very similar to the above &mdash; pin the packages and add the two libraries. 

## Refactoring and speeding up the devcontainers

Finally I did some refactoring to improve the overall performance of the devcontainers. The machine image is created by the Dockerfile and can be loaded very quickly as docker layers. This is not a problem.  However, the deep learning packages represent a huge amount of python code and they are installed in the vscode user account in the ~/.local directory. 

Packages are installed in userspace so users do not need to become root to update packages.  This Userspace install also supports python virtual environments like conda and is current best practice. It is a really bad idea to install packages as root as this may affect the host system. Python is used for many system operations. In a proper Unix system noone will give a user root privileges.  That is why I want you to have root privileges in this course &mdash; so you can learn Linux system administration. 

In a production system, you should never, ever be root.  This is exactly why we use containers for development.  Containers are isolated from the host system and can be run as root without affecting the host system.  This is a good practice to follow.

Now the vscode user account is mounted as a volume which is persistent across reboots and rebuilding of containers. That is, files in this account are only deleted when you run the cpu branch which refreshes the vscode volume.  If you only run the cpufrozen branch, the volume is not deleted.  

This means that the container can be created quickly since we do not need to reinstall deep learning software once it has been created.  So we now detect if ~/.local exists and if it does not, we install the deep learning software.  This is done in install-dev-tools.sh. This script is run after the system container is created in docker via the Dockerfile.

With these changes it now takes less than 10 seconds to reboot the cpufrozen and gpufrozen containers.  The cpu and gpu containers take about 5 minutes to reboot and they always recreate the vscode volume from scratch.  This is a huge improvement over the time it used to take.

For future reference, all apt-get commands should be in the Dockerfile as they change the linux image. All pip commands should be in install-dev-tools.sh as they change the vscode image only.  The Dockerfile is run as root and install-dev-tools.sh is run as the vscode user.  

## Shebangs!

The **If then else statement** in install-dev-tools.sh kept failing even though I was invoking it with `bash install-dev-tools.sh.`  The problem was that I needed to add a shebang so the code would be interpreted by the kernel as bash &mdash; it turns out that opening the script with bash does not actually tell the kernel to use bash. It will still use the good old bourne shell **sh**.  

Here is the correct way to run a script in a different language.  The first line of the script is `#!/bin/bash` which tells the system to run and interpret this script in bash. The shebang is a special 2 character comment **#!** which tells the kernel that the file is a script and should be run by the interpreter specified after the shebang.  

Note that the shebang must always be the first two characters in the file as the kernel looks at the first two characters of every file to see if they form a shebang. If so, the kernel extracts the rest of the line to select the interpreter, and passes the remainder of the file to the specified interpreter.  Never put any spaces before the shebang!

You would think that `bash <shellscript>` would run the script in bash, but it doesn't. This is because unix also allows you to change scripts into executables by simply changing the permissions with `chmod +x <shellscript>`.   The shebang is the only way to specify the interpreter in this traditional, convenient and portable unix implementation of scripting.

If, say, you want to run a python script you would use `#!/usr/bin/python3`.  This is a good way to run scripts as it makes the script more portable.  You can run the script on any system that has the matching interpreter installed.  So this is why we use shebangs. I never knew this before and I hope you find this useful.


## Time spent on this

I worked through to 5am on the weekend and then 5am in an Adelaide hotel room to bring you these solutions. I do hope you enjoy them.
It is now 3am and I am going to bed and it is ANZAC Day tomorrow.   I am tired.  I hope you appreciate the effort I put in to make your life easier.  I do it because I care.  I hope you care too.  Goodnight.

Brian

![Lovell Portrait](/images/Lovell_portrait_small.jpg "Brian Lovell")

<!-- Put Javascript here! -->

<script src="/assets/scripts/copyCode.js" async> </script>

