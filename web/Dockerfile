# 0. ubuntu:22.04をベースにする
FROM ubuntu:22.04

# 1. command_tutorialディレクトリをルートディレクトリに作成して
#    そのなかにfirstvi.txtを作成
RUN mkdir /command_tutorial
RUN touch /command_tutorial/firstvi.txt

# 2. viのインストール
RUN apt update
RUN apt -y install vim

# 3. ubuntuの日本語対応
RUN apt -y install language-pack-ja
RUN update-locale LANG=ja_JP.utf8
ENV LANG ja_JP.utf8

# 4. 日本時間（JST）の設定
RUN DEBIAN_FRONTEND=noninteractive apt -y install tzdata
RUN echo 'Asia/Tokyo' > /etc/timezone
ENV TZ Asia/Tokyo

# 5. Apache 2のインストール
RUN apt -y install apache2
RUN a2enmod rewrite
RUN a2enmod ssl
RUN a2ensite default-ssl
COPY index.html /var/www/html
RUN sed -i -e "s/^<\/VirtualHost>/RewriteEngine on/" /etc/apache2/sites-available/000-default.conf
RUN echo 'RewriteCond %{HTTPS} off' >> /etc/apache2/sites-available/000-default.conf
RUN echo 'RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L]' >> /etc/apache2/sites-available/000-default.conf
RUN echo '</VirtualHost>' >> /etc/apache2/sites-available/000-default.conf
EXPOSE 80
EXPOSE 443

# 6. JDKとSpring Bootアプリケーションのインストール
RUN apt -y install wget apt-transport-https gnupg
RUN wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | apt-key add -
RUN echo "deb https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
RUN apt update
RUN apt -y install temurin-17-jdk=17.0.5.0.0+8
RUN mkdir /var/www/java
COPY DailyReportSystem-0.0.1-SNAPSHOT.jar /var/www/java/
RUN a2enmod proxy
RUN a2enmod proxy_ajp
RUN echo "ProxyPass / ajp://localhost:8009/" >> /etc/apache2/apache2.conf

# 7. 一般ユーザmyuserの作成およびmyuserでログイン
RUN apt-get update
RUN apt -y install sudo
RUN useradd -s /bin/bash -G sudo -m myuser
RUN echo "myuser:mypass" | chpasswd
RUN echo "root:rootpass" | chpasswd

# 8. 起動時コマンドの指定
COPY startup.sh /startup.sh
RUN chmod 744 /startup.sh
CMD /startup.sh