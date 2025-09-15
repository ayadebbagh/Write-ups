"The password for the next level is stored in the file **data.txt** and is the only line of text that occurs only once"

I first started by doing `cat data.txt | uniq` which gave a lot of results. Upon further research, I realized that "uniq" only checks for consecutive duplicates and looks at each string's neighbours, not the entire file at once. To fix this, we can sort the file so all the same strings are consecutive `cat data.txt | sort | uniq`. Notice there are still too many results, this is because `uniq` alone doesn't delete duplicates, instead it collapses two duplicates into one string for examples
Input : a a a b b c
Uniq: a b c
However using uniq -u deletes the duplicates so inputing "a a a b b c" gives only "c". We now do `cat data.txt | sort | uniq -u` which gives the password

Password: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM