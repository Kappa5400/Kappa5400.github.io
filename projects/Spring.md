---
layout: default
title: MERN App
permalink: /projects/Spring/
---

<h1>Spring Project</h1>

イントロダクション

簡単営業向けているERMアプリです。正社員が所有するPCや備品や車は更新・保守は必要になれば上司に自動メールを打つアプリになります。
以下はウェブサイトリンクになります。Digitaloceanのサーバで稼働しました。

https://itemhealtherm.tech/

このプロジェクトの目的はSpring-bootに慣れること営業使いそうなツールを作ることでした。
このアプリは自動Ｃｒｏｎで毎日自動スクリプト動きます。もしある物は更新や保守必要になれば自動メールを送ります。
メールはＭａｉｌｈｏｇでキャッチするので外部のメールアドレスに送りません。
アプリはＣＩ／ＣＤ稼働を使わっているし従業員の情報や持っているものとそのものの詳細はＥｘｃｅｌダウンロード可能もつけています。
単体テストや結合テスト、GithubActionsやDockerでCi/CDにDigitaloceanサーバで自動稼働を行っています。
データベースはMybatisを使われてなまSQLでメソッド作り出してServiceレイヤーに繋げてFlywaydbでデータ移行を行います。
Spring-securityやSpring-routingでバックエンドは完成作られました。
フロントエンドはThymeleaf,JS,Html,ＣＳＳのみに使って同じバックエンドとつながっているとアプリの体系完成になります。

データベース体系
<img src="{{ '/assets/css/img/ERM_ER_Chart.jpg' | relative_url }}" alt="DB Architecture" style="width: 100%; height: auto;"/>
<br/>

システム体系
<img src="{{ '/assets/css/img/ERM_Architecture_Diagram.jpg' | relative_url }}" alt="DB Architecture" style="width: 100%; height: auto;"/>

フロントエンドとバックエンドは同じポートで作って一つのアプリになります。わざわざ複雑フロントエンドを作るより簡単や作り立てやすいの
フロントエンドを目指して作りました。

使用パッケージ
セキュリティ: bcrypt, Spring-security
ユーティリティ: Cron, Spring-mail, Jackson, Mybatis, Flywaydb, mailhog
テスト: Spring-test, junit
フロントエンド: Spring-routing, Thymeleaf

パッケージ解説
セキュリティ
Spring-security: Spring と一緒つけているセキュリティ機能。Security filterを作り出していると認証のハンドリングに対応できます。
アプリでいくつな可能はBoss立場必須を作ってこの認証確認は必要になりました。

bcrypt: パスワードを暗号化（ハッシュ化）するためのパッケージ。

ユーティリティ
Cron:毎日同じとき自動スクリプトを稼働して欲しかったのでCron使いました。
Spring-mail:自動メールを送るためにこのSpring-mailを使いました。
Jackson: JSONオブジェクトの対応するためのパッケージになります。
Mybatis:SIER会社のスタックを合うためにMybatisを使おうとしました。生SQL探索をマッピングしてServiceとつながります。
Flywaydb:データベースの移行するためのパッケージです。
Mailhog: メールを送るたびにMailhogを止めるとフロントエンドに表示できるように設定しました。
Apache-poi: Excelファイルつくためにこのパッケージ使いました。

テスト
Spring-test: Springと付けているテストパッケージです。
Junit:単位テストパッケージです。
Mockito:　模擬パッケージです。

フロントエンド
Thymeleaf:　軽いフロントエンドフレームワーク。

ファイル構成
バックエンド

|ERM-Employee-Item-Health-App/ 
├──.devcontainer/ # Build settings for deployment. 
├──.github/workflows/ # Github actions. 
├──logs/ # Where logs end up. 
├──Dockerfile/ # Dockerfile build setup 
├──tests/ # Testing setup teardown files 
├──docker-compose.yml/ # Docker build settings 
|──Everything else in root: Standard spring-boot build files 
├──src/ # The source code. 
| └──test/ # Where the testing suite is, unit testing and integration testing. 
| └──main/ # | └────resources/ # Front end and db migration files 
| └────db/migrations/ # Flyway migration files for DB setup 
| └────mappers# MyBatis sql mapping files 
| └────static/js # JS script for frontend 
| └────templates # HTML pages
| └────application.yml/propertiex/logback # settings setup, logs 
| └────java/com/healthapp/itemhealth/ # Backend 
| └────config # Flyway config file.
| └────controller # Controller layer. 
| └────exception # Exception handler. 
| └────mapper # Connect Mybatis to backend service layer 
| └────model # Model layer 
| └────security # Spring-security filter chain. 
| └────service # service layer 
| └────health # health template logic for health service 
| └────scheduler # cronjob scripts 
| └────ItemhealthApplication.java # App entry point


