### オレオレ証明書を用いてSSL通信を行う

参考にしたURL:https://httpd.apache.org/docs/2.4/ja/vhosts/name-based.html 
             https://httpd.apache.org/docs/2.4/ja/ssl/ssl_howto.html

SSLに必要な秘密鍵とサーバ証明書の作成を作成していきます。

まず秘密鍵を作成します。

```
$ mkdir sslkey
$ openssl genrsa > server.key
```

次にCSRファイルを作成します。CSRファイルは認証局にサーバ証明書を発行してもらう時に使用するファイルです。
今回は、オレオレ証明書なので、自分で署名します。

```
$ openssl req -new -key server.key > server.csr
```

最後にサーバ証明書を作成し,/usr/local/httpd/conf/に秘密鍵とサーバ証明書を配置します。

```
$ openssl x509 -req -signkey server.key < server.csr > server.crt 
$ sudo mv server.key /usr/local/httpd/conf/
$ sudo mv server.crt /usr/local/httpd/conf/
```



Apacheの設定をするためhttp.confを設定していきます。
sslのモジュールを読み込み、sslに必要な設定ファイルのパスを指定し、バーチャルホストを設定します。



以下バックアップとの差分になります。

```
$ diff -u httpd.conf20190130 httpd.conf
--- httpd.conf20190130	2019-01-30 10:20:30.082694629 +0900
+++ httpd.conf	2019-01-30 11:24:51.655686092 +0900
@@ -149,6 +149,8 @@
 LoadModule alias_module modules/mod_alias.so
 #LoadModule rewrite_module modules/mod_rewrite.so
 LoadModule php5_module        modules/libphp.so
+LoadModule ssl_module /usr/local/httpd/modules/mod_ssl.so
+LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
 <IfModule unixd_module>
 #
 # If you wish httpd to run as a different user or group, you must run
@@ -189,7 +191,7 @@
 #
 # If your host doesn't have a registered DNS name, enter its IP address here.
 #
-#ServerName test:80
+ServerName test:80

 #
 # Deny access to the entirety of your server's filesystem. You must
@@ -489,7 +491,7 @@
 </IfModule>

 # Secure (SSL/TLS) connections
-#Include conf/extra/httpd-ssl.conf
+Include conf/extra/httpd-ssl.conf
 #
 # Note: The following must must be present to support
 #       starting without SSL on platforms with no /dev/random equivalent
@@ -503,3 +505,24 @@
 <IfModule mod_php7.c>
   AddType application/x-httpd-php .php
 </IfModule>
+
+
+<VirtualHost *:443 *:80>
+  ServerName test
+  DocumentRoot "/usr/local/httpd/htdocs/"
+  DirectoryIndex index1.html
+  AddDefaultCharset UTF-8
+</VirtualHost>
+
+<VirtualHost *:80>
+  ServerName blog.test
+  DocumentRoot "/usr/local/httpd/htdocs/"
+  DirectoryIndex index2.html
+  AddDefaultCharset UTF-8
+</VirtualHost>
```

次にhtdocs下にバーチャルホストでアクセスされるファイルを作成します。

```
$ pwd
/usr/local/httpd/htdocs
$ cat index1.html
1
$ cat index2.html
2
```

最後にsslの設定ファイル 'httpd-ssl.conf'を設定していきます。以下バックアップとの差分になります。

```
$ pwd
/usr/local/httpd/conf/extra
$ diff -u httpd-ssl.conf.20190130 httpd-ssl.conf
--- httpd-ssl.conf.20190130	2019-01-29 16:51:05.999679627 +0900
+++ httpd-ssl.conf	2019-01-29 18:17:07.400671192 +0900
@@ -121,10 +121,16 @@

 #   General setup for the virtual host
 DocumentRoot "/usr/local/httpd/htdocs"
-ServerName test:443
+#ServerName test:443
 ErrorLog "/usr/local/httpd/logs/error_log"
 TransferLog "/usr/local/httpd/logs/access_log"

+
+ServerName test
+DirectoryIndex index1.html
+AddDefaultCharset UTF-8
+
+
 #   SSL Engine Switch:
 #   Enable/Disable SSL for this virtual host.
 SSLEngine on

```
