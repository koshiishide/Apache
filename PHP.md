* PHP (バージョン5.6.40）をソースからインストールします。
    前回に引き続きPHPをソースからインストールします。途中、再インストール等していますので、一発でソースインストールしたい方は一度すべて目を通していただけますと幸いです。

    - PHPの依存関係にあるライブラリのインストール

        参考にしたURL:http://php.net/manual/en/install.unix.apache2.php  
        公式のURL:http://php.net

        - libxml2  
            XMLを解析・操作するC言語のライブラリです。

            公式のURL:http://www.xmlsoft.org/

            カレントディレクトリは/usr/local/srcです。  
            ソースファイルをダウンロードし、展開します。

            ```
            $ pwd
            /usr/local/src
            $ sudo wget ftp://xmlsoft.org/libxml2/libxml2-2.9.2.tar.gz
            ```

            展開します。

            ```
            $ sudo tar xzvf libxml2-2.9.2.tar.gz
            $ cd libxml2-2.9.2
            ```

            コンパイル、インストールします。

            ```
            $ cd libxml2-2.9.2
            $ sudo ./configure
            ```

            configureコマンドの結果、エラーが発生しました。
            
            ```
            libxml.c:3909: error: 'PyObject' undeclared (first use in this function)
            libxml.c:3909: error: 'module' undeclared (first use in this function)
            libxml.c:3915: warning: implicit declaration of function 'Py_InitModule'
            libxml.c:3915: warning: nested extern declaration of 'Py_InitModule'
            libxml.c:3915: error: 'libxmlMethods' undeclared (first use in this function)
            ```

            Pythonがインストールされていないことが原因で発生しているエラーのようです。  
            本課題に限りpythonのサポートが必要なさそうなので、configureコマンド実行時のオプション--with-pythonをnoにして実行します。

            ```
            $ sudo ./configure --with-python=no
            $ sudo make
            $ sudo make install
            ```
    - PHPインストール
           
        
        ソースファイルをダウンロードし、展開します。  
        カレントディレクトリは、/usr/local/srcです。

        ```
        $ pwd
        /usr/local/src
        $ sudo wget http://jp2.php.net/get/php-5.6.40.tar.gz/from/this/mirror
        ```
 
        展開します。

        ```
        $ sudo tar xzvf mirror
        $ cd php-5.6.40/

        ```

        PHPをインストールします。configureコマンド実行時、--with-apxs2オプションによってApacheのインストールディレクトリを指定します。  
        指定が無いとPHPをApacheで使用することができません。  
        また、-with-libxml-dirオプションによって依存ライブラリであるlibxmlのインストールディレクトリを指定ます。
　     
        ```
        $ sudo ./configure --with-apxs2=/usr/local/httpd/bin/apxs --with-libxml-dir=/opt/libxm12-2.9.2
        ```

        コンパイル、インストールします。
        
        ```
        $ sudo make
        $ sudo make test
        $ sudo make install
        ```

    - PHP再インストール  
        次の問題であるMySQLインストール完了後、再度PHPをインストールしました。  
        上記configureコマンドのオプションだと後にssl通信、wordpress,mysql等でPHPを利用できません。phpizeを用いてモジュールを追加しようとしたのですが、かなりつまってしまいましたので、PHPを再コンパイルし、再インストールします。

        - 中間ファイルとインストールフォルダ消去  
            カレントディレクトリは/usr/local/src/php-5.6.40です。

            ```
            $ sudo make clean
            $ sudo rm -r /usr/local/php
            ```
            
        - 必要であるライブラリ群のインストール

            まずphpモジュール用のディレクトリを作り、そこに関連するソースファイルをダウンロードします。
            カレントディレクトリは/usr/local/srcです。

            ```
            $ pwd
            /usr/local/src
            $ sudo mkdir phpmodule
            $ cd phpmodule
            ```
            
            phpmoduleディレクトリを作成しました。必要なプログラムをphpmodule下でダウンロードし、インストールします。

            - JPEG 
                JPEGのエンコード、デコードを行うためのライブラリです。
                
                URL:http://www.ijg.org/

                カレントディレクトリは、/usr/local/src/phpmoduleです。  
                同名のフォルダがないことを確認し、ソースファイルをダウンロード、展開します。

                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget http://www.ijg.org/files/jpegsrc.v9c.tar.gz
                ```

                展開します。
                ```
                $ sudo tar xvzf jpegsrc.v9c.tar.gz
                $ cd jpeg-9c/
                ```
                
                configureコマンドの後、makeコマンドよりコンパイル、make installコマンドよりインストールします。

                ```
                $ sudo ./configure
                $ sudo make
                $ sudo make install
                ```
           
            - FreeType

                フォントエンジンを実装したライブラリです。フォント関連の様々な操作をサポートします。
                
                URL:https://www.freetype.org/

                カレントディレクトリは、/usr/local/src/phpmoduleです。  
                同名のフォルダがないことを確認後、ソースファイルをダウンロードし、展開します。

                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget https://download.savannah.gnu.org/releases/freetype/freetype-2.9.tar.gz
                ```

                展開します。

                ```
                $ sudo tar xvzf freetype-2.9.tar.gz
                $ cd freetype-2.9
                ```

                configureコマンドの後、makeコマンドよりコンパイル、make installコマンドよりインストールします。
                
                ```
                $ sudo ./configure
                $ sudo make
                $ sudo make install
                ```

                phpから参照できるようにシンボリックリンクを作成します。

                ```
                $ sudo ln -s /usr/local/include/freetype2 /usr/local/include/freetype
                ```


            - libPNG

                画像データ形式のPNGエンコード・デコードを行うライブラリです。

                公式URL:http://www.libpng.org/pub/png/libpng.html

                カレントディレクトリは、/usr/local/src/phpmoduleです。  
                ソースファイルをダウンロード、展開します。

                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget --trust-server-names https://downloads.sourceforge.net/project/libpng/libpng16/1.6.34/libpng-1.6.34.tar.gz?r=http%3A%2F%2Fwww.libpng.org%2Fpub%2Fpng%2Flibpng.html&ts=1511065314&use_mirror=jaist

                ```

                展開します。
                
                ```
                $ sudo tar xvzf libpng-1.6.34.tar.gz
                ```
                展開されていることを確認します。
                ```
                $ cd libpng-1.6.34
                ```

                Makefileをscriptsディレクトリからリネームしてコピーします。

                ```
                $ sudo cp scripts/makefile.linux Makefile
                ```

                prefixを指定すると必要なライブラリのPathが見つからなくなるようなのでLDFLAGSによってライブラリのPathを指定します。
                
                ```
                $ sudo ./configure --prefix=/usr/local LDFLAGS="-L/usr/local/lib -lz"
                $ sudo make
                $ sudo make install
                ```

            
            - zlib  
                データの圧縮および伸張を行うためのライブラリです。

                公式URL:https://www.zlib.net/

                カレントディレクトリは、/usr/local/src/phpmoduleです。  
                同名のフォルダがないことを確認し、ソースファイルをダウンロード、展開します。

                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget http://zlib.net/zlib-1.2.11.tar.gz
                ```

                展開します。
                
                ```
                $ sudo tar xvzf zlib-1.2.11.tar.gz
                $ cd zlib-1.2.11
                ```
                
                configureコマンドの後、makeコマンドよりコンパイル、make installコマンドよりインストールします。
    
                ```
                $ sudo ./configure
                $ sudo make
                $ sudo make install
                ```


            - m4  
                autoconfの依存ライブラリです。

                公式URL:https://www.gnu.org/software/m4/

                カレントディレクトリは、/usr/local/src/phpmoduleです。  
                ソースファイルをダウンロード、展開します。

                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget http://ftp.gnu.org/gnu/m4/m4-1.4.18.tar.gz
                ```

                展開します。
                
                ```
                $ sudo tar xvzf m4-1.4.18.tar.gz
                $ cd m4-1.4.18
                ```

                カレントディレクトリを展開フォルダに移し、configureコマンドの後、makeコマンドよりコンパイル、make installコマンドよりインストールします。
                
                ```
                $ cd m4-1.4.18/
                $ sudo ./configure
                $ sudo make
                $ sudo make install
                ```


            - autoconf  
                configureスクリプトの自動生成ツールです。

                公式URL:https://www.gnu.org/software/automake/

                カレントディレクトリは、/usr/local/src/phpmoduleです。
                同名のフォルダがないことを確認後、ソースファイルをダウンロードし、展開します。
            
                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget http://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz
                $ find autoconf-2.69.tar.gz 
                autoconf-2.69.tar.gz
                ```
                展開します。
                ```
                $ sudo tar xvzf autoconf-2.69.tar.gz
                $ cd autoconf-2.69
                ```

                カレントディレクトリを展開フォルダに移し、configureコマンドの後、makeコマンドよりコンパイル、make installコマンドよりインストールします。

                ```
                $ cd autoconf-2.69/
                $ sudo ./configure
                $ sudo make
                $ sudo make install
                ```

            - libiconv  
                異なる文字コード間の相互変換を行う標準APIです。

                公式URL:https://www.gnu.org/software/libiconv/

                カレントディレクトリは、/usr/local/src/phpmoduleです。  
                ソースファイルをダウンロードし、展開します。
                
                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget https://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.15.tar.gz
                ```
                展開します。
                ```
                $ sudo tar xvzf libiconv-1.15.tar.gz
                $ cd libiconv-1.15
                ```
                
                カレントディレクトリを展開フォルダに移し、configureコマンドの後、makeコマンドよりコンパイル、make installコマンドよりインストールします。

                ```
                $ cd libiconv-1.15/
                $ sudo ./configure
                $ sudo make
                $ sudo make install
                ```

            - libGD  
                GDはイメージを動的に操作するライブラリです。

                公式URL:https://libgd.github.io/

                カレントディレクトリは、/usr/local/src/phpmoduleです。  
                ソースファイルをダウンロード、展開します。

                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget https://github.com/libgd/libgd/releases/download/gd-2.2.5/libgd-2.2.5.tar.gz
                ```
                
                展開します。

                ```
                $ sudo tar xvzf libgd-2.2.5.tar.gz
                ```
                展開されていることを確認します。
                ```
                $ cd libgd-2.2.5
                ```
                カレントディレクトリを展開フォルダに移し、Makefileをscriptsディレクトリからリネームしてコピーします。
                ```
                $ cd libgd-2.2.5/
                $ sudo cp scripts/makefile.linux Makefile
                ```
                configureコマンド実行時のオプション--withで、png、freetype、jpeg、xpm、libconvのインストールされているフォルダを指定します。
                その後、makeコマンドよりコンパイル、make installコマンドよりインストールします。
                ```
                $ sudo ./configure 
                        --with-png=/usr/local 
                    　　--with-freetype=/usr/local 
                        --with-jpeg=/usr/local 
                        --with-xpm=/usr 
                        --with-libiconv-prefix=/usr/local
                $ sudo make
                $ sudo make install
                ```

            - cURL

                さまざまなプロトコルを用いてデータを転送するライブラリとコマンドラインツールを提供します。

                公式URL:https://curl.haxx.se/

                カレントディレクトリは、/usr/local/src/phpmoduleです。  
                ソースファイルをダウンロード、展開し、カレントディレクトリを展開先フォルダへ移動させます。

                ```
                $ pwd
                /usr/local/src/phpmodule
                $ sudo wget https://curl.haxx.se/download/curl-7.58.0.tar.gz
                ```
                
                展開します。
                
                ```
                $ sudo tar xvzf curl-7.58.0.tar.gz
                ```
                LDFLAGSによってライブラリのPathを、CPPFLAGSによってプリプロセッサのPathを指定します。
                
                ```
                $ CPPFLAGS="-I/usr/local/ssl/include" LDFLAGS="-L/usr/local/ssl/lib"
                ```
                configureコマンド時のオプション --enable-http --enable-cookies によりhttpとcookieがサポートされるようにします。
                またzlib、ssl、libssh2、のインストールされているディレクトリを指定します。
                
                ```
                $ sudo ./configure 
                        --enable-http 
                        --enable-cookies 
                        --with-zlib=/usr/local 
                        --with-ssl=/usr/local/ssl 
                        --with-libssh2=/usr/local
                ```

                makeコマンドよりコンパイル、make installコマンドよりインストールします。
                ```
                $ sudo make
                $ sudo make install
                ```

        PHPのインストールにもどります。  
        カレントディレクトリは、/usr/local/src/php-5.6.40です。  
        configureコマンド実行時、オプション--with-apx2でApacheのインストールディレクトリ下のapxsディレクトリを指定し、PHPをApacheで使用できるように
        します。  
        日本語フォントを扱うためにオプション--enable-gd-jis-convをつけます。  
        gd,freetype,jpeg,zlib,libxml,xpm,curl,opensslのインストールディレクトリを指定します。  
        PHPでmysqlを使用できるように、オプション--with-mysql-sockよりソケットファイルのPathを指定します。
        また、後の課題にてmysqli関数やpdo関数使用するため、オプション--with-pdo-mysql　--with-mysqliオプションでmysqlのインストールディレクトリを指
        定します。


        ```
        $ sudo ./configure  
        --with-apxs2=/usr/local/httpd/bin/apxs
        --with-gd=/usr/local
        --with-freetype-dir=/usr/local/src/phpmodule/freetype-2.7
        --with-jpeg-dir=/usr/local/lib --with-png-dir=/usr/local
        --with-zlib-dir=/usr
        --with-libxml-dir=/usr/local
        --enable-gd-jis-conv
        --with-curl=/usr/local
        --with-openssl=/usr/local/ssl
        --with-xpm-dir=/usr
        --with-mysqli=/usr/local/mysql/bin/mysql_config
        --with-pdo-mysql=/usr/local/mysql
        --with-mysql-sock=/tmp/mysql.sock
        --with-iconv-dir=/usr/local
        ```
        makeコマンドよりコンパイル、make installコマンドよりインストールします。
        ```
        $ sudo make
        $ sudo make install
        ```
        PHPのモジュールを読み込めるよう設定がなされているかApacheの設定ファイルから確認します。
        ```
        $ pwd
        /usr/local/httpd/conf
        $ vim httpd.conf
        #一部抜粋、下記の表記があることを確認する。
        LoadModule php5_module        modules/libphp5.so
        ```
