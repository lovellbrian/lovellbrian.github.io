## Fixing Broken installs

When I issued the commend 
```console
sudo apt upgrade
```
I got the dreaded unmet dependencies error. I tried to fix it by running 
```console
sudo apt-get install --fix-broken
```
but just wound up in a loop. I [finally](https://www.baeldung.com/linux/unmet-dependencies-apt-get) fixed it by running 
```console
sudo apt-get clean 
sudo apt-get autoclean
```
to fix the corrupted system database. 

I think the problem was due to the system being unable to find the nvidia sources and so it couldn't find the dependencies. 

![Lovell Portrait](/images/Lovell_portrait_small.jpg "Brian Lovell")

<!-- Put Javascript here! -->

<script src="/assets/scripts/copyCode.js" async> </script>