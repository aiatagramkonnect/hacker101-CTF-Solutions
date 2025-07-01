Here i first checked the website out and saw what it does. then i saw after i rpessed enter and sent a title and message the new url is the string but it is encrypted. And in the challenge we learn that it is encrypted with aes 128 bit and what would happen if we change/exploit that? then i headed over to burpsuite and tried the same thing but i entered a character d and then sent the request further. then i got my first flag:

^FLAG^1fd8fe727722f2c9baa7dfa876398ba8b76b1b7fe144c7473c6f159e60f4dabe$FLAG$

Then i went on to the second task or continued and found out you have to do something called  padding oracle attack explain this please ty. then i went on to research a little and stumbled across this github: https://github.com/eggburg/hacker101_CTF_Encrypted_Pastebin which helped me find the 3 flags which all were encrypted or whatevs in the oracle attack idk what this is
this github is essentially a script that eggburg made so creds to him for helping me solve this problem.