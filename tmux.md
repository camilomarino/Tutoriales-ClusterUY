# [TMUX](https://github.com/tmux/tmux/wiki)

TMUX es un multiplexor de terminal, por más detalle ir a su página web.

### Instalación
Los siguientes comandos instalan la versión 3.1 de TMUX sin necesidad de permisos de superusuario. El binario de TMUX se ubicará en ~/local/bin/tmux.
```bash
wget https://iie.fing.edu.uy/~cmarino/cluster/install_tmux.sh
./install_tmux.sh
rm install_tmux.sh
```
Las lineas anteriores corren el script install_tmux.sh, el cual contiene:
```bash
#!/bin/bash

# Script for installing tmux on systems where you don't have root access.
# tmux will be installed in $HOME/local/bin.
# It's assumed that wget and a C/C++ compiler are installed.

# exit on error
set -e

TMUX_VERSION=3.1

# create our directories
mkdir -p $HOME/local $HOME/tmux_tmp
cd $HOME/tmux_tmp

# download source files for tmux, libevent, and ncurses
wget -O tmux-${TMUX_VERSION}.tar.gz https://github.com/tmux/tmux/archive/${TMUX_VERSION}.tar.gz 
wget https://github.com/downloads/libevent/libevent/libevent-2.0.19-stable.tar.gz
wget ftp://ftp.gnu.org/gnu/ncurses/ncurses-5.9.tar.gz

# extract files, configure, and compile

############
# libevent #
############
tar xvzf libevent-2.0.19-stable.tar.gz
cd libevent-2.0.19-stable
./configure --prefix=$HOME/local --disable-shared
make -j
make install
cd ..

############
# ncurses  #
############
tar xvzf ncurses-5.9.tar.gz
cd ncurses-5.9
./configure --prefix=$HOME/local
make -j
make install
cd ..

############
# tmux     #
############
tar xvzf tmux-${TMUX_VERSION}.tar.gz
cd tmux-${TMUX_VERSION}
./autogen.sh
./configure CFLAGS="-I$HOME/local/include -I$HOME/local/include/ncurses" LDFLAGS="-L$HOME/local/lib -L$HOME/local/include/ncurses -L$HOME/local/include"
CPPFLAGS="-I$HOME/local/include -I$HOME/local/include/ncurses" LDFLAGS="-static -L$HOME/local/include -L$HOME/local/include/ncurses -L$HOME/local/lib" make
cp tmux $HOME/local/bin
cd ..

# cleanup
rm -rf $HOME/tmux_tmp

echo "$HOME/local/bin/tmux is now available. You can optionally add $HOME/local/bin to your PATH."
```

Este script es extraído de [https://zijishi.xyz/post/linux/installing-tmux-without-root-access/](https://zijishi.xyz/post/linux/installing-tmux-without-root-access/), al cual se le actualizó la versión de tmux.