## Managing Memory in Docker

I am running some face recognition code and the code kept terminating with SIGKILL or SIGNAL 9.  This kind of termination is most likely due to running out of  memory in the docker container.  So how to we increase memory on Docker running in Windows?

First, here is an image of my container running out of memory.

![Alt text](/images/image-24.png)

Note that the CPU usage at the top left is over 100%. That is because this metric is based on one CPU and I am running 8.  So 500%, say,utilization is not unusual. 

Memory is 16G which half of what I actually have on my 32G machine.  At some point the container needs more than 16G and this results in the following message.

```console
enroll() --> templ size: 2048 id: 1309 - OK
Subgraph backend MKLDNN is activated.
PID 32234 exited due to signal 9
[ERROR] Enrollment validation (single process) failed
```

Not nice at all.  So we need more memory than 16G and we have 32G of real memory available.
One way to change this is to create a `.wslconfig` in your user profile.  This is usually `c:/Users/<USERNAME>`.  You can also find this folder by typing `%USERPROFILE%` in file explorer. 

Here is my sample '.wslconfig`.

{% include codeHeader.html %}
```console
# Settings apply across all Linux distros running on WSL 2
[wsl2]

# Limits VM memory to use no more than 4 GB, this can be set as whole numbers using GB or MB
memory=20GB 

# Sets the VM to use two virtual processors
# processors=2

# Specify a custom Linux kernel to use with your installed distros. The default kernel used can be found at https://github.com/microsoft/WSL2-Linux-Kernel
# kernel=C:\\temp\\myCustomKernel

# Sets additional kernel parameters, in this case enabling older Linux base images such as Centos 6
# kernelCommandLine = vsyscall=emulate

# Sets amount of swap storage space to 8GB, default is 25% of available RAM
# swap=8GB

# Sets swapfile path location, default is %USERPROFILE%\AppData\Local\Temp\swap.vhdx
# swapfile=C:\\temp\\wsl-swap.vhdx

# Disable page reporting so WSL retains all allocated memory claimed from Windows and releases none back when free
# pageReporting=false

# Turn off default connection to bind WSL 2 localhost to Windows localhost
# localhostforwarding=true

# Disables nested virtualization
# nestedVirtualization=false

# Turns on output console showing contents of dmesg when opening a WSL 2 distro for debugging
# debugConsole=true
```
This just sets the container memory to 20Gb.

Once you have changed your file, you must restart `wsl` in windows to apply the changes.

{% include codeHeader.html %}
```console
wsl --shutdown
```
Then restart docker in windows, so docker can update its memory settings.
Let me know if you have any problems. 

Happy Dockering. 

Brian

![Lovell Portrait](/images/Lovell_portrait_small.jpg "Brian Lovell")
<!-- Put Javascript here! -->

<script src="/assets/scripts/copyCode.js" async> </script>