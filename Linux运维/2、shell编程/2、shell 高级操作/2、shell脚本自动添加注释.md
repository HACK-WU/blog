**1、在家目录下创建 .vimrc 文件**

2、vim .vimrc

```

set si
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