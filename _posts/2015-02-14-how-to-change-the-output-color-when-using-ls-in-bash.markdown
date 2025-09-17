---
layout: post
title: How to change the output color when using ls in bash
date: 2015-02-14 16:34
comments: true
categories:
- linux
- bash
- misc
---
Sometimes when using Linux in bash or zsh you may have a hard time seeing directories. If you have ANSI colors turned on and your terminal background color is black seeing the directories is hard. That is because the directory color is dark blue by default. The fix to this is to change the dircolors which uses an environmental variable `$LS_COLORS`.

### Change the dir color

The fix is as simple as doing this:

```
LS_COLORS="$LS_COLORS:di=01;33"
```

This sets the color to bold yellow.

##### Make it a default setting.

If you want this to be there the next time you login just add it to your shell rc file like `.bashrc` or `.zshrc`. You can do that one of two ways.

Either append the line at the end of your file like: <br>
`echo 'LS_COLORS="$LS_COLORS:di=01;33"' >> .bashrc`

Or add the line in manually by using a text editor like `nano` or `vi` and add the line `LS_COLORS="$LS_COLORS:di=01;33"` to the bottom of the file.

Once the line is added to the file then restart the shell by logging out and logging in again and your new colors should be there.

### Change other file type colors

Try using the command `dircolors` to see your current settings.

You can also use the command `dircolors -p` to see the default settings. 


### Other color options

This webpage goes over [bash colors](http://misc.flogisoft.com/bash/tip_colors_and_formatting) in depth. You can replace the `00;93` in the example above with another color of your choice.




