---
title: ISUCON11予選に参加して惨敗した
tags: ["isucon"]
date: 2021-08-22
published: true
---

## 結果

ISUCON 経験者の 2 名([@Aki_Mineo](https://twitter.com/Aki_Mineo), [@annkara](https://twitter.com/__annkara__))に誘われてチーム:コバミネとして参加しました。
私は主にインフラ担当でしたが、業務でバックエンドを Go で書いているためインフラの作業を終え次第、合流する作戦でした。

結果は [151 位 29614 点](https://isucon.net/archives/56021246.html) でした。

悔しい。あそこで気付いていれば...みたいなポイントがクリティカルでした。

## 反省点

直すところはたくさんあったと思いますが、`直す`と決断するまでに迷いがありました。

- 時間内でのアプリケーションへの理解が足りなかった。
  - ユーザストーリの理解と加点の仕組みが結びつかなかった。
    - アプリケーションのどこを直したらいいか。
    - どう直したら加点されるのか。
  - 非効率なポイントがあっても、他の要因ですぐに直せなそうだった。
  - 見つけた時には時間が足りなかった。
- 修正して試す環境が本番環境のみだった。
  - 大幅な修正をして、他メンバーとコンフリクトが起きるのが怖かった。

## ざっくりタイムライン

_前準備_

- private な作業リポジトリ(メンバーを招待)
  - Discord との webhook
- deploy.sh
- 各種 tool のインストールスクリプト

_10:00_

- 競技スタート。
- [@annkara](https://twitter.com/__annkara__)が AWS アカウントを準備してくれたので CFn でスタック作成。
- CFn でのインフラ作成完了。
- ssh config 配布。全員が接続確認完了。
- 初回ベンチ回す。`ベンチ:2981点`
- ソースや設定を GitHub へ。
- conditionLimit : 20 -> 40。`ベンチ:1704点`(すぐ Revert)
- alp, netdata, pt-query-digest をインストール。

_11:00_

- AP の負荷があまりにも高く、CPU100%に張り付いていたので DB を No.3 へ逃がす。`ベンチ:8267点`
- `POST /api/condition` を bulk insert へ。`ベンチ:9452点`
- nginx のログ出力を ltsv へ。
- mariaDB のスロークエリを出力。
- DB table `isu_condition`の`jia_isu_uuid`へ INDEX 追加。`ベンチ:22746点`
- DB table `isu`の`jia_isu_uuid`へ INDEX 追加。`ベンチ:22492点`

_12:00_

- この時点で 19 位。盛り上げる。
- 他 table へ INDEX 付与を試すもスコア改善されず。
- アプリ改善のために、ドキュメントを読み返す。

_13:00_

- `GET /api/trend`の isu への select をインメモリへ。 `ベンチ:17416点`
- DB を No.3 へ分けても web+ap サーバの負荷が高いので、 `/api/condition`のリクエストを No.2 へ逃がす。`ベンチ:23014点`
  (ここで、ユーザストーリーと加点の仕組みを合わせて理解出来ていないため、アプリの設定を色々いじる。)
- `POST /api/condition/:jia_isu_uuid`で、一定割合でリクエストを落とすという TODO があったので落とす割合をいじる(0.9 -> 0.5)。`ベンチ:16910点`

_14:00_
(ここでもまだどう修正したら加点されるのか理解出来ていない)

- nginx へキャッシュ追加。`ベンチ:23146点` (すぐ Revert)
- bulk insert の件数を 1000 件ずつへ。`ベンチ:22974点`
- リクエストのドロップ割合を 0.8 へ。`ベンチ:29462点`
- ユーザストーリと加点の仕組みが曖昧なため、予選当日マニュアルを再度読む。

_15:00_

- [キャッシュについて](https://gist.github.com/ockie1729/53589a0e8c979198b6231d8599153c70#%E3%82%AD%E3%83%A3%E3%83%83%E3%82%B7%E3%83%A5%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)から `GET /api/isu`にアプリケーションキャッシュを追加。`ベンチ:27688点`(のちに Revert)
- どうしたら加点されるのかわからないままソースとマニュアルを読む。
- N+1 が随所にあるからとりあえず結合しようとすると、table `isu`に画像データがぶち込まれていることに気付く。
- 画像を抜き出すスクリプトを作成。

_16:00_

- table `isu`の INDEX を試行錯誤する。 (<- 最終的にここのソースまで戻す。)
- 画像を`{jia_isu_uuid}+".jpg"`として配置。
- 画像を取ってくる/保存するように webapp を修正。
  - table `isu`のカラム`Image`を消すか迷う。go の構造体からは消してみる。
  - failed になったのでカラム`Image`はそのまま。
- ここまでで画像を抜き出す修正でベンチが通らない。

_17:00_

- 17:15 過ぎを目処に修正を止める。
- 17:20 からポータルが落ちてベンチも回せなかったので、これ以降の修正を諦める。
- DB から静的ファイルにする修正が一度もベンチ通らなかったので戻す。
- ログ停止&削除。
- netdata 停止。
- netdata 用に開けたポートを閉じる。(超ファインプレー!!!やってなければ 0 点になってた。)

_18:00_

- 振り返りと感想共有
- 18:45 ISUCON11 予選 Finish
- 18:57 [アプリケーションマニュアル](https://gist.github.com/ockie1729/fcb41ade9cb9d2166dd3f53e5179e8a9)の存在に気付く。

## 感想

ISUCON はアプリとインフラ両面で学びが多いイベントでした。周囲の人には気軽に参加して欲しいです。
実際に手を動かしますし、練習の環境をまともに 3 台構成で揃えるだけでも IaC や Linux、Netwrok、App の総合格闘技的な感じなので今まで参加したどのイベントよりも濃かったです。

振り返れば色々と悔いは出てきます。(INDEX 追加した際に DDL 見たんだから画像に気付けたのにとか、ユーザストーリと加点の仕組みを理解しないまま手短な設定を適当に修正したりとか、大きい修正が必要だとわかった時に悩んだことで時間をロスしたりとか...)

また、私は大学で情報系でもなかったですし、同じ業界で友達のような人がいません。
学びと共にエンジニアリングに対して、情熱を持つ 3 人が休日返上して遊べたという経験はとても嬉しかったです。
