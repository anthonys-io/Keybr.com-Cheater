#[Keybr.com-Cheater](http://anthonys.io/keybr-com-multiplayer-cheater/): Fun way to mess with your friends on multiplayer

Created by [@Tech](https://twitter.com/Tech) || [anthonys.io](http://anthonys.io)

![pic](http://anthonys.io/content/images/2016/11/keybr.png)

I recently found out about the website Keybr.com, which is used to improve your typing speed. I'm averaging around 105 wpm, but there is room for improvements. Keybr introduces a fun way to improve by playing against other real-time players around the world in a race. After playing for a few weeks, I got curious and wanted to know what their max speed was set to (If they even had a limit or not).

#The code:
I decided to go with Python as I wanted to keep it very short and simple. In keybr, the faster you type the given text, the faster the car moves to your end goal. 

I first checked out the source code to see if I can crawl it from python, the issue with this end up being: "div data-reactroot="", where that's hiding the game itself in regular code. Moving on lets start on top:

Here we got the start, the second line is one that took a few tries to get right. I ran into an issue where when I was pulling data from coming the whole DIV inner HTML code through inspect element on firefox, it was also copying text that was symbols, python doesn't work well with that without knowing so we added the second line to prevent Unicode errors.
```language-python
#!/usr/bin/python
# -*- coding: utf-8 -*-
``` 
Now lets clear up a few things going on: The first line is just instructions, we will be viewing the inspect element and copying (CTRL+C) that DIV class which will contain all inner code (We will strip it out later). The two other lines are just dependencies that are needed for this project.

==Beautiful Soup==- sits atop an HTML or XML parser, providing Pythonic idioms for iterating, searching, and modifying the parse tree.

==Requests==- Requests allows you to send organic, grass-fed HTTP/1.1 requests, without the need for manual labor. There's no need to manually add query strings to your URLs, or to form-encode your POST data. Keep-alive and HTTP connection pooling are 100% automatic, powered by urllib3, which is embedded within Requests.

==python-tk==- I end up using this to control the clipboard instances.

==language-pack-en==- Kept running into more unicode errors, this solved some of it.

```language-python
#Copy div class= "TextInput TextInput--sizeX0 TextInput--inactive"
#sudo pip install requests beautifulsoup4
#sudo apt-get install python-tk language-pack-en
``` 
Now were just going to import everything we need, this is self explanatory.
```language-python
import os;
import re;
import subprocess
import time
import Tkinter
from Tkinter import *
import sys;
``` 
Unicode fixing here, the reasons for all the unicode trouble is that the data that keybr is holding contains symbols, we will transfer those into regular text.
```language-python
reload(sys);
sys.setdefaultencoding("utf8")
``` 
After we copied the div innter html code, Tkinter would than get the clipboard data, move it to a varible, and save that copied data to the text file input.txt.. After it written the copied text, it will than copy '', which means nothing will hold in the clipboard anymore. The destory reference just closes the window .
```language-python
r = Tk()
r.clipboard_get()
copiedtext = r.clipboard_get()
text_file = open("input.txt", "w")
text_file.write(copiedtext)
r.clipboard_append('')
r.destroy()
text_file.close()
``` 
Now it's time to strip the code. When we get the code from firefox/chrome, all the letters that we need to type is cover with span classes. By this I mean if the word on keybr is ANT, it will appear like this:

**`<span class="TextInput-item ">A</span><wbr><span class="TextInput-item ">N</span><wbr><span class="TextInput-item ">T</span>`**

I made a loop that loads up the input.txt file and checks for all tags, and replace the strings with `blank`. Than it will save all that data to output.txt

The output.txt will look like: **`ANT`**

```language-python
replacements = {'<span class="TextInput-item TextInput-item--cursor">':'', '</span>':'', '<wbr>':'', '<span class="TextInput-item ">':'', '<span class="TextInput-item TextInput-item--special ">':'', '<div style="position: absolute; top: 0px; left: 0px; width: 0em; height: 1em; overflow: hidden;"><input style="display: block; margin: 0px; padding: 0px; width: 1em; height: 1em; font-size: 1em; line-height: 1em; border: medium none; outline: medium none;" type="text"></div><div class="TextInput-fragment">':'','<br>':'', '</div><div class="TextInput-label">Click to activate...</div>':'', '␣':' ', '↵':' ', '<div class="TextInput TextInput--sizeX0 TextInput--inactive">':''}
with open('input.txt') as infile, open('output.txt', 'w') as outfile:
    for line in infile:
        for src, target in replacements.iteritems():
            line = line.replace(src, target)
        outfile.write(line)
``` 

Now that we have a clear output.txt file with all the text we need to type, lets automate the process with xdotool.

==xdotool==- lets you simulate keyboard input and mouse activity, move and resize windows, etc. It does this using X11's XTEST extension and other Xlib functions.

First we are going to do a wait for 1 second, this wait time is for us to focus back on the Firefox window where it will be typing the text. The second line opens the output.txt file we just made. The subprocess is used to type out the text. The last line is used to see how fast we want to type the text, this is a fun variable to play with!

```language-python
time.sleep(1)
text = open('output.txt', 'r').read().strip()
for ch in text:
subprocess.call(["xdotool", "type", ch])
time.sleep(0.0001)
``` 
After it typed all the text to keybr, it's time to do some clean up. First two lines are used to overwrite the two files we made with zero bytes, and than we remove it with the last two lines.

```language-python
open('input.txt', 'w').close()
open('output.txt', 'w').close()
os.remove("input.txt")
os.remove("output.txt")
``` 
#Conclusion
Below is a video of the process that I recored, very simple to implement. Thank you for reading my fun adventures in python!
To view full blog entry, visit: [anthonys.io](http://anthonys.io)


[Video link to vid.me](https://vid.me/fg7E)
<iframe src="https://vid.me/e/fg7E?stats=1" width="854" height="480" frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen scrolling="no"></iframe>

#Update:
Made a few changes so it works on leaderboards.
![pic](http://anthonys.io/content/images/2016/11/lb.png)
