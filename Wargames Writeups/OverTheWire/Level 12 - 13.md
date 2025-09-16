"The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work."

First, let's understand what a hex dump is:
A hex dump is a textual display of binary data (from a file, memory, or storage device) presented in hexadecimal, a base-16 number system.

![[Screenshot 2025-09-16 at 1.11.40 PM.png]]

After making a directory in `/tmp` and copying `data.txt`, we can use `xxd -r data2.txt` to reverse the hexdump 


Password: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn