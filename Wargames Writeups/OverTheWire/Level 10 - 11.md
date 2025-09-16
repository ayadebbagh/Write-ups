"The password for the next level is stored in the file **data.txt**, which contains base64 encoded data"

Knowing one of the commands that might be needed for this level is `base64`, and upon further research `base64 --decode` can be used to decode the password. I used `cat data.txt | base64 --decode`

Password: dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr