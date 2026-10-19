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

For Windows based systems, I recommend downloading WSL(Windows Subsystem for Linux) in order to follow this walkthrough as installing these tools on a Windows system is more of a hassle than need be as you need to compile and build the tools yourself. Make sure to jot down the WSL distribution you have, whether it be Ubuntu, Fedora, Arch, etc and follow along using the commands listed above. A guide to installing WSL is listed here on the official Microsoft website: 

https://learn.microsoft.com/en-us/windows/wsl/install
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


```-p-```  : This will tell nmap to scan ALL ports on the machine, it will then tell us whether those ports are open or not after it is done scanning. This is vital as some machines may have open ports that are beyond the standard port numbers(0-1023). The ports beyond those numbers are called "registered ports" while the ones from 0-1023 are considered "well-known ports".


```MACHINE_IP```: This will simply be where you input the target machine's IP address which should be listed in the top right of your browser window on TryHackMe.

Now that we have let nmap run and it has finished scanning, you should see something like this on your screen:

<img width="886" height="507" alt="Screenshot From 2026-10-19 09-41-40" src="https://github.com/user-attachments/assets/2078d4ad-d113-42a0-ad8c-11004973b4b2" />

As we can see from the terminal, the current open ports on this target machine are 22,80,139,445,8081,10001, and 10121. Now knowing this information we can infer that the highest port number that is open but below 10,000 would be 8081, meaning the answer to our first question would be:

Port 8081

Now the second question is extremely straightforward as well as it is:

"There is an open port outside the common 1000 ports; it is above 10,100. What is it?"

To solve this we can simply look back at our nmap scan from the last question. As we know the only open ports outside the common 1000 are 8081,10001, and 10121. As we can see we only have one open port which is above port 1000 and also above port 10,100 which would be port 10,121. So our answer to the second question as listed would then be:

Port 10121

Now for our third question we will have to look a bit more carefully at our nmap scan in order to concur what the answer could be. The question is as listed:

"How many TCP ports are open?"

TCP is the transport protocol that is used for the port numbers signified. TCP sets up a connection between two devices and constantly checks if data arrives in the right order, it is reliable but has more overhead and is much slower than UDP. UDP is its counterpart and sends data without setting up a connection or confirming delivery, it is much faster than TCP but terribly inefficient as packets could be lost or arrive out of order using this transport protocol. It's important to know the difference as a standard but it is not vital to complete this room I just thought I would explain what it is before we continue.

So with that being said, let's look back at the nmap scan we had conducted earlier in this room:

<img width="886" height="507" alt="Screenshot From 2026-10-19 09-41-40" src="https://github.com/user-attachments/assets/aae9c598-7ab3-4159-8adc-9269b02ecf5b" />

As you can see, the port numbers are listed as follows:

"PORT/TRANSPORT_PROTOCOL"

This is the standard for how nmap marks down each port number. We found seven ports and every single one's transport protocol is TCP, meaning the answer to the question stated would be 7.

Port Service Version Enumeration
---------------------------------

Now this part is going to be a bit more complicated as we will need multiple commands to conduct the research in order to come to the answer but the same methodology follows, we will enumerate the port's pertaining to the service versions we are trying to find. Our first question is:

"On port 80,what is the service version value?"

This one is the simplest of the bunch in my opinion but you will still have to know how to use the command we need and what it does. Port 80 is notorious for hosting HTTP websites over it, so to find the service version value it is as easy as using ```curl``` in order to get the index of the page in our terminal. The command goes as follows:

```curl MACHINE_IP -I```

Let me explain what each part of this command does so we can all understand piece by piece why this works to enumerate the port 80 service version:

```curl```:This command will tell the terminal to use the tool curl.

```MACHINE_IP```:This is simply your target machine IP again as stated in the last command, this tells curl to scrape the webpage for this particular IP if it has one.

```-I```: This command tells curl to index the page with details such as the content type, the last modified date, content length, date,etc,etc. The most important piece of info for us will be labeled "Server" at the bottom of the index.

After inputting the above command, your terminal should look something like this:

<img width="701" height="431" alt="Screenshot From 2026-10-19 12-48-53-obfuscated" src="https://github.com/user-attachments/assets/3abc3f60-ca68-4545-940a-a86386c914b9" />

Due to the nature of TryHackMe's policy on giving away flags or answers I am unable to show the flag so I have to black it out but as we can see we have now have the full index of the page in our terminal. At the bottom of the index we have a section labeled "Server" as mentioned earlier. The format for the answer although I am not able to formally give it away goes as follows:

"THM{********************}"

Now onto the next question. This question is more of the same although this time we will have to enumerate the SSH server header. I had a bit of trouble with this one myself as I was unable to curl the said port as it wasn't a webpage so I had to resort to doing a bit of research. I came to the conclusion that the only way to successfully enumerate this port and get its service version was to use nmap's built in options for scanning. This command is a bit longer than the last ones so be ready. It goes as follows:

```nmap -sV -p 22 MACHINE_IP```

Now a breakdown of this command as it was quite a long one and I want you to fully grasp what you just typed in your terminal and not just mash your keyboard and input random commands you don't. No script kiddies around here on my watch lol. Anyways, it goes as follows:

```nmap```: Again this just tells the terminal to use the tool nmap.

```-sV```: This tells nmap to run with service versions enabled, meaning you will be able to see the service versions of every port applicable to the toggle.

```-p 22```:This command simply tells nmap that it should only run this command against port 22. This is useful to us as every other port does not pertain to the question at hand and it would waste our time to scan every other port's service version as well. This is also applicable in a professional setting make sure to always just scan the port's you are attempting to enumerate if you already know which ones are open not doing so will waste time on your end.

```MACHINE IP```:Again this is just the target machine's IP on your side, it will vary from room to room so make sure to put in the correct IP address.

After inputting this command into your terminal you should get this output on your end as shown in the image:

<img width="955" height="603" alt="SSHSRVVSNOBFUSCATION" src="https://github.com/user-attachments/assets/a65bde5b-7617-47c4-8319-0199a9809d8c" />

As we can see, the info under the "gi?new-service :" section of our output is our service version. A bit down the output two lines under the listed "gi?new-service :" line is our flag for this question. Again I am unable to give the flag away due to TryHackMe's policy but the format goes as follows:

"THM{**********************}"

Now onto the next question. The next question goes as follows:

"We have and FTP server listening on a nonstandard port. What is the version of the FTP server?"

This one is basically more of the same when correlating it against the last question but instead this time we are trying to enumerate a FTP server on a nonstandard port. Now we know from prior that nonstandard ports are any port 1024 or above and we have 3 ports which fit that bill correctly:

PORT 8081

PORT 10001

PORT 10121

Knowing this information from our first nmap scan the only thing we have do now is scan each of these ports for their service version and the one with the FTP server will be shown to us by said service version listing. The command to do this goes as follows:

```nmap -sV -p 8081,10001,10121 MACHINE_IP```

This command is more of the same from the last question so I shouldn't have to explain it to you again. The only thing we do different here is scanning multiple port numbers in conjunction with the ```-sV``` flag. After we input this command, the following output should be in your terminal:

<img width="953" height="573" alt="obfuscatedtelnet" src="https://github.com/user-attachments/assets/e22560df-be59-462e-8f88-53cb0af4ad36" />

As seen above, our nmap scan officially gave us the service names and versions for each of the ports we have scanned.Port 8081 is a HTTP service so that is definitely not it and port 10001 is a scp-config? service so that is not it as well which leaves us the last port which is a FTP service and it lists its version as well. Again due to the policy I am unable to show it but we have found the telnet service version now and we can officially move on to the next section!

Hydra Password Cracking
------------------------

This part will require us to use usernames we are given in order to login to a FTP server which hosts the flag file we must find. The question goes as follows:

"We learned two usernames using social engineering:eddie and quinn. What is the flag hidden in one of these two account files and accessible via FTP?"

This section will be much more complex than the last two but never the less let us begin. In order to gain access to either FTP account owned by either eddie or quinn we will have to brute force these usernames in order to gain the passwords pertaining to them. If they have weak passwords this will be a piece of cake. First course of action is to create a usr.txt file for Hydra to read the usernames off of. This is not necessary but saves a lot of time as the usernames are brute forced together instead of separately. We can do this by using nano to create a text file in the any directory we desire, for instance I will use my home directory which is labeled ~ on most if not all Linux systems. We can do this with the command:

```nano usr.txt```

Again if you do not understand what this command does it goes as follows:

```nano```:This tells the terminal to run the tool nano, which is a command line based text editor.

```usr.txt```:This is just the text file we are creating to use in conjunction with nano, a new file will be created in your files called this when you officially save the file using nano.

After we input this command your terminal should look as follows:

<img width="953" height="573" alt="Screenshot From 2026-10-19 13-41-35" src="https://github.com/user-attachments/assets/43234cf6-9e7c-48d6-97f5-d6b298aa12be" />

All we need to do now is write the usernames in which we will brute force with Hydra into this text file. After you are done writing them down it should look something similar to this:

<img width="953" height="573" alt="Screenshot From 2026-10-19 13-43-31" src="https://github.com/user-attachments/assets/9e72cb92-4045-460d-879f-119dce1b989e" />

Now to save it I personally like using "ctrl+x" and then "y" and then "enter" but the official way is "ctrl+o" and then "ctrl+x". Either way works though. You can always check if you did it correctly after by either checking your gui file manager on your system for the text file or using the command ```ls | grep usr.txt``` while in the directory in which you saved the file. It is always good to double check for the file after saving just to alleviate the confusion if you did accidentally not save it.

Anyways besides that after we officially saved the file and we know it resides in our directory we can continue with the Hydra command. The command goes as follows:

```hydra -L usr.txt -P WORDLIST ftp://MACHINE_IP:10121 -t 4```

Now this command is MUCH longer than the prior ones so an explanation is absolutely mandatory so you know exactly what you just put in your terminal. If you already know what the command does then you can skip this section:

```hydra```:This tells your terminal to run the tool hydra, which is a password brute forcing utility.

```-L```:This tells hydra to read the usernames (or logins) from a file in which you created instead of a singular login. The way to do singular logins would be the command ```-l```.

```usr.txt```:This is just telling hydra to use our "usr.txt" file to read from when brute forcing usernames, enough said pretty simple.

```-P```:This tells hydra what wordlist it should read from when brute forcing the usernames you give it. It will use this wordlist as a list of passwords to test against the usernames.

```WORDLIST```:This is simply where you would put the file which has all the passwords that you want to run against the username file you gave earlier. The one needed for this room is "rockyou.txt" and is available online for download here:

https://github.com/RykerWilder/rockyou.txt

You can also download the SecLists Github repo which comes with this wordlist and many more for other uses, you can download SecLists from the link here:

https://github.com/danielmiessler/SecLists

```ftp://```:This tells hydra what service you are attempting to target, without this flag of sorts hydra would be lost and would refuse to brute force at all.

```MACHINE_IP```:This is just our target machine's IP again. This section tells Hydra what IP address to target when brute forcing our username file.

```:10121```:This part is essential as it is the port number we are attempting to brute force, since FTP usually runs on port 21 this is necessary to successfully brute force our usernames. Without it Hydra would attempt to use port 21 to brute force our machine and would hit a brick wall in a sense.

```-t 4```:This simply tells Hydra to run this many tasks in parallel with each other. Depending on how strong your system is you can ramp this up to over 16 tasks at once with ```-t 16``` although that would take a monster to run. I like to use 4 to be conservative and as it can run on most systems old and new.





























