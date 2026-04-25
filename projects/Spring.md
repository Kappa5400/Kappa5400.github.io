---
layout: default
title: Spring App
permalink: /projects/Spring/
---

<div id="main-content"></div>

# Spring ERM アプリ 概要

##  リンク:

[https://itemhealtherm.tech/](https://itemhealtherm.tech/)

## Githubリンク:

[https://github.com/Kappa5400/ERM-Employee-Item-Health-App](https://github.com/Kappa5400/ERM-Employee-Item-Health-App)

## イントロダクション

営業向けのERMアプリです。従業員が所有するPC・備品・車の更新や保守が必要になった際に上司に自動メールを打つアプリになります。
以下はウェブサイトリンクになります。Digitaloceanのサーバで稼働しました。

[https://itemhealtherm.tech/](https://itemhealtherm.tech/)

このプロジェクトの目的はSpring Bootに慣れることや営業使いそうなツールを作ることでした。
このアプリはSchedulerで毎日自動的にスクリプトが実行されます。アイテムの更新や保守が必要になった場合は上司に自動メールを送ります。
メールはＭａｉｌｈｏｇでキャッチするので外部のメールアドレスに送りません。
アプリはＣＩ／ＣＤを導入しており従業員情報や保有アイテムの詳細をExcelでダウンロードする機能も備えています。
単体テストや結合テスト、GithubActionsやDockerでCi/CDにDigitaloceanサーバで自動稼働を行っています。
データベースアクセスにはMyBatisを使用し、生SQLでメソッドを定義し、Serviceレイヤーに提供しますFlywaydbでデータ移行を行います。
Spring SecurityとSpring MVCでバックエンドを構築しました。
フロントエンドはThymeleaf・JS・HTML・CSSで構築し、同一バックエンドと統合された一体型アプリです

**主な機能：**

- 従業員が所有するアイテムの管理システム
- 自動お知らせメール、Excelエクスポート、CRUD管理制度
- 単体テスト、結合テスト、Docker,クラウドデプロイCI/CDパイプライン
- リレーショナルDBによるPK/FKジョイン管理

データベース体系
<img src="{{ '/assets/css/img/ERM_ER_Chart.jpg' | relative_url }}" alt="DB Architecture" />
<br/>

システム体系
<img src="{{ '/assets/css/img/ERM_Architecture_Diagram.jpg' | relative_url }}" alt="DB Architecture" />


---

## プロジェクト詳細：システムアーキテクチャ

フロントエンドとバックエンドは同じポートで作って一つのアプリになります。複雑なフロントエンドは作らず、シンプルで保守しやすい構成を目指しました。

### 使用パッケージ
セキュリティ: bcrypt, Spring-security<br/>
ユーティリティ: Cron, Spring-mail, Jackson, Mybatis, Flywaydb, mailhog<br/>
テスト: Spring-test, junit<br/>
フロントエンド: Spring-routing, Thymeleaf<br/>

### パッケージ解説
- **セキュリティ:** <br/>
Spring-security: Springに組み込まれているセキュリティ機能。Security filter を構成することで認証処理に対応できます。
アプリの一部機能をBoss権限必須に制限したため、認証機能が必要でした。

bcrypt: パスワードを暗号化（ハッシュ化）するためのパッケージ。

- **ユーティリティ:**<br/>
Scheduler:毎日同じときに自動スクリプトを稼働して欲しかったのでSchedulerを使いました。<br/>
Spring-mail:自動メールを送るためにこのSpring-mailを使いました。<br/>
Jackson: JSONオブジェクトの対応するためのパッケージになります。<br/>
Mybatis: SIERのスタックに合わせるためにMybatisを使おうとしました。生SQLクエリをマッピングしてServiceとつながります。<br/>
Flywaydb:データベースの移行するためのパッケージです。<br/>
Mailhog: メールを送るたびにMailhogでキャッチしてフロントエンドに表示できるように設定しました。<br/>
Apache POI: Excelファイルを作成するために使用しました。<br/>

- **テスト:** <br/>
Spring-test: Springに組み込まれているテストパッケージです。<br/>
JUnit: 単体テストフレームワークです。<br/>
Mockito:　テスト用モックオブジェクトを生成するパッケージです。<br/>

- **フロントエンド:**<br/>
Thymeleaf:　軽いフロントエンドフレームワーク。<br/>

### ファイル構成


````text
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
````

### バックエンド詳細

#### データベース
データベースはPostgresを使いました。Postgresの実践経験を積むために使いました。
ある程度テーブルのジョインしたり関係データベースの機能を真似しようやSIERのスタックに合わせようとしました。

#### モデル
モデルはプライマリーキーと外部キーでジョインして検索できるように設計しました。Employeeプライマリーキー Employee＿ＩＤ でほかの
モデル外部キーとして付けていると物、Ｂｏｓｓ、そしてＳｕｂｏｒｄｉｎａｔｅでジョインできます。
Healthreportはアイテム情報を集約し、自動メール送信に使用するモデルです。ER表も上に見られます。

#### Flywaydb
データ移行するためFlywaydbを使いました。データベースのスキーマを作り情報一貫性や正常に起動させます。

#### Mybatis
生SQLでメソッドを定義します。プライマリーキーと外部キーでジョインして複数テーブルの情報を取得できます。
主にCRUDメソッドを定義し、Serviceレイヤーに提供します。

#### Service
主にMybatisCrud機能とロートをつながっています。自動スクリプト論理、自動メール機能、Excelエクスポート機能、
そして物Health確認自動機能も入っています。

#### Health service
従業員の持っている物の自動Health確認を行っています。アイテムの更新・保守が必要と判断された場合、自動的に担当上司へメールを送信します。
この自動物確認スクリプトはＢｏｓｓＤａｓｈｂｏａｒｄで開始もできます。

#### Scheduler
スクリプトを毎日同じときに自動で開始するためSchedulerを使いました。5日ごとにメール履歴を削除します。
ポートフォリオのために短い間のメール情報だけ表示しようと思って５日間のメール歴史は十分だと思いました。

#### Ｅｘｃｅｌ
従業員、上司、車、ＩＤＣａｒｄ、そしてノートパソコンの情報をExcelでエクスポートできます。
Apachi-POI ライブラリを使ってこの機能を追加しました。

#### メール
HealthCheckスクリプトが更新・保守の必要なアイテムを検出した際に、上司へ自動メールを送る仕組みです。
Spring-mailをつかって簡単お知らせメールを送ります。実際の外部メールアドレスには送信しないようにMailhogを使って、
Mailhogが送信メールをキャプチャして表示します。

#### ルーティング
ServiceとフロントエンドでつながっているAPIエンドポイントです。認証チェックや入力バリデーションもここで行います。
フロントエンドのルーティングもHealthController.javaで管理しています。

#### セキュリティ
Securityフィルターで認証を構成しています。ログイン論理、
リダイレクト論理、パスワードのBCryptハッシュ化などもここで処理します。


#### フロントエンド詳細
Thymeleaf、HTML,Boostrap CSS,そしてJSでフロントエンドを作りました。フロントエンドは主にCRUDフォームで従業員とノートパソコン、
Idcard,と車を管理できます。認証確認でBoss権限を持つユーザーのみCRUD操作が可能で、一般従業員は閲覧のみとなっています。
上司権限ユーザーはExcelエクスポートやHealthCheckスクリプトの実行も可能です。

#### テスト、CI/CD、デプロイ
テスト
テストは単位テストと結合テストを両方作りました。デプロイするたびにテストを行ってパスしなければデプロイしません。
すべてのAPIエンドポイントとService機能をパスべきテストと落ちるべきテストを作りました。

#### CI/CDとデプロイ
Github　ActionsでCI/CDパイプラインを作りました。自動テスト・Dockerビルド・サーバーへのデプロイという流れでCI/CDパイプラインを構築しました。
Docker Secretsを設定し、環境変数を安全に注入します。
ウェブサイトはDigitalOceanクラウドサーバーでデプロイして、NGINXやDockerコンテナーでデプロイして、
CloudflareとDigital Oceanファイアウォールで保護しています。
