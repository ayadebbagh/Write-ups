![](Screenshot2025-09-21at2.37.47PM.png)
Starting with inspecting the code of the python file which has the following:

```
### THIS FUNCTION WILL NOT HELP YOU FIND THE FLAG --LT ########################
def str_xor(secret, key):
    #extend key to secret length
    new_key = key
    i = 0
    while len(new_key) < len(secret):
        new_key = new_key + key[i]
        i = (i + 1) % len(key)        
    return "".join([chr(ord(secret_c) ^ ord(new_key_c)) for (secret_c,new_key_c) in zip(secret,new_key)])
###############################################################################

flag_enc = open('level2.flag.txt.enc', 'rb').read()

def level_2_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    if( user_pw == chr(0x33) + chr(0x39) + chr(0x63) + chr(0x65) ):
        print("Welcome back... your flag, user:")
        decryption = str_xor(flag_enc.decode(), user_pw)
        print(decryption)
        return
    print("That password is incorrect")

level_2_pw_check()
```
The `level_2_pw_check()` function tells us what the right password is, which is a concatenation of `chr(0x33) + chr(0x39) + chr(0x63) + chr(0x65)`. After researching what the `chr()` function does, we learn that it is a built-in function that takes an integer as an argument and returns a string representing the corresponding Unicode character. Using a unicode converter, we see that the equivalent of `chr(0x33) + chr(0x39) + chr(0x63) + chr(0x65)` is `39ce` which is the right password that gives us the flag.

Flag: `picoCTF{tr45h_51ng1ng_502ec42e}`