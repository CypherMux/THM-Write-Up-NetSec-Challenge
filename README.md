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

TCP is the transport protocol that is used for the port numbers signified. TCP sets up a connection between two devices and constantly checks if data arrives in the right order and is required to do so, it is reliable but has more overhead and is much slower than UDP. UDP is its counterpart and sends data without setting up a connection or confirming delivery, it is much faster than TCP but terribly inefficient as packets could be lost or arrive out of order using this transport protocol. It's important to know the difference as a standard but it is not vital to complete this room I just thought I would explain what it is before we continue.

So with that being said, let's look back at the nmap scan we had conducted earlier in this room:

<img width="886" height="507" alt="Screenshot From 2026-10-19 09-41-40" src="https://github.com/user-attachments/assets/aae9c598-7ab3-4159-8adc-9269b02ecf5b" />

As you can see, the port numbers are listed as follows:

"PORT/TRANSPORT_PROTOCOL"

This is the standard for how nmap marks down each port number. We found seven ports and every single ones transport protocol is TCP, meaning the answer to the question stated would be 7.

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

```ftp://```:This tells hydra what service you are attempting to target, without this flag hydra would be lost and would refuse to brute force at all.

```MACHINE_IP```:This is just our target machine's IP again. This section tells Hydra what IP address to target when brute forcing our username file.

```:10121```:This part is essential as it is the port number we are attempting to brute force, since FTP usually runs on port 21 this is necessary to successfully brute force our usernames. Without it Hydra would attempt to use port 21 to brute force our machine and would hit a brick wall in a sense.

```-t 4```:This simply tells Hydra to run this many tasks in parallel with each other. Depending on how strong your system is you can ramp this up to over 16 tasks at once with ```-t 16``` although that would take a monster to run. I like to use 4 to be conservative and as it can run on most systems old and new.

Make sure to remember how to use hydra and what each flag in the hydra tool does as it is essential to a ton of CTFs and rooms present on this site and is useful in general to brute force weak passwords.Anyways now that we ran hydra and let the command run, you should see something like this in your terminal if you did it correctly:


<img width="953" height="448" alt="HYDRAOBFSC" src="https://github.com/user-attachments/assets/b65fe7f7-fba9-4b5d-b3dd-c14e78b728ab" />

As we can see from the image above, hydra has successfully brute forced the usernames from our user file using rockyou.txt and came up with two passwords which it found. Again I cannot show these passwords but we will use them for the next step in the process.

Accessing the FTP Server
------------------------

Now that we are past the hard part this part is extremely easy. All we need to do now is login to both eddie and quinn and see which of these accounts hold our secret flag file. To login to the FTP server, we can use the command:

```ftp MACHINE_IP 10121```

If you do not know what this command does or why it works the way it works, the string goes as follows:

```ftp```:This is simply telling the terminal to login to a FTP server

```MACHINE_IP```:This is the target machine's IP which hosts the FTP server.

```10121```:This is the port number in which we are connecting to on the target machine's servers in order to access the FTP server. Remember the FTP server on this machine is not on the standard port for FTP port 21 so this option is essential to accessing it. Without this option the server will refuse to connect because it cannot find the server.

Now that we have input the command and executed it the process should be pretty straightforward, let's input eddie to start and then input eddie's password. When you have successfully logged into their account your terminal should look like this:

<img width="906" height="386" alt="Screenshot From 2026-10-19 19-57-30" src="https://github.com/user-attachments/assets/b6e21f93-071d-49b5-90ff-316d86ca28b4" />

Now that we are in eddie's FTP server we can finally start searching for the flag file. But first make sure to turn on passive mode via the command ```passive``` in the FTP server. This will alleviate any problems with inputting commands in the server such as freeze ups because the server is most likely if not definitely behind a firewall as being in active mode while inputting commands behind a firewall will cause system choke holds. Passive mode will stop this in most scenarios. A link to an article explaining why this works and that explains it way better than I do is linked here:

https://winscp.net/eng/docs/ftp_modes

Anyways after going into passive mode we can finally start inputting some commands to find this file. Let us start by inputting the following command in the starting directory:

```ls -a```

I should not have to explain what this command does but if you do not know:

```ls```:Lists the current files present in the directory you are in.

```-a```:This flag lists hidden files in the directory you are currently in, you should always use this command on CTFs like this as the flag file could be anywhere it could even be hidden. It's not good to take chances on things like that for a flag so simple.

After you input the said command to list all files hidden and not, your terminal should look like this:

<img width="921" height="448" alt="Screenshot From 2026-10-19 20-11-15" src="https://github.com/user-attachments/assets/6206b72b-ba74-4322-8d9e-059218822f5e" />

As we can see, eddie does not have the flag file we are after, which means the next best alternative is quinn. Let's do the same process for quinn and see what our research shows. If you need to recall what commands to put in simply scroll back up and read the process we used for eddie as it is the exact same minus the username and password we used. After we input quinn's username and password and list the files hidden and not in their directory we can see the following:

<img width="931" height="775" alt="Screenshot From 2026-10-19 20-14-30" src="https://github.com/user-attachments/assets/4c9ab616-741c-4b7b-a2e3-22bb99e042d9" />

Quinn has our flag file in their directory!! All we need to do now is transfer the file from the FTP server onto our machine in order to read it. We do this instead of using ```cat```to read the file as ```cat```is not available on FTP systems. We can transport the file using the command:

```get ftp_flag.txt```

This command is pretty self explanatory but for old times sake this is what each piece of this command does:

```get```:This tells the FTP server you want to retrieve a file and transport it to your system.

```ftp_flag.txt```:This is the file we want transferred to our system. Which in this case would be our flag file!!

Now that the flag file is transported onto your system or the AttackBox you are attacking this machine from we need to see where the file ended up. Majority of the time if not all the time, I haven't seen it a time it hasn't been here so don't quote me on that lol, it will be in your home directory. Simply exit the FTP server using "ctrl+d" and then reset your terminal with the ```reset``` command so you have a fresh terminal to execute commands from, not necessary but to get rid of all the stuff we just done and to declutter I personally enjoy doing this just so I have a clearer terminal space. Now since we resetted our terminal it should automatically be in the home directory in which our flag file should reside. We can check this by inputting the command:

```ls | grep ftp_flag.txt```

Again this command is structured in a certain way so only our flag file is allowed to show up when we input the ``ls``command. Each part goes as follows:

``ls``:Again this just lists the current files in the directory you are currently in.

```|```: This is called a pipe and is extremely useful if you ever want to learn Linux more in depth, it essentially "pipes" or sends the output of one command to another, so in our scenario the pipe sends the output of ```ls``` to the ```grep``` command.

```grep```:grep is another insanely useful command in the Linux terminal as it searches for whatever you put after it in the file, directory, or wherever you execute it. It is extremely useful when attempting to find a single piece of information in a text file for instance when the said text file is thousands of lines long, it simply makes the searching process faster as an user.

```ftp_flag.txt```:This should be pretty self explanatory, we are essentially just telling the ```grep```command to search for this specific wordage in the directory we are currently in.

After we input this command and check the output, if you did the prior steps correct the terminal should look like as follows:

<img width="931" height="296" alt="Screenshot From 2026-10-19 20-33-27" src="https://github.com/user-attachments/assets/d7b900a7-6e2b-4e25-aeb9-27e234510655" />

Perfect the file is now in our home directory! Now all we need to do is ```cat```the file and read its contents. After we execute the cat command on the flag file the flag for this command will be in our terminal! It should look as follows:

<img width="931" height="425" alt="quinnobfsc" src="https://github.com/user-attachments/assets/a7f47fc0-0fbb-44e9-be5a-d1b11701982a" />

Now onto the next question! (That was a long one I know lol.)

Stealth nmap Scanning
---------------------

This next question is much MUCH simpler and just warrants us to complete a quick nmap challenge in order to acquire the flag via our browser. The question goes as follows:

"Browsing to http://MACHINE_IP:8081 displays a small challenge that will give you a flag once you solve it. What is the flag?"

Following the questions guide and navigating to the website in the question, we will be presented with the following:

<img width="948" height="1156" alt="nmapscanssobfscucate" src="https://github.com/user-attachments/assets/9e7e2ef0-fb78-4d09-9f47-53f6cd27db3a" />

For this challenge we will have to use nmap to scan the target machine and do it as covertly as possible in order to not be detected by the system's internal IDS(Intrustion Detection Software). To do this we will have to use the stealthiest nmap scanning options in our terminal in order to pass the IDS present on the system. This is an extremely easy process and I will explain what each part does in depth so you can fully grasp what the command does. It goes as follows:

```sudo nmap -sN MACHINE_IP```

Again as stated prior, this is what each part of the command does:

```sudo```:This tells the terminal to execute this command via the root user. This is essential because we are using the flag ```-sN```which can only be executed as the root user.

```nmap```:This tells the terminal to use the tool nmap.

```-sN```:This flag tells nmap to perform a scan called a "TCP Null Scan". What it essentially does is sends TCP packets with no flags set and then uses the response from the ports to infer the port's status. For example when we input this flag into our command all the ports will be shown as "open|filtered", which means that nmap cannot tell that the port is open or filtered, although we already know these ports are open. The reason we are using it is to get past the IDS. A link to a general guide to nmap port scanning can be found here if you want to do further research on the subject which I heavily recommend as nmap is an extremely useful tool for port enumeration a skill you will need to know for these kinds of rooms and in general for cybersecurity roles:

https://nmap.org/book/port-scanning-tutorial.html


```MACHINE_IP```:Again this is just the target machine's IP, pretty self explanatory.

Make sure to reset the packet count on the site and then execute the command, you should see the browser adjust the chance of being scanned to an extremely low level and after that the site will automatically give you the flag! In case you want to see a visual of what it looks like when you have successfully got around the IDS this is what it should look like in your browser:

<img width="948" height="1156" alt="obfwebbrowser" src="https://github.com/user-attachments/assets/f0366349-af5e-4892-ae78-94199e150081" />

What we learnt
-------------

The importance of doing a full port scan: Scanning all ports via adding the ```-p-``` flag in our nmap command was essential in discovering the rooms hidden services on non-standard ports, such as the hidden FTP server we found.

Protocol Fundamentals: We learnt the importance of understanding the difference between UDP and TCP and why using TCP for important services where reliability matters more than speed is so essential.

Nuances in the FTP protocol: We learned why passive mode is necessary when interacting with the majority of FTP servers modern day. Active connection causes freeze ups due to the firewall guarding the server while passive mode enables us to transfer our commands successfully and extract the data we need.

IDS Evasion: We learnt how to successfully evade an IDS that is tracking our network scans by using the TCP Null scan option in nmap. Sending packets with no flags set can trick the IDS into ignoring our scan outright.

Remediation
-----------

It is great knowing how to attack a system but is even better if you understand how to defend it as well. The ways to defend the server in this room include the following:

Securing the FTP server: The FTP server in this room was running on a non-standard port and was extremely vulnerable to a brute force attack using a common passwords list like in this case "rockyou.txt". The protocol should be migrated over to SFTP and much stronger password policies should be put in place, or even better, key-based authentication via something like a Yubi key for instance.

Harden the IDS/Firewall: The IDS in this system was easily bypassed by using a TCP Null scan. To remediate this, the firewall should be reconfigured to drop malformed TCP packets before they reach the IDS. The IDS should also be set to be alerted for stealth scanning techniques commonly used, such as the TCP Null scan we used.

Use the principle of least privilege: The FTP server in this room allowed us to download the flag file and in fact any file we wanted. Access controls for such an action should be reviewed to be certain that no anonymous or low level users are able to do such a thing to sensitive files.

Thank You
---------

We have officially acquired the last questions flag and we are done with this room! Thank you so much for sitting through this guide and following it step by step as even through a guide learning step by step is way better than looking the answers up. Any advice on what I can do different or how I can improve my write-ups is gladly accepted as I want to create the best write-ups I can so everybody can learn this material regardless of prior experience. Again thank you for sitting through this guide and have a great day!


<img width="554" height="450" alt="arnold" src="https://github.com/user-attachments/assets/717aa7ba-963a-4522-866e-fc3ae0d6c2b4" />


















































