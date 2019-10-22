# LAMP環境のソースインストール(Apache編2.4系)

* 概要

    - WEBサーバやミドルウェア、Linuxについて、学校では深く触る機会がないので、学習・復習のためソースインストールによってLAMP環境を設定していきます。expatやssl通信モジュールの追加など途中不足して再インストールしている部分があるため、一発でソースインストールを済ませたい方は、一度すべて読んでいただけると幸いです。
    

    1. 事前準備（前提) 

        gcc, make,やwgetコマンド等は入っている前提とします。


-  Apacheバージョン2.4.37をソースからインストールしていきます。

    参考にしたURL:https://httpd.apache.org/docs/2.4/en/install.html

    - Apacheのソースファイルをダウンロード  
        公式のURL:https://httpd.apache.org/

        ```
        $ pwd
        /usr/local/src
        $ sudo wget http://ftp.riken.jp/net/apache//httpd/httpd-2.4.37.tar.gz
        ```
        ダウンロードされていることの確認をします。  
        ※2019年1月21日に更新され、最新バージョンのリンクが2.4.38になっています。  
        そのため、現在の正しいURLは http://archive.apache.org/dist/httpd/httpd-2.4.37.tar.gz です。

        ```
        $ find httpd-2.4.37.tar.gz 
        httpd-2.4.37.tar.gz
        ```
        展開します。

        ```
        $ sudo tar xvzf httpd-2.4.37.tar.gz
        $ cd httpd-2.4.37
        ```

    - Apacheの依存関係にあるライブラリ群の確認
        インストールページを参考にすると、APR、APR-Util、PCREが必要と記載されているので、インストールしていきます。
        - APR  
            公式のURL:https://apr.apache.org/  
            ダウンロードサイト:https://apr.apache.org/download.cgi

            APR(Apache Portable Runtime)は、 Apache HTTP Serverのサポートライブラリです。OSとソフトウェアの間でOSなどの環境の違いを吸収するAPIを提供します。  
            ソースファイルのダウンロード、展開をします。 

            ```
            $ pwd
            /usr/local/src
            $ sudo wget http://ftp.tsukuba.wide.ad.jp/software/apache//apr/apr-1.6.5.tar.gz
            ```

            展開します。

            ```
            $ sudo tar xvfz apr-1.6.5.tar.gz
            $ cd apr-1.6.5
            ```

            APRの展開フォルダをApacheの展開フォルダへ移動させ、Apacheインストールのconfigureコマンド時aprと認識するようリネームします。
            
            ```
            $ sudo mv apr-1.6.5 httpd-2.4.37/srclib/apr
            ```

            ファイルの移動を確認します。

            ```
            $ cd /usr/local/src/httpd-2.4.37/srclib/
            $ find -name apr -type d
            ./apr
            ```
        
        - APR-Util

            公式のURL:https://apr.apache.org/  
            ダウンロードページ:https://apr.apache.org/download.cgi
            
            APR-UtillとはAPRの依存ライブラリです。

            ```
            $ pwd
            /usr/local/src
            $ sudo wget http://ftp.tsukuba.wide.ad.jp/software/apache//apr/apr-util-1.6.1.tar.gz
            $ sudo tar xvfz apr-util-1.6.1.tar.gz
            $ cd apr-util-1.6.1
            ```
            APR-Utilの展開フォルダをApacheの展開フォルダ下のsrclibディレクトリへ移動させ、Apacheインストールのconfigureコマンド時apr-utilと認識するようリネームします。
            
            ```
            $ sudo mv apr-util-1.6.1 httpd-2.4.37/srclib/apr-util
            $ cd /usr/local/src/httpd-2.4.37/srclib/
            $ find -name apr-util -type d
            ./apr-util
            ```

        - PCRE

            公式のURL:https://pcre.org/  
            参照URL:https://docs.oracle.com/cd/E51625_01/doc.1111/e49673/install_apache.htm

            PCREはPerl5互換の正規表現をC言語で実装したライブラリです。  
            カレントディレクトリは、/usr/local/srcです。  
            ソースファイルのダウンロード、展開をします。

            ```
            $ pwd
            /usr/local/src
            $ sudo wget https://ftp.pcre.org/pub/pcre/pcre-8.42.tar.gz
            ```
            展開します。

            ```
            $ sudo tar xvfz pcre-8.42.tar.gz
            $ cd pcre-8.42
            ```

            展開フォルダを/usr/local/src/httpd-2.4.37/srclib/pcreにリネームして移し、コンパイル、インストールしていきます。
            
            ```
            $ sudo mv pcre-8.42.tar httpd-2.4.37/srclib/pcre
            $ cd /usr/local/src/httpd-2.4.37/srclib/
            $ find -name pcre -type d
            ./pcre
            ```
            
            カレントディレクトリは、/usr/local/src/httpd-2.4.37/srclib/pcreです。
            まず、ソースファイル内にあるconfigureコマンドで、これからインストールを行うOSのCPUやバージョンを調べたり，必要な関連ツールを調査します。  
            本コマンドよりこれらの状況を記述したMakefileファイルが作成されます。

            ```
            $ pwd
            /usr/local/src/httpd-2.4.37/srclib/pcre
            $ sudo ./configure
            ```
            次に,makeコマンドによりMakefileを基にしてソースコードをコンパイルします。

            ```
            $ sudo make
            ```
            makeで生成されたバイナリファイルなどをmake　installコマンドによって、規定のディレクトリにコピーすることでインストールします。
            
            ``` 
            $ sudo make install
            ```

    - Apacheを関係ライブラリと共にインストール  
        カレントディレクトリは/usr/local/src/httpd-2.4.37です。  
        configureコマンドを実行し、Makefileを作成します。  
        その際、インストール先のディレクトリをオプション--prefixにより指定します。  
        次に、オプション--with-pcreによってpcre-configのディレクトリをオプションで指定します。  
        最後にAPRが/usr/local/src/httpd-2.4.37/srclib/下に配置されていることをオプション--with-included-aprで指定します。
        
        ```
        $ pwd
        /usr/local/src/httpd-2.4.37
        $ sudo ./configure --prefix=/usr/local/httpd --with-pcre=srclib/pcre/pcre-config --with-included-apr
        ```

        実行すると下記のエラーが発生しました。

        ```
        fatal error: expat.h: No such file or directory
        ```

        調べると、expatが無いというエラーのようなので、expatをインストールします。


        - expatインストール  
            expatとは・・・XMLパーサ（XML構文解析のプログラム）

            公式のURL:https://libexpat.github.io/  
            参考にしたURL:https://github.com/libexpat/libexpat
            

            同名のフォルダがないことを確認し、ソースファイルをダウンロード、展開します。  
            カレントディレクトリは/usr/local/srcです。

            ```
            $ pwd
            /usr/local/src
            $ sudo wget https://github.com/libexpat/libexpat/releases/download/R_2_2_6/expat-2.2.6.tar.bz2
            ```
            展開します。

            ```
            $ sudo tar xvzf expat-2.2.6.tar.bz2
            $ cd expat-2.2.6
            ```
            
            configureコマンドを実行後、makeコマンドよりコンパイル、make installコマンドよりインストールをします。

            ```
            $ sudo ./configure
            $ sudo make
            $ sudo make install
            ```
        
        Apacheのインストール作業に戻ります。configureコマンド実行後、コンパイル、インストールをします。  
        カレントディレクトリは、/usr/local/src/httpd-2.4.37です。

        ```
        $ sudo ./configure --prefix=/usr/local/httpd --with-pcre=srclib/pcre/pcre-config --with-included-apr
        $ sudo make
        $ sudo make install
        ```

        
        起動スクリプトを作成します。Apacheのソースフォルダから起動スクリプトを/etc/init.d/下にコピーします。

        ```
        $ sudo cp -ip /usr/local/src/httpd-2.4.37/build/rpm/httpd.init /etc/init.d/httpd
        ```
        
        このままでは動かないため、コピーしてきたスクリプトを本環境に合うように書き換えます。  
        まずバックアップをとります。
        
        ```
        $ sudo cp -a /etc/init.d/httpd /etc/init.d/httpd.`date +%Y%m%d`
        ```
        
        以下バックアップとの差分です。本環境に合わせてhttpdコマンドのPathは 
        `/usr/local/httpd/bin/httpd`、pidfileのPathは`/usr/local/httpd/logs/httpd.pid`としました。

        ```
        $ cd /etc/init.d/
        $ sudo vim httpd
        -httpd=${HTTPD-/usr/sbin/httpd}
        -pidfile=${PIDFILE-/var/run/${prog}.pid}
        +httpd=${HTTPD-/usr/local/httpd/bin/httpd}
        +pidfile=${PIDFILE-/usr/local/httpd/logs/httpd.pid}
        lockfile=${LOCKFILE-/var/lock/subsys/${prog}}
        RETVAL=0

        # check for 1.3 configuration
        check13 () {
        -	CONFFILE=/etc/httpd/conf/httpd.conf
        +	CONFFILE=/usr/local/httpd/conf/httpd.conf
        GONE="(ServerType|BindAddress|Port|AddModule|ClearModuleList|"
        GONE="${GONE}AgentLog|RefererLog|RefererIgnore|FancyIndexing|"
        GONE="${GONE}AccessConfig|ResourceConfig)"
        ```
        プロセスを起動させプロセスが立ち上がるか確認します。

        ```
        $ sudo /etc/init.d/httpd start
        $ ps aufxww |grep httpd
        ```

        起動しました。

    - Apache再インストール  
        opensslが必要な時は、（絶対必要・・・）gccでオプションをつけて最初にインストールしなければならないようです・・・。なので再インストールしていきます。

        参考にしたURL:https://httpd.apache.org/docs/2.4/ssl/#mod-ssl
        
        - 既にインストールされているApacheの消去  
            インストールされているディレクトリを消去、またmakeコマンドによって生成された中間ファイルをmake cleanコマンドにより消去します。  
            カレントディレクトリは/usr/local/src/httpd-2.4.37です。

            ```
            $ pwd
            /usr/local/src/httpd-2.4.37
            $ sudo make clean
            $ sudo rm -r /usr/local/httpd/
            ```


        - opensslインストール  
            opensslとはSSLプロトコル・TLSプロトコルを提供するソフトウェアです。

            公式のURL:https://www.openssl.org/
            
            カレントディレクトリは/usr/local/srcです。  
            同名のフォルダがないことを確認し、ソースファイルをダウンロード、展開します。

            ```
            $ pwd
            /usr/local/src
            $ sudo wget https://www.openssl.org/source/openssl-1.0.2q.tar.gz
            ```

            展開します。
            ```
            $ sudo tar xvzf openssl-1.0.2q.tar.gz
            ```
            展開されていることの確認をします。
            ```
            $ cd openssl-1.0.2q
            ```

            展開フォルダに移動し,コンパイル、インストールします。  
            configureコマンド実行時、インストールディレクトリをオプション-openssldirで指定します。
            
            ```
            $ cd openssl-1.0.2q
            $ sudo ./config --openssldir=/usr/local/ssl
            ```
            makeコマンドでコンパイル、make installコマンドでインストールします。
            ```
            $ sudo make
            $ sudo make install
            ```
            opensslのPathを通すのため、~/.bash_profileにコマンドパスを記載しました。
            以下バックアップとの差分になります。

            ```
            @@ -7,6 +7,6 @@
            
            # User specific environment and startup programs
            
            -PATH=$PATH:$HOME/bin
            +PATH=$PATH:$HOME/bin:/usr/local/ssl/bin
            
            export PATH

            ```
            sourceコマンドによりPathを更新させ、設定を反映させます。

            ```
            $ sudo source .bash_profile 
            $ openssl version
            OpenSSL 1.0.2q  20 Nov 2018
            ```

        Apacheのインストール作業に戻ります。  
        カレントディレクトリを、/usr/local/src/httpd-2.4.37に移します。  
        先ほどのApacheインストール時のconfigureコマンドに追加で、sslのインストールされているディレクトリをオプションで指定します。(--with-ssl=ディレクトリ)  
        また、sslの使用をオプションで明示します。でないと、ssl通信が使用できません。(--enable-ssl)  
        さらに、外部モジュール以外の追加作業でビルドしなおすのを避けるため、--enable-mods-sharedオプションでallを選択しました。  
        これによりopensslのような外部モジュールをのぞき、共有モジュールをの多くを再コンパイルすることなく使用することができます。

        ```
        $ pwd
        /usr/local/src/httpd-2.4.37
        $ sudo ./configure --prefix=/usr/local/httpd
                                            --with-pcre=srclib/pcre/pcre-config 
                                            --with-included-apr 
                                            --enable-mods-shared=all 
                                            --enable-ssl   
                                            --with-ssl=/usr/local/ssl/ 
                                            --enable-module=so
        $ sudo make
        $ sudo make install
        ```        
