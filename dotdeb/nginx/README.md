## dotdeb.orgで配布しているnginxにCloudFlareのNGINX HTTP/2 + SPDY両対応するパッチを適用してリビルドする
===
dotdeb.org リポジトリの追加

```
vi /etc/apt/sources.list

deb http://packages.dotdeb.org jessie all
deb-src http://packages.dotdeb.org jessie all
```

パッケージリストの更新

```
wget https://www.dotdeb.org/dotdeb.gpg
apt-key add dotdeb.gpg
apt-get update
```

debパッケージのビルド環境構築とソースパッケージの取得

```
apt-get install build-essential
mkdir work
cd work
apt-get source nginx
apt-get build-dep nginx
```

CloudFlareのNGINX HTTP/2 + SPDY両対応するパッチを取得して適用

（公式のパッチだと上手くパッチが当たらないためコメント欄にあったパッチをダウンロード）

　https://blog.cloudflare.com/open-sourcing-our-nginx-http-2-spdy-code/
　

```
wget https://gist.githubusercontent.com/felixbuenemann/44d53b911ebfc2a4ff2b951e49923da8/raw/65fe22435b3b65a8e8cb03587e06160aec3d6f3c/nginx-1.9.15-spdy.patch
cd nginx-1.10.1/
patch -p1 < ../nginx-1.9.15-spdy.patch
```

debian/rulesを編集

```
vi debian/rules
```

差分:

```diff
--- debian/rules.org    2016-06-05 10:41:16.507872875 +0900
+++ debian/rules        2016-06-05 10:49:10.891403731 +0900
@@ -51,6 +51,7 @@
                        --with-http_realip_module \
                        --with-http_auth_request_module \
                        --with-http_v2_module \
+                        --with-http_spdy_module \
                        --with-http_dav_module \
                        --with-file-aio \
                        --with-threads
```

パッケージのビルド

```
dpkg-buildpackage -b -uc
```

出来上がったファイルリスト

```
libnginx-mod-http-auth-pam_1.10.1-1~dotdeb+8.2_amd64.deb
libnginx-mod-http-geoip_1.10.1-1~dotdeb+8.2_amd64.deb
libnginx-mod-http-image-filter_1.10.1-1~dotdeb+8.2_amd64.deb
libnginx-mod-http-lua_1.10.1-1~dotdeb+8.2_amd64.deb
libnginx-mod-http-ndk_1.10.1-1~dotdeb+8.2_amd64.deb
libnginx-mod-http-perl_1.10.1-1~dotdeb+8.2_amd64.deb
libnginx-mod-http-xslt-filter_1.10.1-1~dotdeb+8.2_amd64.deb
libnginx-mod-mail_1.10.1-1~dotdeb+8.2_amd64.deb
libnginx-mod-stream_1.10.1-1~dotdeb+8.2_amd64.deb
nginx-common_1.10.1-1~dotdeb+8.2_all.deb
nginx-doc_1.10.1-1~dotdeb+8.2_all.deb
nginx-extras-dbg_1.10.1-1~dotdeb+8.2_amd64.deb
nginx-extras_1.10.1-1~dotdeb+8.2_amd64.deb
nginx-full-dbg_1.10.1-1~dotdeb+8.2_amd64.deb
nginx-full_1.10.1-1~dotdeb+8.2_amd64.deb
nginx-light-dbg_1.10.1-1~dotdeb+8.2_amd64.deb
nginx-light_1.10.1-1~dotdeb+8.2_amd64.deb
nginx_1.10.1-1~dotdeb+8.2_all.deb
```


