<!-- 
hugo new site <name>
\hugo\<name>
にサイトの雛形

テーマ
\hugo\<name>\themes\ 
でgit clone

\hugo\<name>
で
hugo server -t athena --bind=0.0.0.0 #--bind=0.0.0.0で別のホストからもアクセスok
http://localhost:1313 

\hugo\<naem>\public
に出力 -->



# dockerでの準備

 * 基本方針
 * 毎回コンテナ起動→設定→コンテナ終了+削除
 * でもマウントでファイルだけは更新していく感じ



## まずはサイトの初期作成

```sh
docker run --rm --name hugo -v %cd%:/home/ -it -p 1313:1313 japer/hugo hugo new site [site-name]
```

 * --name hugo
    * コンテナ名、`--rm`でコンテナ終了時に削除するのでなんでもよい
 * -v %cd%:/home/
    * ホストのカレントディレクトリ`%cd%`(windowsはこの表記)、とコンテナ側のhomeフォルダをリンク
    * コンテナ側の作業ディレクトリは毎回削除するのでなんでもよい
 * -p 1313:1313
    * ホストとコンテナのポートをリンク。hugoは1313がデフォ？
 * japer/hugo
    * image name
 * hugo new site [site-name]
    * [site-name]のサイトを新規作成

これによってコンテナのhomeフォルダに[site-name]フォルダ+その下にいろいろ設定ファイルができる。かつhomeとホストのカレントディレクトリがリンクしてるので、ホスト側にも[site-name]フォルダが作成される。

(先に`git init`してもいんだけど、サイトのフォルダに何か入っている状態で```hugo new site```をすると、フォルダが空じゃないよ、となってエラーになる。なので先にサイトのフォルダを作っておく）


## リポジトリ登録
[site-name]フォルダに移動
```sh
cd [site-name]
```
git init (とりあえず全部addでpush)
```sh
echo "first commit" >> README.md
git init
touch .gitignore
echo /public >> .gitignore
git add --all
git commit -m "first commit"
git remote add origin [url]
git push -u origin master
```
`.gitignore` に`/public`ファイル指定。Netliftyでホスティングする予定のため

## テーマファイルを入れる
```sh
cd themes
git submodule add https://github.com/halogenica/beautifulhugo.git beautifulhugo
```
urlはあくまで一例。gitが二重管理にならないよう`git submodule add`で入れる。
docker run でコンテナ側からgitで入れてもいいけど、やっていることは一緒だし結局マウントされてるのでホスト側で入れておｋ


config.tomlファイルを編集。以下を追加
```
theme = "[theme name]"
```
## 新規記事作成
サイトのフォルダへ移動しておく
```
cd [site-name]
```
run をしたときに新規記事追加
```
docker run --rm --name hugo -v %cd%:/home/ -it -p 1313:1313 japer/hugo  hugo new posts/filename.md
```
 * -v %cd%:/home/
    * 今の作業場所[site-name] `%cd%`、とコンテナ側のhomeフォルダをリンク
    * コンテナ側は直接home下に設定ファイルがいろいろ作成されるけど、フォルダの↕関係はあってるので問題ない
* hugo new posts/filename.md
    * hugo new posts/[filename]で名前をつけて新規記事の作成
    * content/posts/以下に記事のファイルが作られる
    * 違うディレクトリでhugo new postsをすると勝手にcontentが作られちゃうので注意

## 記事の編集
filename.md を編集。ファイル内の記述を
```md
draft: false
```
としないと実際には反映されないので注意。記事の編集もホスト側で編集してもおｋ


## 必要のない作業
Netliftyなのどのホスティングサイトと連携していれば、あとはgithubにコミットすることで自動的にビルドして反映してくれる。なので 以下は必須ではないけど、ローカルでサイトを確認したいときに行う作業
 * ビルドする(/publicにhtmlとか公開用のファイルが作られる)
 * サーバーを起動

build
```sh
docker run --rm --name hugo -v %cd%:/home/ -it -p 1313:1313 japer/hugo hugo
```
start server
```
docker run --rm --name hugo -v %cd%:/home/ -it -p 1313:1313 japer/hugo hugo server --bind=0.0.0.0
```

<!--  --workdir="/home/"  -->