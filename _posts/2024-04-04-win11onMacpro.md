# Joys of upgrading to Windows 11 on 2020 MacPro

I have tried several approaches to upgrade my 2020 MacPro 13" to Windows 11 and now it is finally happening. I am so excited to share my experience with you.

I followed this [guide](https://www.youtube.com/watch?v=zzoNO5YSCIA) to install Windows 11 on my MacPro. This allowed installation of windows 11 direct from the ISO.  The problem I struck was that the installer would not install to the windows partition as it was formated as APFS. `Diskpart` refused to format the partition as NTFS unless I did a clean operation which would delete all partitions and wipe out the Mac partition.

Next try was to install Windows 10 first and then upgrade according to this [guide](https://www.youtube.com/watch?v=Lj0iwLyVpzI). This worked most of the way, but the windows 11 upgrade failed with the error message "This PC does not meet the minimum requirements for Windows 11".  I tried to bypass this by editing the registry as suggested in this [guide](https://www.youtube.com/watch?v=3JZ2v9Q2J1A), but it did not work for me.

At the time, I was trying to install `Win11_23H2_EnglishInternational_x64v2.iso`and it generated the compatibility error. However when I switched to an earlier version `Win11_English_x64v1-001.iso ` there was no error and it finally worked. The later ISO must have had more severe hardware checks.

Hopefully, I can now upgrade to the latest version of Windows 11.

I have a new desktop coming which will also be Windows 11, so all my machines will finally be up to date. 

I hope this helps you if you are having similar problems.


Brian

![Lovell Portrait](/images/Lovell_portrait_small.jpg "Brian Lovell")

<!-- Put Javascript here! -->

<script src="/assets/scripts/copyCode.js" async> </script>