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

# 5. 一般ユーザmyuserの作成およびmyuserでログイン
RUN apt -y install sudo
RUN useradd -s /bin/bash -G sudo -m myuser
RUN echo "myuser:mypass" | chpasswd
RUN echo "root:rootpass" | chpasswd

# 6. SSHサーバのインストールと起動
RUN apt-get update
RUN apt -y install openssh-server
RUN mkdir /home/myuser/.ssh
COPY id_rsa.pub /home/myuser/.ssh
RUN mv /home/myuser/.ssh/id_rsa.pub /home/myuser/.ssh/authorized_keys
RUN chown -R myuser:myuser /home/myuser/.ssh
RUN chmod 700 /home/myuser/.ssh
RUN chmod 600 /home/myuser/.ssh/authorized_keys
EXPOSE 22
CMD service ssh start && /bin/bash