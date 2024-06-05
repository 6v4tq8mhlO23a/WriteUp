In this challenge we are supposed to use a buffer overflow to overwrite the return address and let it return to the win function. The first thing I did was looking at the source code.

Here we could see the function "vuln" that uses the dangerous function gets. Gets does not check if our input exceeds the buffer size. With that we can create a buffer overflow. After the gets Function the function "get_return_address()" is called. So we basically just need to supply enough characters until we hit the return address.

Now we can open the binary in gdb and examine the function addresses with the command:
`info function`
![grafik](https://github.com/6v4tq8mhlO23a/WriteUp/assets/76184566/a544dd2c-56e8-416b-a763-9f76a9f3eda2)

We can see that the address of the win function is 0x080491f6. After that I just disassembled the get_return_address function and set a breakpoint at the `mov eax, DWORD PTR [ebp+0x4]` instruction. Now i just supply a couple of 'AAAA' as our input and examined the stack with `x/20wx $esp`. The return address is placed in `DWORD PTR [ebp+0x4] (0xffffd0cc)`. Now we can just check how many char's as padding are needed to be supplied and then we can overwrite the return address.

I wrote a simple and shitty python script to do that for me and get the flag:

```
import os
import sys
from pwn import *

payload = b'A' * 44

payload += p64(0x080491f6)

p = remote(#HOSTADDRESS, #PORTNUMBER)
p.recvuntil('Please enter your string:')
p.sendline(payload)
p.interactive()
```
