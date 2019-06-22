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

echo /public>> .gitignore
git add --all
git commit -m "first commit"
git remote add origin [url]
git push -u origin master
```
`.gitignore` に`/public`ファイル指定。Netliftyでホスティングする予定のため

## テーマファイルを入れる

### 手順

テーマ一覧 https://themes.gohugo.io/ で好きなフォルダをthemesに入れる

重要なのはgitが二重管理にならないことなので
 * zip解凍でフォルダを直接入れる
 * git clone で入れて、.gitを削除
 * git submodule add を使う  (http://vdeep.net/git-submodule)

などがある。

### 設定
config.tomlファイルを編集。以下を追加
```
theme = "[theme name]"
```
どんな名前か＋さらに追加でどんな記述をtomlに行わないといけないか、テーマによって結構違ったりするのでreadmeを読んでくとgood

## 新規記事作成
サイトのフォルダへ移動しておく
```
cd [site-name]
```
run をしたときに新規記事追加
```
docker run --rm --name hugo -v %cd%:/home/ -it -p 1313:1313 japer/hugo hugo new posts/filename.md
```
 * -v %cd%:/home/
    * 今の作業場所[site-name] `%cd%`、とコンテナ側のhomeフォルダをリンク
    * コンテナ側は直接home下に設定ファイルがいろいろ作成されるけど、フォルダの上下関係はあってるので問題ない
* hugo new posts/filename.md
    * hugo new posts/[filename]で`posts`フォルダの中に名前をつけて新規記事の作成
    * content/posts/以下に記事のファイルが作られる
    * 名前はpostsじゃなくてもいいけど、再設定が必要？
    * 違うディレクトリでhugo new をすると勝手にcontentが作られちゃうので注意

## 記事の編集
filename.md を編集。ファイル内の記述を
```md
draft: false
```
としないと実際には反映されないので注意。build時に`--buildDrafts`を追加すれば下書きでも反映される。

## 必要のない作業
Netliftyなのどのホスティングサイトと連携していれば、あとはgithubにコミットすることで自動的にビルドして反映してくれる。なので 以下は必須ではないけど、ローカルでサイトを確認したいときに行う作業
 * ビルドする(/publicにhtmlとか公開用のファイルが作られる)
 * サーバーを起動


build ([site-name]フォルダで実行)
```sh
docker run --rm --name hugo -v %cd%:/home/ -it -p 1313:1313 japer/hugo hugo
```
start server
```
docker run --rm --name hugo -v %cd%:/home/ -it -p 1313:1313 japer/hugo hugo server --bind=0.0.0.0 --buildDrafts
```

option
 * `--bind=0.0.0.0`でホストからでもアクセスできるようになる
 * `--buildDrafts` はドラフト記事も表示
 <!-- *   --theme=hugo_theme_robust -->
 <!-- * `--watch` リアルタイムの反映を有効 -->
<!--  --workdir="/home/"  -->
 http://localhost:1313/ にアクセスしれば確認できる。ちなみに自分はレイアウト表示が全然demo通りにいかず沼。原因はブラウザでvivaldiだとうまくいかないみたい😭 chrome推奨
