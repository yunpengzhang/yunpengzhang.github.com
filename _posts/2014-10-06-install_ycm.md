---
layout: post
title: "安装YouCompleteMe插件"
description: ""
category: "工具使用"
tags: 
- vim
- youcompleteme
- vim插件
- clang

---
{% include JB/setup %}

假期折腾了下从前的vim插件，都变成从vundle下载，同时也去掉了一些过时的功能，
后面有时间再讲解下各种插件的用途。今天记录下安装youcompleteme的安装。

YouCompleteMe简称YCM，是一款vim的语法补全插件，对于c++和python支持的最好，
从前的补全插件大多是用tag然后字符匹配去猜。而YCM是使用clang的编译，从语法
角度去补全，所以补全能力大大提升。但是依赖clang的安装要3.3以上版本，下载后
还要重新编译才可以使用。


###安装vim 7.4版本
该插件对vim要求版本较高，直接安装7.4。
下载源代码

`wget ftp://ftp.vim.org/pub/vim/unix/vim-7.4.tar.bz2`

删除老版本

`sudo apt-get remove vim-tiny vim-common vim-gui-common`


编译


```
    cd vim
    ./configure --with-features=huge \
        --enable-multibyte \
        --enable-rubyinterp \
        --enable-pythoninterp \
        --with-python-config-dir=/usr/lib/python2.7/config \
        --enable-perlinterp \
        --enable-luainterp \
        --enable-gui=gtk2 --enable-cscope --prefix=/usr
    make VIMRUNTIMEDIR=/usr/share/vim/vim74
    sudo make install
```

###安装clang支持

先下载如下4个源码： llvm-3.3源码 clang-3.3源码 clang-tools-extra-3.3源码 compiler-rt-3.3源码 

```
http://llvm.org/releases/3.3/llvm-3.3.src.tar.gz
http://llvm.org/releases/3.3/compiler-rt-3.3.src.tar.gz
http://llvm.org/releases/3.3/clang-tools-extra-3.3.src.tar.gz
http://llvm.org/releases/3.3/cfe-3.3.src.tar.gz
```

建立目录：

```
mkdir ~/llvm-clang
```

分别解压上面下载的4个文件到上述目录

```
tar -xvzf llvm-3.3.src.tar.gz
tar -xvzf compiler-rt-3.3.src.tar.gz
tar -xvzf clang-tools-extra-3.3.src.tar.gz
tar -xvzf cfe-3.3.src.tar.gz
```

再将工具移动到llvm的相应目录下,使得clang，clang-tools-extra和compiler-rt就可以和llvm一起编译了：
```
mv cfe-3.3.src/ llvm-3.3.src/tools/clang/

mv clang-tools-extra-3.3.src/ llvm-3.3.src/tools/clang/extra/

mv compiler-rt-3.3.src/ llvm-3.3.src/projects/compiler-rt/
```


返回~/llvm-clang目录，并新建一个目录llvm-build专门用于编译llvm-clang，使得不污染源码。

```
mkdir llvm-build

cd llvm-build/

../llvm-3.3.src/configure --enable-optimized
```

建立编译环境

此种配置后，llv-clang默认安装到目录 /usr/local/ 下， 如果想改变安装目录，则加上配置： --prefix=Path 来制定 输入
```
make -j4
sudo make install
```

###安装配置YouCompleteMe
下载源码。使用vundle搞定，在vimrc文件中加入 
Bundle 'Valloric/YouCompleteMe'
执行命令：

```
    $ cd ~
    $ mkdir ~/ycm_build
    $ cd ~/ycm_build
    $ cmake -G "Unix Makefiles" -DUSE_SYSTEM_LIBclang=ON -DEXTERNAL_LIBCLANG_PATH=CLANG_INSTALL_PATH/libclang.so . ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/
```

注意:**CLANG_INSTALL_PATH**是编译clang生成的路径，要配置，否则是没有的。~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/的路径也和很多文档中的不一样，可能后来新版本变更了。
例如：
    `cmake -G "Unix Makefiles" -DUSE_SYSTEM_LIBclang=ON -DEXTERNAL_LIBCLANG_PATH=/usr/local/lib/libclang.so . ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/`


在YouCompleteMe中生成libclang.so和ycm_core.so文件 
执行 

```
make ycm_core
```

将在~/.vim/bundle/YouCompleteMe/python/目录下自动生成两个文件(libclang.so和ycm_core.so)//新版不是这个位置了
这还是不够的，还必须执行命令：

```
make ycm_support_libs
```


###参考
<http://www.linuxzen.com/vim-dai-ma-bu-quan-he-jian-cha-youcompleteme-syntastic.html>

<http://blog.csdn.net/linuxzbq/article/details/15505395>

<http://www.360doc.com/content/13/0322/13/4238731_273202893.shtml>

<https://github.com/adonis0147/vim-config>
