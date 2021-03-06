概要

Apery は USI プロトコルの将棋エンジンです。
USI エンジンに対応した GUI ソフトを使って下さい。
将棋所 以外で動作検証しておりませんので、将棋所 の使用を推奨します。
Apery は GNU General Public License version 3 またはそれ以降のバージョンのもとで配布されます。
主にチェスエンジンの Stockfish の実装を参考にしています。
将棋固有のデータ構造、評価関数等、Bonanza の実装を非常によく参考にしています。

ファイルの説明

・Readme.txt, このファイルです。
・Copying.txt, GNU General Public License version 3 条文です。
・src/, Apery のソースコードのフォルダです。
・utils/, Apery 開発で使用する本体以外のソフトのソースコードのフォルダです。
・bin/, 評価関数や定跡のバイナリのフォルダです。ファイルサイズが巨大なので、リポジトリ自体は分けています。


利用環境

メモリに最低でも 1.2GB 程度空きがあること。
64bit OS であること。


使い方

最初に、実行ファイルは apery/bin フォルダに置くことを想定しています。

将棋所での使い方のみを説明します。
将棋所を立ち上げます。

Windows の場合
Shogidokoro.exe をダブルクリックして下さい。
立ち上がらない場合は、.NET Framework が古い可能性が高いです。新しいものにして下さい。


Linux の場合
terminal を立ち上げ、mono Shogidokoro.exe とコマンドを打って下さい。
立ち上がらない場合、mono のバージョンが低いか、mono のライブラリが足りない可能性が高いです。
MonoDevelop 等をインストールすれば必要なライブラリは揃うと思います。
例として、Ubuntu の場合は sudo apt-get install monodevelop とコマンドを打つとインストール出来ると思います。
(最新の将棋所は Mono で動かない事もあるようです。古い将棋所を使うか、Mono を可能な限り最新にすることで動作するかも知れません。
 また、将棋所を使って人間が指した時など、駒音が鳴るときに将棋所が落ちる事があるようです。音が鳴る設定を切ってお使い下さい。)


将棋所のエンジン登録で Windows の場合は apery/bin/apery.exe (Linux の場合は apery/bin/apery) を登録して下さい。
登録に失敗するなら、Apery が正しく動作していない可能性があります。
その場合は、apery/bin/apery (Windows の場合は apery/bin/apery.exe) をダブルクリックして、usi とコマンドを打ってみて下さい。
usiok が表示されない場合は、ご利用の PC では Apery が動作しないようです。


将棋所に登録出来ましたら、後は将棋所の使い方を参照して下さい。


開発者向け注意点

Linux, Windows で g++-4.8 以上のバージョンで動作確認をしています。
Mac では clang++ で動作確認しています。(clang++ が OpenMP に対応していない場合は Makefile の -fopenmp を消して下さい。)
Visual Studio でも UTF8 BOM有り にすればほとんど修正無しでビルド出来ます。

評価関数の機械学習をするには
ifdef.hpp で LEARN を有効にして下さい。
apery をビルドして、
./apery l <学習用棋譜ファイル名(先後利用)> <学習用棋譜ファイル名(先手のみ利用)> <学習用棋譜ファイル名(後手のみ利用)> <使用する棋譜の数(0なら全て使うという意味)> <並列数(parse1)> <並列数(parse2)> <最低探索深さ> <最大探索深さ> <更新ステップ数> <1イテレーションに使う棋譜数> <最初のステップでの更新する最大値(64まで)> <最後のステップでの更新する最大値(64まで)> <L1正則化のペナルティを使用するかどうか(0または1)>
とコマンド入力すると、Bonanza Methodでの学習を開始します。
棋譜の形式はCSA1行形式です。詳しくは learner.hpp のコメントを参照して下さい。
棋譜はプロの棋譜60000棋譜程度、最低探索深さ 3 最大探索深さ 4 で学習する事を推奨します。

./apery make_teacher <対局開始局面集データ(ハフマン符号化したもの)> <出力教師データ> <スレッド数> <出力教師局面数>
とコマンドすると、強化学習用の教師データを生成します。

./apery_use_teacher <教師データ> <スレッド数>
とコマンドすると、強化学習を開始します。既存評価関数から学習する際には、
KPP_synthesized.bin, KKP_synthesized.bin, KK_synthesized.bin はそれぞれ
KPP_some_synthesized.bin, KKP_some_synthesized.bin, KK_some_synthesized.bin に名前を変更しておいて下さい。


定跡を生成するには
apery をビルドして、
./apery b <定跡用棋譜ファイル名>
とコマンド入力すると定跡生成を開始します。
棋譜の形式はCSA1行形式です。詳しくは book.cpp のコメントを参照して下さい。

定跡はfloodgate上位のソフトが上手く指した棋譜のみを抽出して生成する事が出来ます。
utils/onesidebook/oneside_filter.rb で floodgate の棋譜を抽出して CSA1行形式で出力しています。
