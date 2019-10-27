---
title: vim config on ubuntu
date: 2019-10-27
categories:
- Tech
- Methodology
tags:
- blog
- post
---

> vim config on ubuntu


## first

Install some requirement pacages.

- ctags
- vim-addon-manager
- cscope
- vim-scripts
- vim-doc
- taglist

Use `sudo apt install ctags vim-addon-manager cscope vim-scripts vim-doc`
to install system pacages.

Use `vim-addons install taglist`
to install vim addon.

## second

Edit .vimrc in home directory.

Here is some basic item.

```
if has("syntax")
	syntax on
endif

filetype on
filetype plugin on

set background=dark

if has("autocmd")
	au BufReadPost * if line("'\'")>1 && line("'\'") <= line("$") | exe normal !g'\'" | endif
	filetype plugin indent on
endif

set autoindent
set cindent

set smartindent
set tabstop=4
set softtabstop=4
set shiftwidth=4
set cinoptions={0,1s,t0,n-2,p2s,(03s,=.5s,>1s,=1s,:1s}
set showmatch
set whichwrap=b,s,<,>,[,]
set hlsearch
set nu
set cursorline
set ruler
set autowrite
set magic
```


## third

Restart all vim processes.


