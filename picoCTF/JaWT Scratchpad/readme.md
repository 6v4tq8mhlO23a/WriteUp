# picoCTF JaWT Scratchpad

Upon visiting the website you are greeted with a welcome screen and the information that you can not login as the username admin.

On the website as well you can see a hyperlink on the name "John" that leads to the GitHub Repository of John the Ripper. So we can already tell that we probably need to brute force something.

After looking around for a bit I decided to test the login and intercept it with Burp. The first Post Request just supplies our username that we wrote into the Login field. Once you forward the Request you can see a second and third Request that provides us with a JWT Cookie.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoidGVzdCJ9.IAu_YSHppFe8hXH_BSPb4OLJYGUi8wXqXdS0T33cKbA
```
If you Base64 decode the JWT Cookie you can see that we only supply the algorithm HS256 and the type JWT in the header field and in the payload part we can see our supplied username:
```
{"typ":"JWT","alg":"HS256"}{"user":"test"}
```
As we already saw on the Homepage a hyperlink leads us to John the Ripper so probably need to brute force the secret key of the JWT Token so we can sign the token our self and modify it.

So I started John the Ripper, put our JWT token into a file and used the rockyou.txt wordlist to brute force the secret.
```
john jwt.txt --wordlist=rockyou.txt --format=HMAC-SHA256
```
![grafik](https://github.com/6v4tq8mhlO23a/WriteUp/assets/76184566/84d5e36a-9e1e-484c-91af-6a079b8e7040)

After just a few seconds we obtained the secret: ilovepico

Now we can just modify the token, use the secret to sign it and we should be able to login as admin. To generate and sign the token I just used the handy JWT De- and Encoder on jwt.io
![grafik](https://github.com/6v4tq8mhlO23a/WriteUp/assets/76184566/5d993e3c-94b7-4fd7-ac73-195421354823)
Once we use this token in both get Requests we should be logged in as the admin user and get the flag.
![grafik](https://github.com/6v4tq8mhlO23a/WriteUp/assets/76184566/4a75bc8e-b637-4743-bec4-b121ed651da3)
