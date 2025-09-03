# Web Attacks Skills Assessment
1. [Description](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/Web%20Attacks.md#description)
2. [Walkthrough](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/Web%20Attacks.md#walkthrough)

## Description
```
You are performing a web application penetration test for a software development company, and they task you with testing the latest build of their social networking web application. Try to utilize the various techniques you learned in this module to identify and exploit multiple vulnerabilities found in the web application.

The login details are provided in the question below.

Authenticate to with user "htb-student" and password "Academy_student!" 

Question:
Try to escalate your privileges and exploit different vulnerabilities to read the flag at '/flag.php'.
```

## Walkthrough
First I loaded the target in BurpSuite and login with the credentials provided

<img width="949" height="809" alt="Screenshot 2025-08-28 221646" src="https://github.com/user-attachments/assets/5ffa0b9e-ab9f-47b6-ac7c-942fbc4e2163" />

I captured the requests sent during login and this stood out to me, as it seems to fetch a profile based on the URL alone, which may be susceptible to IDOR vulnerabilities

<img width="1225" height="293" alt="Screenshot 2025-08-28 221729" src="https://github.com/user-attachments/assets/b8cf9eb2-2d05-4598-90f9-c9ca09ce9839" />

We can see that we are logged in as `Pablo Perrone`

<img width="1912" height="886" alt="Screenshot 2025-08-28 221812" src="https://github.com/user-attachments/assets/c4e45a3c-d1ce-4f9a-9395-927cd43fcb0d" />

After exploring, the only page available was the `Change Password` page under `Account Settings`.

<img width="1918" height="825" alt="Screenshot 2025-08-28 221905" src="https://github.com/user-attachments/assets/0cbe5e55-3fcb-447e-9170-7e1553f73973" />

I tested the functionality and captured the requests, it first sends a GET request to the `/token` endpoint, to receive the token for our current user profile. This might be susceptible to IDOR vulnerabilities as well

<img width="1227" height="321" alt="Screenshot 2025-08-28 223018" src="https://github.com/user-attachments/assets/c43c770a-5287-46db-802f-eece4363d82f" />

It then sends a POST request to `/reset.php`, with the `uid`, corresponding `token` and the `new password`

<img width="1226" height="364" alt="Screenshot 2025-08-28 222132" src="https://github.com/user-attachments/assets/bc71bf4b-ad4a-40b2-9701-5d3f4f666a55" />

Upon successful reset, we receive the response `Password changed successfully`

<img width="1229" height="516" alt="Screenshot 2025-08-28 222600" src="https://github.com/user-attachments/assets/7c003ca9-cb1c-4d05-ab3f-14e2967c0a24" />

Now I have a rough idea of what I need to do, I might need to exploit the IDOR vulnerability when retrieving profile pages to gain access to other profiles, maybe an admin of sort, then change the password such that we can login as the admin
</br>
As such, I tested in Burp Repeater and it is susceptible to IDOR vulnerabilities. We can see that we managed to fetch a profile different from ours

<img width="1226" height="470" alt="Screenshot 2025-08-28 221923" src="https://github.com/user-attachments/assets/502ef062-1931-48db-afa1-4bd2717568d5" />

Next I used Burp Intruder to fuzz for different profiles from a range of 1-80, which is roughly based on our initial profile number of 74.

<img width="1609" height="811" alt="Screenshot 2025-08-28 222015" src="https://github.com/user-attachments/assets/012f032c-6f56-4b26-a44f-1f57f02d5e9a" />

As I am not sure what exactly I am looking for yet, I changed the settings to flag out any response containing `admin`

<img width="556" height="525" alt="Screenshot 2025-08-28 222046" src="https://github.com/user-attachments/assets/89f69e33-30db-49ac-8210-f8a548bbe47a" />

I managed to discover profile number 52, with the user belonging to a company called `Administrator`. This is likely the profile we need to use

<img width="1743" height="721" alt="Screenshot 2025-08-28 222240" src="https://github.com/user-attachments/assets/9f8db0fe-e570-4fc7-b857-748aa0e1dd2a" />

I first exploit the IDOR vulnerability in the `/token` endpoint to get the token for the administrator's profile

<img width="1226" height="457" alt="Screenshot 2025-08-28 223045" src="https://github.com/user-attachments/assets/4e36b8dc-d809-4e2d-8745-f0d38f9f98a7" />

I then try to replicate the POST request to reset password, but changing the `uid` to 52 and to the corresponding `token`. However we get `access denied`. This might be due to POST requests being blocked when changing administrator's password

<img width="1227" height="695" alt="Screenshot 2025-08-28 222313" src="https://github.com/user-attachments/assets/53adad3b-5a2c-4f03-91a9-ae3eca874aff" />

As such, I tried to convert it to a GET request and we get the success message `Password changed successfully`

<img width="1230" height="464" alt="Screenshot 2025-08-28 223111" src="https://github.com/user-attachments/assets/447bf257-eede-4f8b-84cf-dbc1a82e4cb7" />

Now, we login to the administrator's profile with the username `a.corrales` and the new password `test`. We can see a new `Add Event` button which was previously not present

<img width="1918" height="865" alt="Screenshot 2025-08-28 223217" src="https://github.com/user-attachments/assets/b2be8430-cdfa-45a3-ae53-40c600c336b4" />

Upon navigating to it, we are able to fill in the `Event name`, `Event details`, and the `Date`

<img width="953" height="547" alt="Screenshot 2025-08-28 223236" src="https://github.com/user-attachments/assets/564e2e75-2615-4d8e-b226-59cb1ef5ed4f" />

Capturing the request in Burp, we can see it sends a POST request to the `addEvent.php` endpoint, with the body being in `XML` format

<img width="1226" height="497" alt="Screenshot 2025-08-28 223313" src="https://github.com/user-attachments/assets/cce9f48e-4b92-4968-a07c-777ff7616cfc" />

I tried different inputs to see which ones will be reflected in the reponse. In this case, it is the `Event Name`

<img width="1224" height="627" alt="Screenshot 2025-08-28 223514" src="https://github.com/user-attachments/assets/e587a164-a105-4a5c-a95d-411797f4240c" />

I then added a payload to exploit the XXE vulnerability, by creating an entity which references the `/flag.php` file that I want to read, then encoding in base64 to bypass any character restrictions

<img width="1567" height="637" alt="Screenshot 2025-08-28 224020" src="https://github.com/user-attachments/assets/8fe1cd87-abdc-40ea-948b-7670591c3296" />

We get the contents of the `/flag.php` file in base64 encoding, and when decoded gives us the flag









