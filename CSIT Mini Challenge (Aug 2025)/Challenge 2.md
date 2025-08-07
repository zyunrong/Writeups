# Challenge 2

## Challenge Instructions
```
You've successfully blocked the hacktivists' first strike. While cleaning the repository, you uncovered discarded communication logs. 
The data is scrambled, but you've isolated two strange audio fragments. 
They sound like echoes from long-forgotten transmission tech. Decode these phantom sounds to unlock the next piece of the puzzle. 
```

## Hints and Resources
```
An old language echoes in bursts and beeps — one speaks in dots and dashes, the other through numbered keys. 
From fading tones and rhythmic clicks, two paths emerge. Only when their voices unite does the message come alive. (Look for seedling on this page)
```

## Walkthrough
Taking a look at the webpage, we can see a seedling emoji located just above the `Hints and Resources`

<img width="1918" height="879" alt="Screenshot 2025-08-07 225021" src="https://github.com/user-attachments/assets/e98619bb-2e78-4397-91ff-46c25fb9df0a" />

Upon clicking on the seedling, a file `challenge2.txt` is downloaded. When opened, the text file is indecipherable but we can make out certain texts important to us. </br>
Namely `challenge2_sound2.wavux` and `challenge2_sound1.wavux`

<img width="906" height="888" alt="image" src="https://github.com/user-attachments/assets/6afba232-333e-4cd1-8e00-b645be4181ed" />

Looking at the file signature at the start, I identified it to be a compressed zip file format. This means that if we rename the file to `challenge2.zip`, we may be able to access the contents inside

<img width="950" height="579" alt="image" src="https://github.com/user-attachments/assets/9aaeb90a-e0d1-481e-a44a-fe79d7e6d70a" />

After renaming, we are able to access the contents, which contain 2 audio file and a `README.txt`

<img width="977" height="263" alt="image" src="https://github.com/user-attachments/assets/ea0d118f-7ca5-4e5f-8804-b77bb82092db" />

The `README.txt` contains the following text

```
After successfully decoding the 2 wave files, you will get 2 flag parts (e.g., FLAG1 and FLAG2).
You have to combine the 2 flag parts and submit it with the following format: CSIT{FLAG1FLAG2}
```

As such, we have to decode both files and combine them to get the final flag

</br>

The first audio file `challenge2_sound1.wav` contains the audio of a sequence of phone numbers being inputted. After searching, I used a `DTMF Decoder` to decode the numbers being pressed was `62#74#23#43#81#93`

<img width="848" height="375" alt="image" src="https://github.com/user-attachments/assets/edfd545e-195a-4db0-97c7-ba6594aef7a0" />

The second audio file `challenge2_sound2.wav` contains a series of long and short beeps, likely to be morse code. As such, using a `Morse Decoder` to decode the message to be `H@LLS7AT10N`

<img width="991" height="139" alt="image" src="https://github.com/user-attachments/assets/bab0b157-8270-4523-aaef-7a1a0f5d73ef" />

From the second part of the flag, we can see the similarities to the flag we found in `Challenge 1`, however, the first half `62#74#23#43#81#93` is likely not the final form of the flag

</br>

Since the sequence of numbers was derived from a phone, it might reference to the characters associated with each number in the phone keypad. 

</br>

The quirk of the typing with a keypad is you have to press the associated number `1`, `2`, `3`, or `4` times to get to the letter you want. As such the `62` might mean the `2nd` character on the keypad `6`. This is further supported by the `#` character which would then be a delimiter character and can be excluded in the final string

<img width="571" height="490" alt="image" src="https://github.com/user-attachments/assets/852ae80a-2124-40ae-a72f-c580fa20e064" />

Following this logic, I quickly decoded the flag to be `NSCITY`, when combined with the other half, forms `NSCITYH@LLS7AT10N`.

</br>

The flag `CSIT{NSCITYH@LLS7AT10N}` is similar to the flag in `Challenge 1` which references a MRT Station in Singapore. When submitted, it is correct and we are given access to `Challenge 3`
