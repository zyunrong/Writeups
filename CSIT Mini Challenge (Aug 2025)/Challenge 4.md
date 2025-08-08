# Challenge 4

## Hints and Resources
```
The map may look familiar, but something's off. Perhaps the truth lies in how it's presented, not just what it shows. (Look for... there's nth to look for on this page)
```

## Walkthrough
Upon loading, all we are given is a map of Singapore's MRT with the hint that something's off with it

<img width="2771" height="1129" alt="image" src="https://github.com/user-attachments/assets/efee1595-79d7-4d47-bc66-7153ed0c49e2" />

As such, after looking through the image, I realised that the bottom right has been altered with a QR code and text that says `Hint, Scan Me`

<img width="1493" height="1480" alt="Screenshot 2025-08-08 152255" src="https://github.com/user-attachments/assets/b10406d2-342f-481d-8eea-85591bd04321" />

Upon scanning and visiting the link, we are given a hint with the text

```
Each of these flags you have obtained has more than one significance.
Use the map and combine them to form a significant day to Singapore.

To complete this challenge, use the following flag format:
CSIT{STATIONCODE1-STATIONCODE2-STATIONCODE3}
```

To break it down, we have to use the previous 3 flags we found and combine them to form the final flag. The previous flags were all referencing specific stations on Singapore's MRT, namely `EW Paya Lebar`, `NS City Hall` and `NS Woodlands`

</br>

The flag format also mentions that it is constructed from 3 different station codes, which is likely to be the station codes of those referenced by the flags

</br>

Lastly, it has to form a significant day to Singapore, which based on the CTF's theme, is Singapore's National Day, which is 9 August 2025

<img width="1493" height="1480" alt="Screenshot 2025-08-08 152255" src="https://github.com/user-attachments/assets/c489f264-18e8-4043-ac53-1d46ac4e0ad7" />

By using the marked out station codes of the respective station and the station lines mentioned in the flags, we can construct the final flag to be `CSIT{NS9-EW8-NS25}` which forms Singapore's National Day 2025

</br>

However, when I tried the flag, it was incorrect but it fits all the hints given. As such I experimented with a few variations and ultimately arrived at `CSIT{NS09-EW08-NS25}`, which worked as the final flag to `Challenge 4`
