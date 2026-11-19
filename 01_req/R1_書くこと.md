## 0. 概要
### 0.1 この手順書でできること
目標：一から FSP-RA4E2 の開発環境を構築すること＋開発できること<br>
### 0.2 必要な機材
※表で書く<br>
 - マイコン(FPB-RA4E2)<br>
 - USBケーブル<br>
参照：[RA4E2 FPB 編 手順書づくりの進め方](https://docs.google.com/presentation/d/1qx1VdwxyvKymWIX86opWBcbBQuqNNmiMYU0mebTjE9g/edit)(3ページ)
### 0.3 前提知識・事前準備
　システム開発を行う上でスケジュールや成果物を管理するために使用するツールがあることをいう<br>
 - Git/GitHub運用<br>
 - Redmine運用<br>
## 1. 開発環境構築
### 1.1 ツール・ドライバのインストール
 1. e2-studio(FSP 同梱版) のインストール手順<br>
 - Renesas 公式の GitHub からインストールさせる<br>
 - バージョンは 6.6.0 をインストールする<br>
※Git Hub：https://github.com/renesas/fsp/releases <br>
 2. Tera Term のインストール手順<br>
 - バージョンは 5.7.1 64bit Windows(119kって書いてあるやつ)をインストールする<br>
※Tera Term：https://github.com/TeraTermProject/teraterm/releases/tag/v5.7.1<br>
### 1.3 インストールの確認
 - インストール先のパスに入っていればOKの確認<br>
※Quickで普通にインストールした場合 C:\Users\username\AppData\Local\Programs\Renesas の中に RA というフォルダが入ってる<br>
## 2. シリアルターミナル
### 2.1 使用するツール
 - Tera Term を使う<br>
### 2.2 通信設定
①ハード接続<br>
③Tera Term設定<br>
### 2.3 動作確認
 - 各題材で動作確認をとりあげる<br>
## 3.プロジェクト作成
### 3.1 作成手順
 - プロジェクトの新規作成＋プロジェクトの初期設定をする
### 3.2 プロジェクト構造
 - ワークスペースがあること<br>
 - ワークスペース内の構造<br>
 - コンフィグレータの出し方、いじり方<br><br>
※下記リンクはビデオ教材<br>
 - コンフィグレータ：https://www.renesas.com/ja/software-tool/ra-smart-configurator?srsltid=AYCX9vIm6fpJa3-bdTDcNeqH7JYAfx-b8J5FGYEq0M-VzJGhnN-Xse87#videos_training<br>
 - プロジェクト構造：https://www.renesas.com/ja/video/exploring-fsp-folders-and-configuration-gui<br>
 