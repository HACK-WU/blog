**1、在家目录下创建 .vimrc 文件**

2、我的配置

```
syntax on
 #显示行号
set nu      
#设置tab建为4个空格  
set ts=4      
set sw=4
#当backsapce不能删除时，可设置
set backspace=2  
#自动缩进
set autoindent   
set cindent
#显示标尺
set ruler       
#高亮搜索 
set hlsearch    
set expandtab
au BufReadPost * if line("'\"") > 0|if line("'\"") <= line("$")|exe("norm '\"")|else|exe "norm $"|endif|endif    #使光标自动回到上一次编辑的地方。
# 如果没生效，可能是当前用户无法读取 .viminfo文件导致的，要赋予r权限，或者直接chmod 777 .vimrc
set si            #自动添加注释ss
autocmd BufNewFile *.sh exec ":call SeTitle()"
func SeTitle()
        if expand("%:e")  == 'sh'
        call setline(1,"#!/bin/bash")
        call setline(2,"########################################")
        call setline(3,    '# Author:hackwu')
        call setline(4,    "\# time:"        .strftime("%c"))
        call setline(5,"# filename:"        .expand("%"))
        call setline(6,"# Script description:")
        call setline(7,"########################################")
endif
endfunc
```

```
" 语法高亮
set syntax=on
" 在处理未保存或只读文件的时候，弹出确认
set confirm
 
" 自动缩进
set autoindent
set cindent
" Tab键的宽度
set tabstop=4  #ts=4
 
" 统一缩进为4
set softtabstop=4  
set shiftwidth=4
   
" 使用空格替代tab
set expandtab
 
" 显示行号
set number
"禁止生成临时文件
set nobackup
set noswapfile
 
"搜索忽略大小写
set ignorecase
"搜索逐字符高亮
set hlsearch
set incsearch
 
" 我的状态行显示的内容（包括文件类型和解码）
"set statusline=%F%m%r%h%w\ [FORMAT=%{&ff}]\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")}
set statusline=[%F]%y%r%m%*%=[Line:%l/%L,Column:%c][%p%%]
set laststatus=2
 
" 在编辑过程中，在右下角显示光标位置的状态行
set ruler  
" 高亮显示匹配的括号
set showmatch
 
" show the TAB key and blank at the line tail
set list
set listchars=tab:>.,trail:.
```