# File Upload Attacks Skills Assessment
1. [Description](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/File%20Upload%20Attacks.md#description)
2. [Walkthrough](https://github.com/zyunrong/Writeups/blob/main/HTB%20Academy/File%20Upload%20Attacks.md#walkthrough)

## Description
```
You are contracted to perform a penetration test for a company's e-commerce web application. The web application is in its early stages, so you will only be testing any file upload forms you can find.

Try to utilize what you learned in this module to understand how the upload form works and how to bypass various validations in place (if any) to gain remote code execution on the back-end server.

Question:
Try to exploit the upload form to read the flag found at the root directory "/".

Hint:
Try to fuzz for non-blacklisted extensions, and for allowed content-type headers. If you are unable to locate the uploaded files, try to read the source code to find the uploads directory and the naming scheme.
```
## Walkthrough
Upon loading, we are greeted with a generic shop homepage. It is not immediately clear where the file upload can be exploited

<img width="1051" height="787" alt="Screenshot 2025-08-01 154011" src="https://github.com/user-attachments/assets/05a15baf-7a60-42d3-a817-a27e61994f7b" />

After exploring the pages, the `Contact Us` page allow us to attach and upload a screenshot as part of our feedback.

<img width="1051" height="852" alt="Screenshot 2025-08-01 154030" src="https://github.com/user-attachments/assets/75f1634a-8529-401a-88ec-e0c2a83568fc" />

The uploaded image is rendered inline as shown below

<img width="495" height="319" alt="Screenshot 2025-08-01 154140" src="https://github.com/user-attachments/assets/cda75cc8-0e65-4302-b541-933ca9d9e74f" /> 

Next, we check the page source and notice that there is a simple whitelist built in the front-end which accepts only `.jpg`, `.jpeg` or `.png`. If there is any whitelist in the back-end, it will likely accept these 3 extensions as well

<img width="1227" height="69" alt="Screenshot 2025-08-01 154200" src="https://github.com/user-attachments/assets/ceb9fbeb-15a3-445f-ae28-30a9ef6e9b5f" />

To bypass the front-end validation, we can capture the `POST` request in `Burp Suite` when we try to upload the image

<img width="1230" height="486" alt="Screenshot 2025-08-01 154503" src="https://github.com/user-attachments/assets/c37e5ad8-605e-4326-bcb7-47e050e4613b" />

Next, we use `Burp Intruder` to fuzz and see which extensions are allowed and which are blacklisted. The list of extensions used can be found [here](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst)
</br>
I also cleaned up the contents during testing so it looks cleaner

<img width="1562" height="747" alt="Screenshot 2025-08-01 154748" src="https://github.com/user-attachments/assets/c3fea9d4-e667-43b1-8f1e-56683c10cdd6" />

Looking at the results, it seems like those extensions containing `.php` are blacklisted and shows an error `Extension not allowed`

<img width="1688" height="416" alt="Screenshot 2025-08-01 155614" src="https://github.com/user-attachments/assets/22b4ea02-a435-498b-98fe-5901c4f05d84" />

However, `.pht`, `.phar`, `.pgif` and `phtm` are not blacklisted, instead showing `Only images are allowed`. This suggests that these extensions are not blocked, but there is a whitelist in the back-end which checks for `.jpg`, `.jpeg` or `.png` similar to the front-end

<img width="1708" height="404" alt="Screenshot 2025-08-01 155635" src="https://github.com/user-attachments/assets/c51a2093-2144-4542-834f-89a2e2270986" />

As such, we modify to include `.jpg` as a suffix to our payload to see if this allows us to upload arbitrary files

<img width="1560" height="756" alt="Screenshot 2025-08-01 155729" src="https://github.com/user-attachments/assets/107c9412-c929-43ef-84eb-ee4633fa9e4f" />

However, we once again get `Only images are allowed`. This might mean that there is a check being done on the uploaded file's `MIME-Type`, as the `Content-Type` header is correct 

<img width="1719" height="453" alt="Screenshot 2025-08-01 155833" src="https://github.com/user-attachments/assets/eef1ca43-ee49-4eae-bf73-fb2fde61bd6b" />

To test it out, I added `ÿØÿî` which is the file signature for a `.jpg` file. The file signatures can be found [here](https://en.wikipedia.org/wiki/List_of_file_signatures)

<img width="1558" height="700" alt="Screenshot 2025-08-01 160709" src="https://github.com/user-attachments/assets/cbf737b7-b39d-4519-96f7-c1afbbf1ad98" />

The arbitrary file upload is successful as we can see our file is uploaded and rendered inline on the webpage

<img width="1747" height="621" alt="Screenshot 2025-08-01 160724" src="https://github.com/user-attachments/assets/ff4c8c1f-9fd6-498d-a191-4749fc146e05" />

Now that we can upload files, we need to find the directory where the files are stored, so that we can achieve code execution. To do that, I will use `Burp Intruder` to fuzz the `Content-Type` to see if any other file types are allowed which may allow us to exploit and leak the source code

<img width="1554" height="729" alt="Screenshot 2025-08-01 162453" src="https://github.com/user-attachments/assets/c34bec53-b103-4571-97e0-5bd81041d335" />

The list of all `Content-Type` can be found [here](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt)
</br>
First, download the wordlist
`wget https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt`

Next filter the wordlist to only contain those image content types
`cat web-all-content-types.txt | grep 'image/' > image-content-types.txt`

<img width="1505" height="378" alt="Screenshot 2025-08-01 160127" src="https://github.com/user-attachments/assets/31392498-4e7e-462b-b698-51bc1dbe5aed" />

Looking at the results, we see that svg+xml are allowed which can allow for XXE exploitation to leak the source code

<img width="1746" height="635" alt="Screenshot 2025-08-01 162735" src="https://github.com/user-attachments/assets/faf34e2a-35d5-430e-97cf-a1dd4419eb2a" />

To test the XXE exploitation, we use 
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=upload.php"> ]>
<svg>&xxe;</svg>
```
to leak the source code in the upload.php to find where the directory is, encoded in base64

<img width="1553" height="726" alt="Screenshot 2025-08-01 162938" src="https://github.com/user-attachments/assets/e0f9ca3b-9355-419a-a6ab-94ce83dd92a3" />

We can see that it worked and the webpage contains the base64 encoded source code

<img width="1713" height="741" alt="Screenshot 2025-08-01 163009" src="https://github.com/user-attachments/assets/330ad3dc-91c6-4906-b325-f424bc66ffa8" />

We can use `echo [source code] | base64 -d` to decode the source code
</br>
Once decoded, we see that the file are located in the `./user_feedback_submissions/` directory. The file is also renamed before storing, appending the date of upload and `_` as a prefix to the file name.

<img width="1917" height="865" alt="Screenshot 2025-08-01 163107" src="https://github.com/user-attachments/assets/15ca41cf-bf52-4b99-9d1b-679a9da1c0e5" />

Referring to the PHP:date manual, the date of uploaded will be formatted with 2 digits for the day, month and year. In my example, it is `August 1st 2025`, as such the prefix appended to the filename will be `250801_`

<img width="507" height="21" alt="image" src="https://github.com/user-attachments/assets/cac80f1d-eb58-437f-a520-1caf061c2b33" />

As such, now we can proceed to upload our payload file to allow for code execution. I will use `<?php system($_REQUEST['cmd']); ?>` to spawn a basic php web shell

<img width="1558" height="781" alt="Screenshot 2025-08-01 163328" src="https://github.com/user-attachments/assets/13707c19-03ee-4cf1-9c9d-d445005b805a" />

We can see that our payload file has been successful uploaded

<img width="1710" height="665" alt="Screenshot 2025-08-01 163341" src="https://github.com/user-attachments/assets/000ebfc7-9f2c-4f52-bc5f-c2c143e89dfa" />

Next, we need to capture a `GET` request and modify it to visit the directory where our uploaded file is at. I also added a command `ls /` to test and see which extension will execute the command, and locate the flag in the root directory `/`

<img width="1593" height="756" alt="Screenshot 2025-08-01 164032" src="https://github.com/user-attachments/assets/aee09c2a-32f3-4254-ac1e-26e74cc1b5e1" />

Looking at the results, only `.phar` allows for command execution, and the name of the file containing the flag is shown

<img width="1745" height="778" alt="Screenshot 2025-08-01 164011" src="https://github.com/user-attachments/assets/feae9aeb-c8bc-4dca-9e7c-d9b7a54756b7" />

Lastly, we can just visit the page again with the command `cat /[FILE NAME]` to read the contents of the flag

<img width="1476" height="159" alt="Screenshot 2025-08-01 164155" src="https://github.com/user-attachments/assets/da349d06-337b-476c-b240-44eb48430153" />
