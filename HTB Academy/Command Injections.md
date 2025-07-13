# Command Injection Skills Assessment

## Description
```
You are contracted to perform a penetration test for a company, and through your pentest, you stumble upon an interesting file manager web application.
As file managers tend to execute system commands, you are interested in testing for command injection vulnerabilities.

Use the various techniques presented in this module to detect a command injection vulnerability and then exploit it, evading any filters in place.

Authenticate with user "guest" and password "guest"

What is the content of '/flag.txt'?

Hints:
It is always easier to inject our command in an input going at the end of the command, rather than in the middle of it, though both are possible.
```
## Walkthrough
Initial thoughts upon reading the description
1. I would be looking for possible places for system commands execution on the target to exploit a command injection vulnerability
2. I would have to exploit the vulnerability to read the contents of /flag.txt

Upon authenticating to the target with user "guest" and password "guest", the file manager application has a simple interface with various notables
- A search bar
- An empty folder ```tmp``` with several placeholder ```.txt``` files
- 4 actions available for the ```.txt``` files, ```Preview```, ```Copy To...```, ```Direct Link```, ```Download```
<img width="1269" height="790" alt="image" src="https://github.com/user-attachments/assets/02c7d653-4c25-46bb-b50f-a8c46f474786" />

After exploring the target and the functions available (clicked around on everything), the most noteworthy was the ```Copy To...``` function availabe for the .txt files, especially the ```move``` function

<img width="1060" height="300" alt="image" src="https://github.com/user-attachments/assets/a86de839-8416-4127-ba86-cb3a71712d39" />

After clicking on move without specifying the ```tmp``` folder, an error appeared with the following text, 
```Error while moving: mv: '/var/www/html/files/51459716.txt' and '/var/www/html/files/51459716.txt' are the same file```

<img width="1060" height="268" alt="image" src="https://github.com/user-attachments/assets/7de13d7e-4507-4e6a-adb5-b82e6112a170" />

This is interesting as the error showed that a system command was used, being the ```mv``` command, which is used to move or rename files
To verify this, I tried to replicate the error and noticed that the error message was exactly the same
<img width="488" height="53" alt="image" src="https://github.com/user-attachments/assets/09959873-0ef8-439c-a93b-1179fc78969d" />


The command has a format ```mv [SOURCE] [DEST]```, and as such, if we can figure out a way to manipulate the source and destination input, we can exploit it to execute system commands that we want

I noticed that when I selected the folder ```tmp``` to move the file to, the URL seemingly changed
This suggests to me that the input can potentially be manipulated through the query field in the GET request

<img width="367" height="30" alt="image" src="https://github.com/user-attachments/assets/a205a2ba-6450-44d0-ad32-e2229badd04a" />

<img width="392" height="34" alt="image" src="https://github.com/user-attachments/assets/b2604a71-8b96-4fc9-939e-d8a52a70fbcd" />

To test it out, I captured the request in Burp Suite and sent it to Repeater with the following adjustments
<img width="524" height="25" alt="image" src="https://github.com/user-attachments/assets/cbf485ef-7d08-4bc6-b81b-7312e5515776" />

I modified the destination input to ```;ls``` to attempt to execute the ```ls``` command as the overall system command executed would be ```mv [SOURCE] ;ls```

The response suggests that this is the right direction, but there might be blacklisted characters and/or commands that we have to bypass for it to work

<img width="595" height="149" alt="image" src="https://github.com/user-attachments/assets/7b307bb8-3dd6-41fc-8481-de386a2b578a" />

<img width="584" height="43" alt="image" src="https://github.com/user-attachments/assets/2e5e6ff7-ca53-4502-9445-5b52fcce7499" />

<img width="423" height="51" alt="image" src="https://github.com/user-attachments/assets/e9600cb9-2b1d-4703-b501-3294714277df" />


<img width="596" height="162" alt="Screenshot 2025-07-13 232250" src="https://github.com/user-attachments/assets/dc40b625-9f85-4654-a1f9-b0e98c7e2541" />