バックエンド詳細

データベース
データベースはPostgresを使いました。SQLiteでもつかられるんですがとりあえずPostgresの訓練しようとおもって使いました。
ある程度テーブルのジョインしたり関係データベースの機能を真似しようやSIERスタックを合わせようとしました。

モデル
モデルはプライマリーキーと外部キーでジョインで詮索できるように作りました。Employeeプライマリーキー Employee＿ＩＤ でほかの
モデル外部キーとして付けていると物、Ｂｏｓｓ、そしてＳｕｂｏｒｄｉｎａｔｅでジョインできます。
Healthreportは報告するために物の情報が集めて自動メールを送れるように作りました。ER表も上に見られます。

Flywaydb
データ移行するためFlywaydbを使いました。データベースのスキーマを作り情報一貫性や正常に起動させます。

Mybatis
生SQLメソッド作られます。プライマリーキーと外部キーでジョインして複数のテーブルの情報を取られます。
主にCRUD機能メソッドを作っているとServiceレイヤーをつながります。

Service
主にMybatisCrud機能とロートをつながっています。自動スクリプト論理、自動メール機能、Excelエクスポート機能、
そして物Health確認自動機能も入っています。

Health service
従業員の持っている物の自動Health確認を行っています。申し物が更新及び保守は必要だっとしたら自動に上の方でメール送ります。
この自動物確認スクリプトはＢｏｓｓＤａｓｈｂｏａｒｄで開始もできます。

Ｃｒｏｎ　ｊｏｂ
スクリプトを毎日同じときに自動で開始するためＣｒｏｎを使いました。5日ごとに送っているメールを解除します。
ポートフォリオのために短い間のメール情報だけ表示しようと思って５日間のメール歴史は十分だと思いました。

Ｅｘｃｅｌ
従業員、上司、車、ＩＤＣａｒｄ、そしてノートパソコンの情報をExcelでエクスポートできます。
Apachi-poiライブラリを使ってこの機能を追加しました。

メール
Health確認スクリプトは更新・保守必要な物が見付ったら上司に自動メールを送れる論理です。
Spring-mailをつかって簡単お知らせメールを送ります。ほんとのメールアドレス送らないようにMailhogを使って、
Mailhogは送るメールを捕まって表示します。

ルーティング
ServiceとフロントエンドでつながっているAPIエンドポイントです。認証確認や記入確認はここします。
フロントエンドのルーティングもHealthController.javaで立ちます。

セキュリティ
Spring-securityでSecurity filterの認証を立ちます。ログイン論理、
リダイレクト論理、パスワードのBcryptでハッシュ処理などここに任せます。


フロントエンド詳細
Thymeleaf、HTML,CSS,そしてJSでフロントエンドを作りました。フロントエンドは主にCRUDフォームで従業員とノートパソコン、
Idcard,と車をいじられます。認証確認で上司立場のみのかたをCrudできると普通の従業員かたは見るだけ制度立ちました。
上司のかたはもExcelエクスポートやHealth checkスクリプト開始できます。

テスト、CI/CD、デプロイ
テスト
テストは単位テストと結合テストを両方作りました。デプロイするたびにテストを行ってパスしなければデプロイしません。
すべてのAPIエンドポイントとService機能をパスべきテストと落ちるべきテストを作りました。

CI/CDとデプロイ
Github　ActionsでCI/CDパイプラインを作りました。自動テスト、Dockerコンテナーを作って、サーバに開始する流れでCI/CD立ちました。
DockerSecrets作り立ってENV.変数の投入します。
ウェブサイトはDigitalOceanクラウドサーバーでデプロイして、NGINXやDockerコンテナーでデプロイして、
CloudflareやDigitaloceanファイアウオールで守備します。
