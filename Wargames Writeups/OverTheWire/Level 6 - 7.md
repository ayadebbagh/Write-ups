"The password for the next level is stored **somewhere on the server** and has all of the following properties:

- owned by user bandit7
- owned by group bandit6
- 33 bytes in size"

My first attempt was `find . -user bandit7` which gave nothing since I was looking in the bandit6 directory instead of the entire server. Doing `find \ -user bandit7` gives a few results. Made it more specific by doing `find / -user bandit7 -group bandit6 -size 33c` which still gave a lot of results but with a lot of  "Permission denied" errors. Looking up how to filter that out we can add `2>/dev/null` ato the comme