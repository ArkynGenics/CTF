# 1. Greeting
#### Author : Arkoov (https://github.com/ArkynGenics)
### Steps:

1. Open the ip provided `http://ctf.tcp1p.com:58161/`
2. Inside the page elements of the web resides the commented flag

![image](https://user-images.githubusercontent.com/86551419/215078990-f7e3e090-b150-42d8-8617-bc3b715a0db0.png)

---

# 2. Insecure
#### Author : Arkoov (https://github.com/ArkynGenics)
### Steps:

1. Open the ip provided `http://ctf.tcp1p.com:58161/`
2. The challenge's description hints about cookies which we can view using browser inspector.
3. There are two cookies in the website which is `isAdmin` and `session`. isAdmin might be used as an identifier to wether a user is an admin or not.
4. By changing the value to 1 we are able to get the flag in the elements like the previous challenge.

![image](https://user-images.githubusercontent.com/86551419/215080376-b80faafd-aa03-4446-a8f2-665596b28890.png)

![image](https://user-images.githubusercontent.com/86551419/215080432-e73b37bf-6603-4ffa-bb8b-f13a232e99ff.png)

---
# 3. Magic
#### Author : Arkoov (https://github.com/ArkynGenics)
### Steps:

1. The challenge provide an ip to the web and a source code for the page in php
2. After accessing the php file, We know that we can use secret parameter in the get request to fetch the flag, but if we pay a close attention on if else statement about secret parameter. the secret value isn't `240610708` and secret value hashed with md5 is `0e462097431906509019562988736854`. which when i checked the hashed result of `240610708` is  `0e462097431906509019562988736854`.
3. After searching for a while, we found a page explaining the weird behaviour of 1`==` source: `https://news.ycombinator.com/item?id=9484757`. Its said that the == comparison automatically converts 0e into float value 0.0.
4. With that we can use a value that starts with 0e after MD5 hashing. (Example : `QNKCDZO`)
5. After using `QNKCDZO` as the value, the page returns with the flag.

![image](https://user-images.githubusercontent.com/86551419/215082700-ee42fd1a-793a-406f-b078-b5091ba5fb68.png)

![image](https://user-images.githubusercontent.com/86551419/215082795-0baeca5c-bf44-49f8-9b4f-843dc33b7625.png)

![image](https://user-images.githubusercontent.com/86551419/215082904-eb04bcfc-0c48-4b06-b977-99372307bdb9.png)

---
# 4. PHP Unserialize
#### Author : Arkoov (https://github.com/ArkynGenics)
### Steps:



