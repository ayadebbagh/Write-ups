"The password for the next level is stored in **/etc/bandit_pass/bandit14 and can only be read by user bandit14**. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. **Note:** **localhost** is a hostname that refers to the machine you are working on"

When doing ls, the only file I see in a ssh private key file, and assuming form the commands that are said might be used for this level, we will have to ssh but use the key instead of a password to connect to bandit 14 and view the file located at `/etc/bandit_pass/bandit14` since it is only accessible by that user. We do `ssh -i sshkey.private -p 2220 bandit14@@bandit.labs.overthewire.org` which connect us to bandit 14, and we can then `cat /etc/bandit_pass/bandit14` to get the password 

**Password for level 14:** 
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS