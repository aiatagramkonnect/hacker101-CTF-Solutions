Moderate 	TempImage 	Web 	0 / 2 	
Hints | Terminate 

From studying the site i can see it uses php, and you can upload pictures, uploaded pictures have to be png, i also see that pictures uploaded are encoded as a md5 hash. I have already done something similair on picoCTF and that was exploting the picture upload to do something on the php end so i think this is the same. SOmething simialir at least.

After having digged thrugh pictures and uploads and explored what happens i ended up using burpsuite to intercept the request and modify the request in the repeater
 tab where i changed the filename to ../../cat php which gave me the flag.... pretty easy. I feel like the picoctf task u had to do more but this is similair of course without having to change the file extension to php and etc and injecting php commands in that code/file/picture.

 FirstFlag.png

 after trying myself i was unsure what to do so i checked what other writeups did and they actually did kinda the same as picoCTF by uploading a png file and kind of turning it into a php file file not a php picture if that makes sense. SO we go ahead and try to get php web shell access by putting this in my php file: or at least in the burp suite request: <?php echo system($_GET['command']); ?> we also have to trick the upload by doing file traversal vulnerability: /../../test.php by doing this you can see your php file on the website having being uploaded correctly: 
 
 PHP.png

 now we do commands from the website/ aka web shell we have to write beside the php picture: ?command=ls then we get something similair to my picture below:

 PHPTest.png

 now that we see what files are in the server we cat to the diffrent files until we find the correct file that contains our flag, if that is the case unless we have to do something more.

 Index.png