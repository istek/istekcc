---
title: Auto Completing of gvim/vim for PHP
date: 2008-12-23T01:29:31+00:00
layout: post
categories:
  - Linux
tags:
  - vim
---
When I try to use vim to code, I found it’s lack of auto completing. Well, I got a solution for that. Please consolt the below information for tails.

Installation:

1. Download the function list file : <http://cvs.php.net/viewvc.cgi/phpdoc/funclist.txt>

2. Copy function list file to installation path of vim.

3. Add the following code to the end of vimrc:
```
    "You can obtain the completion dictionary file from:
    "http://cvs.php.net/viewvc.cgi/phpdoc/funclist.txt
    set dictionary-=$VIM/funclist.txt dictionary+=$VIM/funclist.txt

    "Use the dictionary completion
    set complete-=k complete+=k

    "Auto completion using the TAB key
    "This function determines, wether we are on
    "the start of the line text(then tab indents)
    "or if we want to try auto completion
    function! InsertTabWrapper()
    let col=col(‘.’)-1
    if !col || getline(‘.’)[col–1] !~ ‘\k’
    return “\<TAB>”
    else
    return “\<C-N>”
    endif
    endfunction

    "Remap the tab key to select action with InsertTabWrapper
    inoremap <TAB> <C-R>=InsertTabWrapper()<CR>
```

And pay attention on $VIM,that’s your vim’s config directory.