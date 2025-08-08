# Challenge 3

## Challenge Instructions
```
With the key from the audio logs in hand, you arrive at the hacktivists' control system—its web portal.
Locked out by a shattered authentication gateway, you must dig through the portal code and bypass the broken lock.
```

## Hints and Resources
```
A broken gate where shadows creep, With secrets that the source code keep. Gather the crumbs, the token's soul, To prove your worth and take control.
(Look for padlock on this page)
```

## Walkthrough
Upon loading the page, we can see a lock emoji near the centre of the page

<img width="2780" height="1132" alt="Screenshot 2025-08-08 144918" src="https://github.com/user-attachments/assets/122288b2-8008-4cc2-8bf8-6a4c8c9177d1" />

When clicked, we are redirected to a new webpage where we have to submit and verify a `JWT Token`

<img width="1544" height="911" alt="image" src="https://github.com/user-attachments/assets/7f1b6517-996b-49dd-9de0-86702f6118b0" />

From `jwt.io`, JWT Token consists of 3 parts separated by dots `.`, which are `Header`, `Payload` and `Signature`

```
JSON Web Token (JWT) is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. T
his information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the HMAC algorithm) or a public/private key pair using RSA or ECDSA.

Although JWTs can be encrypted to also provide secrecy between parties, we will focus on signed tokens.
Signed tokens can verify the integrity of the claims contained within it, while encrypted tokens hide those claims from other parties.
When tokens are signed using public/private key pairs, the signature also certifies that only the party holding the private key is the one that signed it.
```

Using the hint, I try to inspect the source code and come accross the cookies, which are seemingly random but contain 2 important ones, `jwt_sample` and `jwt_secret`

<img width="2766" height="1437" alt="Screenshot 2025-08-08 145535" src="https://github.com/user-attachments/assets/798640d0-54b2-4768-bb3a-77940c276b93" />

Following `jwt_secret` and checking the console, we are given important hints that we need to construct a jwt token with the correct name and organisation, especially the `jwt secret` which is `csit-mini-challenge-2025-jwt-secret-key` 

<img width="976" height="185" alt="image" src="https://github.com/user-attachments/assets/78d6124a-de5d-4b00-90e4-36149b4218ec" />

I then use `jwt.io` as shown in the console to decode the `jwt_sample` in the cookies, which shows the structure of the token we have to fill in

<img width="2077" height="1261" alt="image" src="https://github.com/user-attachments/assets/0a5eb75a-cf20-4fd1-9d74-3e58fcac2232" />

I replaced the default secret with the one in the console and it was valid. So we have to fill in these details with accurate information and construct a `jwt token` in order to receive our flag

<img width="2082" height="1270" alt="image" src="https://github.com/user-attachments/assets/f6eba1e0-eca9-413a-bdba-d9dd7f6e8f8a" />

The details they asked for references the seemingly random cookies in the browser, as such I managed to locate all the information needed

<img width="2766" height="1437" alt="Screenshot 2025-08-08 145535" src="https://github.com/user-attachments/assets/593169d7-27d9-41fa-bdf8-764f41b05682" />

Now, we just need to create our `jwt token` with the relevant information from the cookies, and the secret from the console

<img width="2101" height="1331" alt="Screenshot 2025-08-08 150604" src="https://github.com/user-attachments/assets/f029f54f-b729-4aae-b6d5-e9079a3549fb" />

Finally, we can submit and verify our `jwt token` to receive our flag for `Challenge 3` and unlock `Challenge 4`

<img width="1539" height="1125" alt="Screenshot 2025-08-08 150629" src="https://github.com/user-attachments/assets/02f3a6f1-4455-4033-88e0-9bcdb5d4e665" />
