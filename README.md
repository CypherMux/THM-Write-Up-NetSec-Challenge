# THM-Write-Up-NetSec-Challenge
****************************************************************************
Hello everybody, this will be a walkthrough of the room "Net Sec Challenge" on TryHackMe, this room requires the tools ```nmap```, ```telnet```, and ```hydra``` in order to complete it so make sure to download them with these CLI commands or use the built-in AttackBox which TryHackMe provides themselves:

Arch-Based
----

```sudo pacman -Syu nmap hydra inetutils```

Ubuntu/Debian/Kali
------------------

```sudo apt update``` and then ```sudo apt install nmap hydra telnet```

Fedora-Based
------------

```sudo dnf install nmap hydra telnet```

MacOS
-----

```brew install nmap hydra telnet```

Windows
-------

For Windows based systems, I recommend downloading WSL(Windows Subsystem for Linux) in order to follow this walkthrough as installing these tools on a Windows system is more of a hassle than need be as you need to compile and build the tools yourself. Make sure to jot down the WSL distribution you have, whether it be Ubuntu, Fedora, Arch, etc and follow along using the commands listed above.
******************************************************************************************

With that being said and the tools now being installed on your system of choice, let us begin the room!

Port Enumeration
----------------

For the first questions of this room we will have to enumerate the target system in order to obtain info on what ports are open to our attacking machine. The first question we are asked is:

"What is the highest port number that is open and less than 10,000?"

To answer this question we will have to enumerate the target machines ports via nmap to see which ports are open and which are not, we can do this by using the command:

```nmap -p- MACHINE_IP```

Lets breakdown exactly what this command does and why it is useful to us:

```nmap``` : This tells the terminal to utilize the tool nmap


```-p-```  : This will tell nmap to scan ALL ports on the machine, it will then tell us whether those ports are open or not after it is done scanning.


```MACHINE_IP```: This will simply be where you input the target machine's IP address which should be listed in the top right of your browser window on TryHackMe.

Now that we have let nmap run and it has finished scanning, you should see something like this on your screen:

<img width="886" height="507" alt="Screenshot From 2026-10-19 09-41-40" src="https://github.com/user-attachments/assets/2078d4ad-d113-42a0-ad8c-11004973b4b2" />







