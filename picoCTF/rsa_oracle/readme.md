# rsa_oracle

In this challenge, we are provided with the following:

    A message containing the flag.
    The encrypted password used to encrypt the message.
    An oracle that can encrypt and decrypt any input except the password.

Our objective is to either:

    Retrieve the private key, or
    Find a way to decrypt the password using the oracle.

The Oracle's Behavior

The oracle enforces a single restriction: it refuses to decrypt the exact password. However, it can process any other value. This behavior opens up a potential vulnerability that we can exploit.

Our approach leverages the ability to:

    Obtain the encrypted value of a small number (e.g., 2) using the oracle.
    Use mathematical operations to indirectly derive the password.

Step-by-Step Exploit
1. Getting the Encrypted Value of 2

To exploit the oracle, we first request the encryption of the number 2. Importantly, we need to avoid automatic type conversion, which would transform the number into its hexadecimal representation. To ensure this, we use the following Python snippet to send the raw byte representation of 2:

```
python3 -c '
import time
print("E")  # Signal to the server
time.sleep(2)
print(chr(2))  # Send the raw byte for the number 2
' | nc <SERVER> <PORT>
```

The oracle responds with the ciphertext for 2:

5067313465613043651275429665315895824157755779222372979446076012356324498190828210335763979330272318657269048435311897896433721115606764442199497891219230

2. Exploiting Multiplicative Properties

Next, we multiply the encrypted password (password.enc) with the ciphertext of 2. This operation effectively shifts the encrypted password into a new, unique space that can bypass the oracle's restrictions.

Encrypted Password: 
1765037049764047724348114634473658734830490852066061345686916365658618194981097216750929421734812911680434647401939068526285652985802740837961814227312100

Encrypted 2:
5067313465613043651275429665315895824157755779222372979446076012356324498190828210335763979330272318657269048435311897896433721115606764442199497891219230

Resulting Value:
8943996009575278864115573703991201712571074215117036179086668593808926390701605353776642251720073674504199260815309943536447818516632375817246532671301018861809177830861805075153232349532481616132684137546455678578211809237269343473990373627305672839436403271663643913293883535204677067137127081834731683000

3. Decrypting the Result

We pass the resulting value to the oracle for decryption. The oracle returns the decrypted hex value:

707062c872

4. Extracting the Password

To retrieve the original password:

    Divide the decrypted value by 2 (undoing the multiplication step).
    Convert the resulting hex value into ASCII characters. This yields the password:

881d9

5. Decrypting the Message

With the recovered password, we can now decrypt the encrypted message using openssl:
```
openssl enc -aes-256-cbc -d -in message.enc -out message_decrypted.txt
```
After running the decryption command, the file message_decrypted.txt contains the flag.
