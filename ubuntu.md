# Ubuntu

## Install Tomcat

### User 생성
먼저 tomcat 전용 유저를 생성.
```
$ sudo useradd -M tomcat
```

### Apache Tomcat 다운로드

http://tomcat.apache.org/download-90.cgi

```
$ cd ~
$ curl -O http://ftp.meisei-u.ac.jp/mirror/apache/dist/tomcat/tomcat-9/v9.0.5/bin/apache-tomcat-9.0.5.tar.gz
$ tar -xzvf ~/apache-tomcat-9.0.5.tar.gz
$ sudo mv ~/apache-tomcat-9.0.5 /opt
$ sudo chown -R tomcat:tomcat /opt/apache-tomcat-9.0.5
```

### サービスの作成と登録
Ubuntu 16.04 LTS ではサービスを systemd が管理するようになっています。 ここでは Apache Tomcat 9 をサービスとして登録します。 まずは /etc/systemd/system/tomcat.service を新たに作成して、つぎのように記述して保存します。 これはサービスの定義ファイルです。

```
[Unit]
Description=Apache Tomcat 9
After=network.target

[Service]
User=tomcat
Group=tomcat
Type=oneshot
PIDFile=/opt/apache-tomcat-9.0.5/tomcat.pid
RemainAfterExit=yes

ExecStart=/opt/apache-tomcat-9.0.5/bin/startup.sh
ExecStop=/opt/apache-tomcat-9.0.5/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

```
$ sudo chmod 755 /etc/systemd/system/tomcat.service
$ sudo systemctl enable tomcat
Created symlink from /etc/systemd/system/multi-user.target.wants/tomcat.service to /etc/systemd/system/tomcat.service.
```

### Apache Tomcatの起動と停止

```
$ sudo systemctl start tomcat
$ sudo systemctl stop tomcat
```
혹은
```
$ sudo service tomcat start
$ sudo service tomcat stop
$ sudo service tomcat restart
$ sudo service tomcat status
```

### Apache 웹서버와 Tomcat 연동

```
$ sudo a2enmod proxy
$ sudo a2enmod proxy_ajp
```

ProxyPass httpdでのパス ajp://サーバー名:8009/Tomcat上のパス/

```/etc/apache2/sites-available/[SiteName]```

```
<VirtualHost *:80>
        ServerName xxx.com # ドメイン
        ServerAdmin admin@xxx.com # 管理者のメール

        <Location />
           ProxyPass ajp://localhost:8009/
           Order allow,deny
           Allow from all
        </Location>

        ErrorLog /var/log/apache2/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog /var/log/apache2/access.log combined
</VirtualHost>
```

サイトを有効にしてApacheの再起動。
```
$ sudo a2ensite [SiteName]
$ sudo /etc/init.d/apache2 restart
```

## Command Line / Shell
### sudo cd
「''sudo cd ...''」 명령은 에러가 난다. 관리자 권한으로 cd를 실행하려면,

```
$ sudo sh -c "cd ...; 다음에 실행할 명령"
```

실제로 경로가 이동되지는 않고, 별도로 관리자 권한 쉘을 실행시켜서 명령을 한번에 전달하는 방식.
