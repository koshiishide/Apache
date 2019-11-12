* MySQL (バージョン5.6.X系）をソースからインストールする

    参考にしたURL:https://dev.mysql.com/doc/mysql-sourcebuild-excerpt/5.5/en/installing-source-distribution.html  
    公式URL:https://www.mysql.com/  
    ダウンロードURL:ftp://ftp.iij.ad.jp/pub/db/mysql/Downloads/MySQL-5.6/

    MySQLをダウンロードします。
    カレントディレクトリは/usr/local/srcです。  
    同名のフォルダがないことを確認後、ソースファイルをダウンロード、展開します。

    ```
    $ pwd
    /usr/local/src
    $ find mysql*
    find: `mysql*': そのようなファイルやディレクトリはありません
    $ sudo wget ftp://ftp.iij.ad.jp/pub/db/mysql/Downloads/MySQL-5.6/mysql-5.6.42.tar.gz
    ```
    ダウンロードされていることを確認します。
    ```
    $ find mysql-5.6.42.tar.gz 
    mysql-5.6.42.tar.gz
    ```
    展開します。
    ```
    $ sudo tar xvzf mysql-5.6.42.tar.gz
    ```
    展開されていることを確認します。
    ```
    $ cd mysql-5.6.42
    $ ls
    BUILD                    VERSION.dep           libmysqld         sql
    CMakeCache.txt           client                libservices       sql-bench
    CMakeFiles               cmake                 make_dist.cmake   sql-common
    CMakeLists.txt           cmake_install.cmake   man               storage
    COPYING                  cmd-line-utils        my-new.cnf        strings
    CPackConfig.cmake        config.h.cmake        my.cnf            support-files
    CPackSourceConfig.cmake  configure.cmake       mysql-test        tests
    CTestTestfile.cmake      dbug                  mysys             unittest
    Docs                     extra                 mysys_ssl         vio
    Doxyfile-perfschema      include               packaging         win
    INSTALL                  info_macros.cmake     plugin            zlib
    Makefile                 install_manifest.txt  regex
    README                   libevent              scripts
    VERSION                  libmysql              source_downloads
    ```
    カレントディレクトリを展開フォルダに移動します。
    ```
    $ cd mysql-5.6.42
    ```
    インストールしていきます。  
    configureコマンド実行しようとしましたが、該当コマンドがありませんでした。  
    MySQLにはconfigureがないため、ソフトウェアのビルドの自動化ツールであるcmakeが必要です。cmakeをインストールします。

    - Cmake
        
        公式URL:https://cmake.org/

        
        カレントディレクトリは/usr/local/srcです。

        同名のフォルダがないことを確認後、ソースファイルをダウンロード,展開します。

        ```
        $ pwd
        /usr/local/src
        $ find cmake*
        find: `cmake*': そのようなファイルやディレクトリはありません
        $ sudo wget https://github.com/Kitware/CMake/releases/download/v3.13.1/cmake-3.13.1.tar.gz
        ```
        ダウンロードされていることを確認します。
        ```
        $ find cmake-3.13.1.tar.gz 
        cmake-3.13.1.tar.gz
        ```
        展開します。
        ```
        $ sudo tar xvzf cmake-3.13.1.tar.gz
        ```
        展開されていることを確認します。
        ```
        $ cd cmake-3.13.2
        [hbadmin@AOPEN02-koshi cmake-3.13.2]$ ls
        Auxiliary                   CompileFlags.cmake  Templates
        CMakeCPack.cmake            Copyright.txt       Tests
        CMakeCPackOptions.cmake.in  DartConfig.cmake    Utilities
        CMakeGraphVizOptions.cmake  Help                bootstrap
        CMakeLists.txt              Licenses            cmake_uninstall.cmake.in
        CMakeLogo.gif               Modules             configure
        CONTRIBUTING.rst            Packaging           doxygen.config
        CTestConfig.cmake           README.rst
        CTestCustom.cmake.in        Source
        ```
        カレントディレクトリを展開フォルダに移動します。
        ```
        $ cd cmake-3.13.1
        ```
        
        コンパイル、インストールしていきます。

        ```
        $ sudo ./configure
        [sudo] password for hbadmin:
        Sorry, try again.
        [sudo] password for hbadmin:
        ---------------------------------------------
        CMake 3.13.1, Copyright 2000-2018 Kitware, Inc. and Contributors
        Found GNU toolchain
        C compiler on this system is: gcc       
        ---------------------------------------------
        Error when bootstrapping CMake:
        Cannot find a C++ compiler that supports both C++11 and the specified C++ flags.
        Please specify one using environment variable CXX.
        The C++ flags are "".
        They can be changed using the environment variable CXXFLAGS.
        See cmake_bootstrap.log for compilers attempted.
        ---------------------------------------------
        Log of errors: /usr/local/src/cmake-3.13.1/Bootstrap.cmk/cmake_bootstrap.log
        ---------------------------------------------
        ```
        configureコマンド実行時にエラーが発生しました。
        エラーを調べてみると、
        URL:https://stackoverflow.com/questions/47886400/cmake-configure-error-in-3-10-1-but-not-in-3-9-6

        >This requires a capable compiler. The first version of GCC which is C++11 feature complete is GCC 4.8.1.
        >Gcc 4.4.5 is over 7 years old and more important, it doesn't support C++11 (from the error):

        とありました。gccのバージョンを調べると、

        ```
        $ gcc -v
    
        gcc version 4.4.7 20120313 (Red Hat 4.4.7-23) (GCC)
        ```

        4.4.7で4.8.1以下でしたので、新しいバージョンのgccをインストールします。

        - gcc

            公式URL:https://gcc.gnu.org/

            ダウンロード、展開、ビルド用のディレクトリを作成し、インストールを行います。  
            カレントディレクトリは/usr/local/srcです。

           同名のフォルダがないことを確認後、 ソースファイルをダウンロード、展開しカレントディレクトリを展開先フォルダへ移動させます。

            ```
            $ pwd
            /usr/local/src
            $ find gcc*
            find: `gcc*': そのようなファイルやディレクトリはありません
            $ sudo wget http://ftp.tsukuba.wide.ad.jp/software/gcc/releases/gcc-8.2.0/gcc-8.2.0.tar.gz
            ```
            ダウンロードされていることを確認します。
            ```
            $ find gcc-8.2.0.tar.gz 
            gcc-8.2.0.tar.gz

            ```
            展開します。
            ```
            $ sudo tar xzvf gcc-8.2.0.tar.gz
            ```
            展開されていることを確認します。
            ```
            $ cd gcc-8.2.0
            $ ls
            ABOUT-NLS           config.guess       libbacktrace     ltgcc.m4
            COPYING             config.rpath       libcc1           ltmain.sh
            COPYING.LIB         config.sub         libcpp           lto-plugin
            COPYING.RUNTIME     configure          libdecnumber     ltoptions.m4
            COPYING3            configure.ac       libffi           ltsugar.m4
            COPYING3.LIB        contrib            libgcc           ltversion.m4
            ChangeLog           depcomp            libgfortran      lt~obsolete.m4
            ChangeLog.jit       fixincludes        libgo            maintainer-scripts
            ChangeLog.tree-ssa  gcc                libgomp          missing
            INSTALL             gmp                libhsail-rt      mkdep
            LAST_UPDATED        gmp-6.1.0          libiberty        mkinstalldirs
            MAINTAINERS         gmp-6.1.0.tar.bz2  libitm           move-if-change
            MD5SUMS             gnattools          libmpx           mpc
            Makefile.def        gotools            libobjc          mpc-1.0.3
            Makefile.in         include            liboffloadmic    mpc-1.0.3.tar.gz
            Makefile.tpl        install-sh         libquadmath      mpfr
            NEWS                intl               libsanitizer     mpfr-3.1.4
            README              isl                libssp           mpfr-3.1.4.tar.bz2
            build               isl-0.18           libstdc++-v3     symlink-tree
            compile             isl-0.18.tar.bz2   libtool-ldflags  ylwrap
            config              libada             libtool.m4       zlib
            config-ml.in        libatomic          libvtv
            ```

            展開フォルダ下のcontrib/download_prequisitesスクリプトはインストールに必要な必要なMPC,GMP(多倍長整数など任意の精度の算術ライブラリ)，MPFR(高品質多倍長浮動小数点演算ライブラリ)をダウンロードします。  
            実行します。

            ```
            $ sudo ./contrib/download_prerequisites
            ```
            ビルド用のディレクトリを作成しカレントディレクトリを移動します。

            ```
            $ sudo mkdir build
            $ cd build/
            ```
            コンパイル、インストールしていきます。
            configureコマンド実行時に--enable-languagesオプションよりサポート言語をc,c++とします。  
            また、--prefixオプションよりインストールディレクトリを設定します。

            ```
            $ sudo ../configure --enable-languages=c,c++ --prefix=/usr/local
            $ sudo make
            $ sudo make install
            $ gcc -v
            gcc バージョン 8.2.0 (GCC)
            ```

        cmakeのインストール作業を続けます。カレントディレクトリは/usr/local/src/cmake-3.13.1です。


        ```
        $ pwd
        /usr/local/src/cmake-3.13.1
        $ sudo ./configure
        [sudo] password for hbadmin:
        ---------------------------------------------
        CMake 3.13.1, Copyright 2000-2018 Kitware, Inc. and Contributors
        Found GNU toolchain
        C compiler on this system is: gcc       
        ---------------------------------------------
        Error when bootstrapping CMake:
        Cannot find a C++ compiler that supports both C++11 and the specified C++ flags.
        Please specify one using environment variable CXX.
        The C++ flags are "".
        They can be changed using the environment variable CXXFLAGS.
        See cmake_bootstrap.log for compilers attempted.
        ---------------------------------------------
        Log of errors: /usr/local/src/cmake-3.13.1/Bootstrap.cmk/cmake_bootstrap.log
        ---------------------------------------------
        ```
        同様のエラーが発生してしまいました。

        ```
        $ cc -v
        gcc version 4.4.7 20120313 (Red Hat 4.4.7-23) (GCC)
        ```
        推察ですが、まずccにはられているリンクが古いgcc(4系)をさしていたのでこのようなエラーが発生したと考えました。
        シンボリックリンクcc→/usr/local/bin/gccへのコマンドを実行します。

        ```
        $ sudo ln -nfs /usr/local/bin/gcc /usr/bin/cc
        $ gcc -v
        gcc バージョン 8.2.0 (GCC)
        $ cc -v
        gcc バージョン 8.2.0 (GCC)
        ```
        アップデートの確認がとれたのでconfigureコマンドを実行します。

        ```
        $ sudo ./configure
        [sudo] password for hbadmin:
        ---------------------------------------------
        CMake 3.13.1, Copyright 2000-2018 Kitware, Inc. and Contributors
        Found GNU toolchain
        C compiler on this system is: gcc       
        ---------------------------------------------
        Error when bootstrapping CMake:
        Cannot find a C++ compiler that supports both C++11 and the specified C++ flags.
        Please specify one using environment variable CXX.
        The C++ flags are "".
        They can be changed using the environment variable CXXFLAGS.
        See cmake_bootstrap.log for compilers attempted.
        ---------------------------------------------
        Log of errors: /usr/local/src/cmake-3.13.1/Bootstrap.cmk/cmake_bootstrap.log
        ---------------------------------------------
        
        ```
        失敗しました。同様のエラーでした。次の推察に移ります。  
        ディレクトリのパーミッションを確認します。
        ```
        $ ls -l |grep cmake
        drwxr-xr-x 15 root    root         4096 12月 24 17:55 2018 cmake-3.13.1
        -rw-r--r--  1 root    root      8614687 12月 13 22:50 2018 cmake-3.13.1.tar.gz

        ```
        その他のユーザが実行,書き込みできるよう/usr/local/src/cmake-3.13.1のフォルダの権限を変更し、sudoを使わずにconfigureコマンドを実行します。

        ```
        $ pwd
        /usr/local/src/cmake-3.13.1
        $ sudo chmod o=rwx ./
        $ ls -l |grep cmake
        drwxr-xrwx 15 root    root         4096 12月 24 17:55 2018 cmake-3.13.1
        -rw-r--r--  1 root    root      8614687 12月 13 22:50 2018 cmake-3.13.1.tar.gz
        $ ./configure
        ---------------------------------------------
        CMake 3.13.1, Copyright 2000-2018 Kitware, Inc. and Contributors
        Found GNU toolchain
        C compiler on this system is: gcc       
        C++ compiler on this system is: g++          
        Makefile processor on this system is: gmake
        g++ has setenv
        g++ has unsetenv
        g++ does not have environ in stdlib.h
        g++ has stl wstring
        g++ has <ext/stdio_filebuf.h>
        ---------------------------------------------
        $ sudo make
        ```
        成功しました。
        フォルダに実行権限がなかったのでsudoでconfigureコマンドを実行していましたが、コマンド実行時にsudoで実行すると環境変数envが一般ユーザでなくrootのもに変わってしまうことが失敗してしまうことが原因だと結論づけることができました。  
        解決策はchmodでディレクトリの所有者をhbadminにする、もしくはsudo時にhbadminの環境変数を引き継げるようにすることと考えました。
        今回はsudo時に環境変数を引き継げるように、/etc/sudoersをvisudoコマンドによって編集します。
        
        ```
        $ sudo visudo
        ```
        以下をコメントアウトしてenv_resetオプションを無効化します。
        これにより環境変数が最小限の設定にリセットされることを防ぎます。 
        
        ```
        #Defaults env_reset
        ```
        
        sudo時の環境変数を引き継がせるために以下を追記します。

        ```
        Defaults env_keep += "PATH"
        ```
        そして、以下をコメントアウトします。

        ```
        #Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin
        ```

        次に共有ライブラリのパスの追加をします。
        カレントディレクトリは/etc/ld.so.conf.dです。

        local-lib64.confというファイルを作成し、新たな共有ライブラリ/usr/local/lib64を追記しました。
        また、ldconfigコマンドによって更新の読み込みをします。

        ```
        $ cd /etc/ld.so.conf.d/
        [hbadmin@AOPEN02-koshi ld.so.conf.d]$ ls
        kernel-2.6.32-696.el6.x86_64.conf  local-lib64.conf
        [hbadmin@AOPEN02-koshi ld.so.conf.d]$ cat local-lib64.conf
        /usr/local/lib64
        [hbadmin@AOPEN02-koshi ld.so.conf.d]$ sudo ldconfig
        ```
        cmakeのインストールをします。一度、make cleanを実行し、中間ファイルとディレクトリを消去してからconfigureコマンド実行の後コンパイル、インストールします。
        カレントディレクトリは/usr/local/src/cmake-3.13.1です。

        ```
        $ sudo make clean
        $ sudo rm -r /usr/local/src/cmake-3.13.1
        $ sudo tar xvzf cmake-3.13.1.tar.gz
        $ cd cmake-3.13.1
        $ sudo ./configure
        $ sudo make
        $ sudo make install
        ```
        インストールできました。


    MySQLのインストールに戻ります。  
    カレントディレクトリは/usr/local/src/mysql-5.6.42です。  
    cmakeコマンドを実行します。  
    cmake時のオプションで-DCMAKE_INSTALL_PREFIXによってインストールフォルダを指定します。  
    -DDEFAULT_CHARSETオプションよりデフォルトの文字コードをutf8にします。  

    ```
    $ cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DDEFAULT_CHARSET=utf8
    Curses library not found.  Please install appropriate package,
    remove CMakeCache.txt and rerun cmake.On Debian/Ubuntu, package name is libncurses5-dev, on Redhat and derivates it is ncurses-devel.
    ```
    cmakeコマンドによりエラーが発生しました。  
    ncurses-develパッケージがインストールされていないとのエラーが発生したためncurses-develパッケージをインストールします。

    - ncurses
        

        公式URL:https://ftp.gnu.org/pub/gnu/ncurses/

        nucrsesは端末に依存しない形式でテキストユーザインタフェースを作成するためのAPIを提供するライブラリです。

        カレントディレクトリは/usr/local/srcです。
        同名のフォルダがないことを確認後、ダウンロード、展開しカレントディレクトリを展開先フォルダに移します。

        ```
        $ pwd
        /usr/local/src
        $ find ncurses*
        find: `ncurses*': そのようなファイルやディレクトリはありません
        $ sudo wget https://ftp.gnu.org/pub/gnu/ncurses/ncurses-6.1.tar.gz
        ```
        ダウンロードされていることを確認します。
        ```
        $ find ncurses-6.1.tar.gz 
        ncurses-6.1.tar.gz
        ```
        展開します。
        ```
        $ sudo tar xvzf ncurses-6.1.tar.gz
        ```
        カレントディレクトリを展開フォルダに移動します。
        ```
        $ cd ncurses-6.1
        ```

        コンパイル、インストールします。

        ```
        $ sudo ./configure
        $ sudo make
        $ sudo make install
        ```

    MySQLのインストールに戻ります。
    ```
    $ sudo cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DDEFAULT_CHARSET=utf8 
    $ sudo make
    $ sudo make install
    ```

    mysqlのグループとユーザを作成し、ディレクトリ/usr/local/mysql以下のすべてのディレクトリとファイルのユーザー所有権とグループ所有権を「mysql」に変更します。

    ```
    $ pwd
    /usr/local/mysql
    $ sudo groupadd mysql
    $ sudo useradd -r -g mysql mysql
    $ sudo chown -R mysql:mysql /usr/local/mysql
    ```
    
    MySQLのコマンドのPathが通るようにbash_profileに設定を加えます。
        
    ```
    $ pwd
    /home/hbadmin
    $ sudo vim .bash_profile 
    $ diff -u bash_profile.20190221 .bash_profile 
    --- bash_profile.20190221	2019-02-21 17:55:02.418704698 +0900
    +++ .bash_profile	2019-02-21 17:55:13.282702879 +0900
    @@ -7,6 +7,6 @@
    
    # User specific environment and startup programs
    
    -PATH=$PATH:$HOME/bin:/usr/local/ssl/bin
    +PATH=$PATH:$HOME/bin:/usr/local/ssl/bin:/usr/local/mysql/bin
    
    export PATH
    ```
    起動スクリプトをソースファイルの雛形からコピーし作成します。

    ```
    $ pwd
    /usr/local/mysql
    $ sudo cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
    ```
    データベースを初期化します。
    
    ```
    $sudo ./script/mysql_install_db --user=mysql
    ```
    プロセスを起動します。
    
    ```
    $sudo /etc/init.d/mysqld start
    [sudo] password for hbadmin: 
    Starting MySQL.. SUCCESS! 
    ```
    パスワードを変更します。

    ```
    mysql -u root
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 5
    Server version: 5.6.42 Source distribution

    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> SET PASSWORD FOR root@localhost=PASSWORD('XXXXXXXXXXXX');
    ```
    ログインできるか確かめます。

    ```
    $ mysql -u root -p
    Enter password: 
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 11
    Server version: 5.6.42 Source distribution

    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> 
    ```
