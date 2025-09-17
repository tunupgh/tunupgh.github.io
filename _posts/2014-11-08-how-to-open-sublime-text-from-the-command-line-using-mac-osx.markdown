---
layout: post
title: How to open Sublime Text from the command line using Mac OSX
date: 2014-11-08 19:32
comments: true
categories:
- misc
- mac
- sublime text
---
To open Sublime Text from a command line or terminal in Mac OSX requires a small configuration change but is possible.

### Identify the executable

Try this command from the command prompt:

`/Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl`

It should open Sublime Text from the command line. This path might be slightly different on some installs like it may say `Sublime\ Text\ 2.app`. So if you get no such file then look around a little to find the right path.

### Create a symbolic link

From the command line confirm that `/usr/local/bin` is in your path by doing the following command:

`echo $PATH`

If it is there then create a symbolic link in that directory that points to the subl program. Here is the command to do this:

`sudo ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/subl`

This is the same path that we identified in step one. 

After that you should be able to simply type `subl` at a command line prompt and open the program.


### Using the command

To open a single file:<br>
`subl /path/to/filename.txt`

To open a whole directory:<br>
`subl .`

To see additonal command usage:<br>
`subl --help`

Additional command line usage can be seen here:<br>
<a href="http://www.sublimetext.com/docs/2/osx_command_line.html">subl command for ST2</a><br>
<a href="http://www.sublimetext.com/docs/3/osx_command_line.html">subl command for ST3</a>
