+++
title = "2020年 ふりかえり"
date = 2020-12-29T21:02:32+09:00
tags = ["poem"]
draft = false
published = true
+++

2020年の振り返りと来年にやりたいことをつらつらと書こうと思う。
きっかけは、1on1をしてくれたEMからのアドバイスだ。

- 年末になると、1年を振り返るが過小評価しがち
- 何回かに分けてやると良い
- 謙遜せずやったことはやったと自分に言う

そんな趣旨のアドバイスを貰った。実際、いつも自分でやる振り返りでは、今年も結局...みたいな評価になってしまっている。
仕事を頑張っていないわけではない。今年は自分でも頑張ったと思う。そんな、いつもは謙虚な(?)な自分に今年は前向きな評価をしてあげよう。
そう思った。

- Dependency Injectionの実装方法を理解した
  - トランザクションマネージャ(DB参照先を捕捉し、rollbackを埋め込んであるmethodを持つinterface)を依存性に注入することでrollback記載不要に。
  - トップダウンで記述するDIでは、DB参照先もあらかじめ記述しておけるが、トランザクション範囲については自身で記載しなければ出来ないという結論に。
- ポインタレシーバにはポインタが、値レシーバには値がコピーされる。
  - メソッドをinterfaceに指定する際、レシーバにコピーされる値への変更を保持したいか等でポインタ/値レシーバどちらにするかの判断基準になる。
- WebFramework Echoのmiddlewareの作り方。
  - Skipperによるecho.Contextの使い回し方。(structメンバーに、echo.Contextを引数に持つ関数を持たせる)
  - Echoのデフォルトログでは、ヘッダーx-request-idに指定した値がrequest idに出る。
  - Echoのmiddlewareに対する理解が深まった。(雑)
- CloudFrontの各項目に対する理解が深まり、通信障害時にどのように調べて、どのメトリクスを見れば良いかがある程度わかるようになった。
  - CloudFrontのrequest idをoriginのEchoへ出力したい場合、x-amz-cf-idをEchoロガーで出力すればよい。
  - L@Eの使い方と開発/リリース時の注意点について教訓を得た。
    - L@Eへの反映には時間がかかるので、テストの仕方を工夫しなければいけない。
    - L@Eに適用するLambdaのversionはLATESTを指定出来ないので、リリース時にはLambdaのversionを数値指定しなければいけない。
- L@Eを修正するにあたり、Node.js v10.xを少し勉強した。(動的型付け言語は好きになれない。結局、undefinedと向き合わなければならず、動的型付けのメリットが感じられなかった。)
- その点、TypeScriptはちょっと触った感じ楽しかった。トランスパイルという言葉を知った。(言語のversionがどんどん上がっていくECMAScriptに対して、トランスパイルが追従していけるのかちょっと不安を感じた。)
- AWS StepFunctionは、Lambdaをjsonというインタフェースで繋げていて、Lambdaのフローをjsonで定義しているだけなんだと理解した。
- AWS Lambdaをローカルである程度動作確認出来るようにDEBUGフラグを付けた。
- Go言語の開発環境が自分の中で固まってきた。(Vimで必要なPluginがある程度固定されてきた)
- Alfredにより、unixtimestamp変換とbase64エンコード/デコードが素早く行えるようになった。
- CI/CD時にdocker pull rate limitによるエラーを、DockerHubへのログインで解消させた。
- コネクションプールの生存時間が短すぎることを見つけ、DBstatsからコネクションスパイクに対してプール生存時間を伸ばすことでDBへの負荷とコネクション接続時のlatencyを短くした。