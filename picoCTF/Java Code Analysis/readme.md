# picoCTF Java Code Analysis

Once we use the login we can see that we get a JWT Token.

JWT Token:
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiRnJlZSIsImlzcyI6ImJvb2tzaGVsZiIsImV4cCI6MTcxMDY3OTM3OSwiaWF0IjoxNzEwMDc0NTc5LCJ1c2VySWQiOjEsImVtYWlsIjoidXNlciJ9.b5go16l65JoJPcQYxUgZYdb8URjnPBQ2spD910lbWic
```

Upon Encoding it we can see in the payload that it contains the role and a user Id. We can either look in the source code that is supplied to us on challenge begin or assume that there are two roles admin and free. And we can see that we have the user Id 1 so the admin should have something close to us (probably 0 or 2).

So we just need to brute force the secret and we can sign the Token our self to look at the Flag book.
```
john jwt.txt --wordlist=rockyou.txt --format=HMAC-SHA256
```
That gives us the secret: 1234.

Using that and changing the role and user Id in the payload we can try and use it to see if we can access the PDF.

New Token:
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiYWRtaW4iLCJpc3MiOiJib29rc2hlbGYiLCJleHAiOjE3MTA2NzkzNzksImlhdCI6MTcxMDA3NDU3OSwidXNlcklkIjoyLCJlbWFpbCI6InVzZXIifQ.oSqRLujr74wOcJfjrJepiUBQJljXByV0CsXz1y7a1yg
```

![grafik](https://github.com/6v4tq8mhlO23a/WriteUp/assets/76184566/ba0945b7-9077-4094-9b40-f65b65a387dc)

And we get our Flag:
![grafik](https://github.com/6v4tq8mhlO23a/WriteUp/assets/76184566/0598b4f2-c120-4fd4-aff8-1654f3b200b3)
