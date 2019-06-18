FROM ubuntu:latest

# \→改行したい箇所で、これを打ってから改行すると次の行にコマンドをかける
# A && B →Aが正常終了したらB

# apt-get update :パッケージのリストをサーバーから入手
# apt-get install :パッケージをインストール
#  --no-install-recommends はrecommendsされてるだけの余計なパッケージをインストールしない
# RUN apt-get updaate と apt-get install は常に同じ RUN 命令文で連結 推奨
# http://docs.docker.jp/engine/articles/dockerfile_best-practice.html

RUN apt update -y  \
    && apt install -y --no-install-recommends \
        sudo \
        python3 \
        python3-pip \
        wget \
        curl \
# apt-get clean キャッシュ・ファイルを削除する
    && apt clean \
    && rm -rf /var/lib/apt/lists/*

# よく使うpythonのライブラリインストール
RUN pip3 install \
    setuptools \
    flask \
    requests
    # jsonschema

# ワーク（作業）ディレクトリの指定。こうしておけばコンテナ起動時にはじめからhomeへ行ける？
# docker composeでworking_dir: /homeと指定したほうがよいかも
# WORKDIR /home/




# run command
# .でカレントディレクトリにあるDockerfileを指定して、-tオプションで名前
# docker build . --force-rm=true -t imagesname(japer/python)