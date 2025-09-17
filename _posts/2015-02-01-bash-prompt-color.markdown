---
layout: post
title: Bash Prompt Color
date: 2015-02-01 12:50
comments: true
categories:
- linux
- misc
---
By using the `export PS1` command you can alter the way the bash prompt looks. You can do this right at the command line which will change it just for that session or you can place the config in your `.bashrc` which loads every time you login to your bash shell. To reload your `.bashrc` contents either start a new shell by doing `bash` or logout and login again.

```bash
txtblk='\e[0;30m' # Black - Regular
txtred='\e[0;31m' # Red
txtgrn='\e[0;32m' # Green
txtylw='\e[0;33m' # Yellow
txtblu='\e[0;34m' # Blue
txtpur='\e[0;35m' # Purple
txtcyn='\e[0;36m' # Cyan
txtwht='\e[0;37m' # White
bldblk='\e[1;30m' # Black - Bold
bldred='\e[1;31m' # Red
bldgrn='\e[1;32m' # Green
bldylw='\e[1;33m' # Yellow
bldblu='\e[1;34m' # Blue
bldpur='\e[1;35m' # Purple
bldcyn='\e[1;36m' # Cyan
bldwht='\e[1;37m' # White
unkblk='\e[4;30m' # Black - Underline
undred='\e[4;31m' # Red
undgrn='\e[4;32m' # Green
undylw='\e[4;33m' # Yellow
undblu='\e[4;34m' # Blue
undpur='\e[4;35m' # Purple
undcyn='\e[4;36m' # Cyan
undwht='\e[4;37m' # White
bakblk='\e[40m'   # Black - Background
bakred='\e[41m'   # Red
badgrn='\e[42m'   # Green
bakylw='\e[43m'   # Yellow
bakblu='\e[44m'   # Blue
bakpur='\e[45m'   # Purple
bakcyn='\e[46m'   # Cyan
bakwht='\e[47m'   # White
txtrst='\e[0m'    # Text Reset - Useful for avoiding color bleed

export PS1="\[$bldgrn\]\u@\h\w$ \[$txtrst\]"
```

The first part defines colors. This is optional but makes it easier to create a pretty prompt. The `export PS1` command at the end is what is actually changing the prompt. The backslash codes that can be used are:

* `\u` display the current username
* `\h` display the hostname of the system
* `\w` display the working directory
* `\d` display the date
* `\e` ASCII escape character
* `\t` the current time in 24-hour HH:MM:SS format
* `\T` the current time in 12-hour HH:MM:SS format
* `\@` the current time in 12-hour am/pm format
* `\A` the current time in 24-hour HH:MM format
* `\!` the history number of this command
* `\#` the command number of this command
* `\[` begin a sequence of non-printing characters, which could be used to embed a terminal control sequence into the prompt
* `\]` end a sequence of non-printing characters


### Examples

#### With color codes already defined 
These examples assume the color codes at the top of this page have been added to the `.bashrc` file.<br><br>
Single color:<br>
`export PS1="\[$bldgrn\]\u@\h\w$ \[$txtrst\]"`
<pre><code><span style="color: #00FF00;">user@localhost~/dir$ </span></code></pre>
<br>
Two Colors:<br>
`export PS1="\[$bldgrn\]\u\[$txtrst\]@\[$bldred\]\h\[$txtrst\\w$"`<br>
<pre><code><span style="color: #00FF00;">user</span>@<span style="color: #FF0000;">localhost</span>~/dir$</code></pre>
<br>
#### Without color codes defined
It's not necessary to add the color codes. These examples show how to use inline color codes to achieve the same results.

Single color:<br>
`export PS1="\e[1;32m\u@\h\w$ \e[0m"`
<pre><code><span style="color: #00FF00;">user@localhost~/dir$ </span></code></pre>
<br>
Two Colors:<br>
`export PS1="\e[1;32m\u\e[0m@\e[1;31m\h\e[0m\w$"`<br>
<pre><code><span style="color: #00FF00;">user</span>@<span style="color: #FF0000;">localhost</span>~/dir$</code></pre>


