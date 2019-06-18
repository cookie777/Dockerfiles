

FROM ubuntu:latest
RUN apt update -y  \
    && apt install -y --no-install-recommends \
        wget \
        git \
# apt-get clean キャッシュ・ファイルを削除する
    && apt clean \
    && rm -rf /var/lib/apt/lists/*

# apt-get install hugo とたいけど何故かverが古いので
# githubからdebをとっていてインストールする
# export VER="0.53"
RUN wget --no-check-certificate https://github.com/gohugoio/hugo/releases/download/v0.55.6/hugo_0.55.6_Linux-64bit.deb  \
    && apt install ./hugo_0.55.6_Linux-64bit.deb \
    && rm ./hugo_0.55.6_Linux-64bit.deb

# インストール時にパスは通る。↓hugoの場所
# /usr/local/bin/hugo


# フォルダやファイル作成、移動は
# ①ここに記述
# ②docker-composeに記述
# ③runで指定する
# どれがいいんだろう

WORKDIR /home/



# run command
# .でカレントディレクトリにあるDockerfileを指定して、-tオプションで名前
# docker build . --force-rm=true -t imagesname(ex:japer/python)