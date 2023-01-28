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

1. Open the ip provided `http://ctf.tcp1p.com:16544/`
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

1. Open the ip provided `http://ctf.tcp1p.com:32116/`
2. The page displays a php code of a flag class and a get request handler. inside the request handler we see that parameter a is being unserialized and checked if the value of isflag inside a is true, if its true then the page returns the flag.
3. From that we can copy the flag class and create a serialized string out of it and changing the properties isflag to true.
4. Next, we can request with parameter a as the serialized string `O:4:"Flag":1:{s:6:"isflag";b:1;}`
5. After that, the page returns the flag.

![image](https://user-images.githubusercontent.com/86551419/215085877-a155eed2-8e64-4b17-92a2-0b95e87a1574.png)

![image](https://user-images.githubusercontent.com/86551419/215085938-b6a094e0-6ef4-4a4e-b338-b67d3bd75709.png)

![image](https://user-images.githubusercontent.com/86551419/215086006-030a2d02-7b1f-4a1d-ad05-b2c7d6afe7a5.png)

---
# 4. PHP Unserialize
#### Author : Arkoov (https://github.com/ArkynGenics)
### Steps:
1. The challenge provide an ip to the web and a source code for the web
2. Inside the website there is a registration page and home page. In the source code there are a few things that is quite interesting to see. There are routes to admin which means there's admin privilege. inside the route page is where the flag resides. so we need to find a way to access the route `/admin`. 
![image](https://user-images.githubusercontent.com/86551419/215253641-5f5f0bb5-5399-40d5-8d7c-992de579ab30.png)
3. The admin route checks if the state session of user's isAdmin is `true or 1`
4. We then need to see where the state.session is populated with data. inside `register.ts` on line 9 the value of user is assigned directly to the body.value which can be exploited.
![image](https://user-images.githubusercontent.com/86551419/215253790-531041c9-9809-4e73-bea1-85986b7e6cab.png)
5. Using tools like burpsuite we can capture registration post request and change the json body's value by adding `"isAdmin":1`
![image](https://user-images.githubusercontent.com/86551419/215253971-8e54d503-9352-4542-b7eb-eaaab9af32b5.png)
6. After sending the request we are redirected to the home page. on the navbar menu there is an admin button which redirects us to `/admin`. This is proof that we already have admin privilege.







