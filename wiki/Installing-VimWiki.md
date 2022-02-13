#Installing VimWiki

These instructions apply to Vim 7.4.1528 and later.

First of all, clome the repo:

    git clone https://github.com/vimwiki/vimwiki.git ~/.vim/pack/plugins/start/vimwiki
    
    # to generate documentation i.e. ':h vimwiki'
    vim -c 'helptags ~/.vim/pack/plugins/start/vimwiki/doc' -c quit

Create (or append to) .vimrc:

    set tabstop=8 softtabstop=0 expandtab shiftwidth=4 smarttab
    
    set nocompatible
    filetype plugin on
    syntax on

    let wiki_0 = {}
    let wiki_0.path = '~/blog/'
    let wiki_0.syntax = 'markdown'
    let wiki_0.ext = '.md'

    let g:vimwiki_list = [wiki_0]
    let g:vimwiki_ext2syntax = {'.md': 'markdown', '.markdown': 'markdown', '.mdown': 'markdown'}
    let g:vimwiki_global_ext = 0


