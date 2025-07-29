# Login Brute Forcing Skills Assessment
1. [Skills Assessment Part 1 Description](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/Login%20Brute%20Forcing.md#skills-assessment-part-1-description)
2. [Skills Assessment Part 1 Walkthrough](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/Login%20Brute%20Forcing.md#skills-assessment-part-1-walkthrough)
3. [Skills Assessment Part 2 Description](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/Login%20Brute%20Forcing.md#skills-assessment-part-2-description)
4. [Skills Assessment Part 2 Walkthrough](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/Login%20Brute%20Forcing.md#skills-assessment-part-2-walkthrough)

## Skills Assessment Part 1 Description
```
The first part of the skills assessment will require you to brute-force the the target instance. Successfully finding the correct login will provide you with the username you will need to start Skills Assessment Part 2.

You might find the following wordlists helpful in this engagement: usernames.txt and passwords.txt
Question 1:
What is the password for the basic auth login?

Question 2:
After successfully brute forcing the login, what is the username you have been given for the next part of the skills assessment?
```
The wordlist provided are here:
[usernames.txt](https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt) and [passwords.txt](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt)

## Skills Assessment Part 1 Walkthrough

Upon loading the target, we are required to provide a `Username` and `Password` before being able to access the website's content

<img width="1258" height="563" alt="image" src="https://github.com/user-attachments/assets/718a0093-1f27-4036-ae25-385867e5d8f9" />

When I clicked `Cancel`, an error `401 Authorization Required` appeared which suggests that the credentials are supplied through a `GET` request to the web server before the web server responds with the content

<img width="1261" height="433" alt="image" src="https://github.com/user-attachments/assets/a1e83360-f9fa-4b11-80f4-885898f9c319" />

As such, we can proceed to try to bruteforce the credentials to gain access. First we download the `username.txt` and `passwords.txt` from the description which will be used as our wordlists for the bruteforcing.

To download the wordlists:
```
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Usernames/top-usernames-shortlist.txt
```
```
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt
```
<img width="814" height="161" alt="Screenshot 2025-07-28 113551" src="https://github.com/user-attachments/assets/a471e7d2-353f-4165-8537-fa8e6ade91a5" />

Next, we used hydra along with our supplied wordlists to try and find credentials to access the website

Using this command,

```hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt 94.237.54.192 http-get / -s 40005```

We can see that we have found a pair of `Username` and `Password` that we can use to access the website

The `Password` is the flag for the `Question 1`

<img width="1082" height="248" alt="Screenshot 2025-07-28 184129" src="https://github.com/user-attachments/assets/610e27c6-9dda-4522-9fc4-b3285666b3e8" />

Upon using the above credentials to login to the website, we are given our second flag and answer for `Question 2`, and the username required for the next part of the skills assessment

<img width="1255" height="581" alt="Screenshot 2025-07-28 184150" src="https://github.com/user-attachments/assets/c794d151-62ba-4ce4-9f5c-9a861fe19b07" />

## Disclaimer
```
Complete Part 1 before going to Part 2 as Part 2 has spoilers for the flag for Part 1
```

## Skills Assessment Part 2 Description
```
This is the second part of the skills assessment. YOU NEED TO COMPLETE THE FIRST PART BEFORE STARTING THIS. Use the username you were given when you completed part 1 of the skills assessment to brute force the login on the target instance.

Question 1:
What is the username of the ftp user you find via brute-forcing?

Question 2:
What is the flag contained within flag.txt 
```

## Skills Assessment Part 2 Walkthrough

Upon trying to load the target, we get an error and are unable to proceed any further. However, given the username from Part 1 is `satwossh`, we can try to establish a ssh connection to the target using the username
<img width="1004" height="698" alt="Screenshot 2025-07-28 220712" src="https://github.com/user-attachments/assets/53b29b1d-7796-4d63-8f75-444dd2acbab1" />

Using the wordlists from above as well, we try to bruteforce the password for the username `satwossh` using hydra `hydra -l satwossh -P 2023-200_most_used_passwords.txt 94.237.121.185 -s 47331 ssh`

<img width="813" height="103" alt="Screenshot 2025-07-28 222011" src="https://github.com/user-attachments/assets/5232bd23-f906-4621-b74e-c8e9a5db2995" />

With the password, we try to ssh to the target using `ssh satwossh@94.237.121.185 -p 47331` and managed to gain access

<img width="820" height="573" alt="Screenshot 2025-07-28 222144" src="https://github.com/user-attachments/assets/5a838566-5dfe-43e4-a3a2-9b2b417d98c6" />

Once we gained access, I first try to see what files are in the current directory. The `passwords.txt` and `username-anarchy` are likely needed for the upcoming portions

<img width="817" height="122" alt="Screenshot 2025-07-28 222226" src="https://github.com/user-attachments/assets/ea942d0c-3e58-433a-8146-615e15adfd3a" />

The other interesting file, `IncidentReport.txt`, mentions that a user `Thomas Smith` has been uploading files to the FTP server 

<img width="816" height="434" alt="Screenshot 2025-07-28 222415" src="https://github.com/user-attachments/assets/24c13d43-0359-4ee5-b97d-597914bff116" />

As such, we will try to gain access to the FTP server as `Thomas Smith` by using username-anarchy to generate a list of possible usernames based on `Thomas Smith` through `./username-anarchy Thomas Smith > thomas_smith_usernames.txt`. We will then copy the file generated to the directory with the `passwords.txt` as it will be used as our wordlist for the password

<img width="815" height="153" alt="Screenshot 2025-07-28 222712" src="https://github.com/user-attachments/assets/7cbfcd3f-e784-40aa-bb5c-4ad5c2a465e9" />

With our wordlists prepared, we can use hydra to bruteforce the login credentials to the FTP server through `hydra -L thomas_smith_usernames.txt -P passwords.txt 127.0.0.1 ftp`

<img width="812" height="316" alt="Screenshot 2025-07-28 222825" src="https://github.com/user-attachments/assets/87da88ab-7fc8-4e80-8336-ec2a787957fb" />

The username found is the flag for `Question 1`

With the username and password, we login to the FTP server as `Thomas Smith` and managed to gain access through `ftp ftp://thomas:"chocolate!"@localhost`, using `""` to properly escape the `!` in the password

<img width="817" height="280" alt="Screenshot 2025-07-28 223120" src="https://github.com/user-attachments/assets/309f02f4-ecbe-40e6-be74-15b27df03cd1" />

With access to the FTP server, we can first execute `ls` and see that the `flag.txt` is in the current directory. As such we can simply download it from the FTP server using `get flag.txt`

<img width="815" height="380" alt="Screenshot 2025-07-28 223151" src="https://github.com/user-attachments/assets/9c2fd7a5-74b2-4755-afe1-5a4fb8290181" />

Lastly, we can simply read the contents of the file to get the flag and answer for `Question 2`
<img width="848" height="76" alt="Screenshot 2025-07-28 223207" src="https://github.com/user-attachments/assets/51bd4f5a-ccd2-403c-bdaa-2a5d496432c0" />




