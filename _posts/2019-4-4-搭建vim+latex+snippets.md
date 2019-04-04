---
layout: post
title: 搭建vim+latex+snippets
author: husong
date: 2019-04-04
head-img:
catalog: true
tags:
  - 技术
---

### For MAC OSX

3. 安装`vimtex`插件
```
    Plugin 'lervag/vimtex', {'for': ['tex', 'plaintex', 'bst']}
```

4. 安装`basictex, tlmgr (tex live manager), latexmk` 编译latex文件
```
    brew cask install basictex
    export PATH=/usr/local/texlive/2017basic/bin/x86_64-darwin:$PATH

    sudo tlmgr update --self --repository http://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet
    sudo tlmgr install latexmk --repository http://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet
```

2. 安装`PDFviewer`
```
    brew tap zegervdv/zathura
    brew install zathura
    brew install zathura-pdf-poppler
```

1. 解决语言编码warning运行zathura 提示 `Gtk-WARNING **: Locale not supported by C library` 
```
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

2. 解决 打开 `zathura error: could not open plugin directory: /usr/local/Cellar/zathura/0.3.9/lib/zathura`
```
    // 设置环境变量
     $ mkdir -p $(brew --prefix zathura)/lib/zathura
    ln -s $(brew --prefix zathura-pdf-poppler)/libpdf-poppler.dylib $(brew --prefix zathura)/lib/zathura/libpdf-poppler.dylib
```

7. 安装`snippets`, 编辑 ~/.vimrc 
```
" better key bindings for UltiSnipsExpandTrigger
let g:UltiSnipsExpandTrigger="<c-e>"
let g:UltiSnipsJumpForwardTrigger="<c-j>"
let g:UltiSnipsJumpBackwardTrigger="<c-k>"
```


### For Linux 
    `todo`

### Reference

[https://github.com/zegervdv/homebrew-zathura](https://github.com/zegervdv/homebrew-zathura)

[https://castel.dev/post/lecture-notes-1/](https://castel.dev/post/lecture-notes-1/)

[https://www.ohadsoft.com/2016/02/gtk-warning-locale-not-supported-by-c-library/](https://www.ohadsoft.com/2016/02/gtk-warning-locale-not-supported-by-c-library/)

[https://github.com/zegervdv/homebrew-zathura](https://github.com/zegervdv/homebrew-zathura)

[https://www.ouyangsong.com/posts/57080/](https://www.ouyangsong.com/posts/57080/)

[https://www.pydanny.com/setting-up-latex-on-mac-os-x.html](https://www.pydanny.com/setting-up-latex-on-mac-os-x.html)

[http://www.readventurer.com/?p=782](http://www.readventurer.com/?p=782)

[https://mg.readthedocs.io/latexmk.html](https://mg.readthedocs.io/latexmk.html)
