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
---
LaTeX（/ˈlɑːtɛx/，常被读作/ˈlɑːtɛk/或/ˈleɪtɛk/），排版时通常使用LATEX，是一种基于TeX的排版系统，由美国计算机科学家莱斯利·兰伯特在20世纪80年代初期开发，利用这种格式系统的处理，即使用户没有排版和程序设计的知识也可以充分发挥由TeX所提供的强大功能，不必一一亲自去设计或校对，能在几天，甚至几小时内生成很多具有书籍质量的印刷品。对于生成复杂表格和数学公式，这一点表现得尤为突出。因此它非常适用于生成高印刷质量的科技和数学、物理文档。这个系统同样适用于生成从简单的信件到完整书籍的所有其他种类的文档。

使用vim 来编辑latex文件，需要安装vim的latex插件- vimtex。markdown文件编译成latex文件需要编译工具支持，这里我选择latexmk。显示PDF文件使用zathura。

#### 安装
1. 安装`vimtex`插件
tx.vim: socket /tmp/fcitx-socket-/private/tmp/com.apple.launchd.zxwF4zFNm6/org.macosforge error: [Errno 2] No such file or directoryfcitx.vim: socket /tmp/fcitx-socket-/private/tmp/com.apple.launchd.zxwF4zFNm6/org.macosforge error: [Errno 2] No such file or directory
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

#### 动态同步latex

### For Linux 
---
    todo

### Reference
---
[https://github.com/zegervdv/homebrew-zathura](https://github.com/zegervdv/homebrew-zathura)

[https://castel.dev/post/lecture-notes-1/](https://castel.dev/post/lecture-notes-1/)

[https://www.ohadsoft.com/2016/02/gtk-warning-locale-not-supported-by-c-library/](https://www.ohadsoft.com/2016/02/gtk-warning-locale-not-supported-by-c-library/)

[https://github.com/zegervdv/homebrew-zathura](https://github.com/zegervdv/homebrew-zathura)

[https://www.ouyangsong.com/posts/57080/](https://www.ouyangsong.com/posts/57080/)

[https://www.pydanny.com/setting-up-latex-on-mac-os-x.html](https://www.pydanny.com/setting-up-latex-on-mac-os-x.html)

[http://www.readventurer.com/?p=782](http://www.readventurer.com/?p=782)

[https://mg.readthedocs.io/latexmk.html](https://mg.readthedocs.io/latexmk.html)
