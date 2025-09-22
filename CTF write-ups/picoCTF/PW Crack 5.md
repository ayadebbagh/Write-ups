**Challenge:**
Can you crack the password to get the flag?Download the password checker [here](https://artifacts.picoctf.net/c/33/level5.py) and you'll need the encrypted [flag](https://artifacts.picoctf.net/c/33/level5.flag.txt.enc) and the [hash](https://artifacts.picoctf.net/c/33/level5.hash.bin) in the same directory too. Here's a [dictionary](https://artifacts.picoctf.net/c/33/dictionary.txt) with all possible passwords based on the password conventions we've seen so far.

---
The content of `level5.py` is the following
```
import hashlib

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

flag_enc = open('level5.flag.txt.enc', 'rb').read()
correct_pw_hash = open('level5.hash.bin', 'rb').read()

def hash_pw(pw_str):
    pw_bytes = bytearray()
    pw_bytes.extend(pw_str.encode())
    m = hashlib.md5()
    m.update(pw_bytes)
    return m.digest()

def level_5_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    user_pw_hash = hash_pw(user_pw)
    
    if( user_pw_hash == correct_pw_hash ):
        print("Welcome back... your flag, user:")
        decryption = str_xor(flag_enc.decode(), user_pw)
        print(decryption)
        return
    print("That password is incorrect")

level_5_pw_check()
```
We are given a `dictionary.txt` file that has numerous potential right passwords. These passwords are hashed and the hash is then compared to the correct hash to see if it is the right password. We can use this file and iterate over all the potential passwords to see which one is right by modifying the `level5.py` code as follows
```
import hashlib

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

flag_enc = open('level5.flag.txt.enc', 'rb').read()
correct_pw_hash = open('level5.hash.bin', 'rb').read()
#importing the filepath and creating a password array
filepath = "./dictionary.txt"
pw = []

#Iterating over the file, taking off all the line breaks, and appending the entries to the password array
with open(filepath, 'r') as file:
	for line in file:
		pw_string = line.replace("\n", "")
		pw.append(pw_string)

def hash_pw(pw_str):
    pw_bytes = bytearray()
    pw_bytes.extend(pw_str.encode())
    m = hashlib.md5()
    m.update(pw_bytes)
    return m.digest()

def level_5_pw_check():
#iterating over the password list and trying them all
	for i in range (0, len(pw)):
		user_pw = pw[i]
    	user_pw = input("Please enter correct password for flag: ")
    	user_pw_hash = hash_pw(user_pw)
    
    	if( user_pw_hash == correct_pw_hash ):
        	print("Welcome back... your flag, user:")
        	decryption = str_xor(flag_enc.decode(), user_pw)
        	print(decryption)
        	return
    	print("That password is incorrect")

level_5_pw_check()
```
This tries all the password entries from the dictionary until we find the right one which gives us the following flag:
Flag: `picoCTF{h45h_sl1ng1ng_fffcda23}`