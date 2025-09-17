---
layout: post
title: 'vim Editor: How to Add a Colored Theme'
date: 2015-02-14 09:59
comments: true
categories:
- vi
- misc
---
The vim editor can take advantage of color schemes to help emphasize specific elements of code.

![Vim Colors](/images/vimcolors.png)

### Adding a theme

First make sure you have vim installed and not just vi. You can test this by typing 'vim' at the command line and see if the editor loads.

Add the following to ~/.vimrc to make desert the default theme.

```
colorscheme desert
```

After restarting your shell (logout and login again again) your vim sessions will have the desert color scheme. Desert is the scheme that's pictured at the top of this article. Some other stock themes are:


* blue
* delek
* evening
* murphy
* slate
* darkblue
* desert
* koehler
* pablo
* ron
* torte
* default
* elflord
* morning
* peachpuff
* shine
* zellner

### Modifying a theme

If you want to change just a specific color in a theme you can do so by editing the theme itself. For instance if we want to change the comments in the desert theme to green we can do the following.

Edit /usr/share/vim/vim73/colors/desert.vim

Search and replace the following two lines so they now say green:

```
hi Comment  guifg=green
hi Comment  ctermfg=green
```

### Adding even more themes

Tired of the default themes? Need something more? Try some of these other themes:

http://www.vimninjas.com/2012/08/26/10-vim-color-schemes-you-need-to-own/

https://github.com/morhetz/gruvbox



### Other vim settings I use

Unrelated to color themes but related to vim settings. Here are the other settings in my ~/.vimrc file.

```
set tabstop=4
set shiftwidth=4
set smartindent
set backspace=indent,eol,start
```



