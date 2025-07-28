# Login Brute Forcing Skills Assessment

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

The `Password` is the flag for the first question

<img width="1082" height="248" alt="Screenshot 2025-07-28 184129" src="https://github.com/user-attachments/assets/610e27c6-9dda-4522-9fc4-b3285666b3e8" />

Upon using the above credentials to login to the website, we are given our second flag, and the username required for the next part of the skills assessment

<img width="1255" height="581" alt="Screenshot 2025-07-28 184150" src="https://github.com/user-attachments/assets/c794d151-62ba-4ce4-9f5c-9a861fe19b07" />
