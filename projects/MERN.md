---
layout: default
title: MERN App
permalink: /projects/MERN/
---

# MERNスタック チェスアプリ 概要

##  リンク:

[https://chesspuzzlekappa54.me/](https://chesspuzzlekappa54.me/)

## Githubリンク:

[https://github.com/Kappa5400/mern_chess](https://github.com/Kappa5400/mern_chess)

## イントロダクション

本プロジェクトは、Digital Oceanにデプロイされたチェスアプリのリポジトリです。UIの構築だけでなく、実用的なCI/CDパイプラインを構築している点が特徴です。

GitHubへプッシュするたびに、コードのテストと静的解析（Lint）が自動実行され、フロントエンドとバックエンドのDockerイメージが生成されます。その後、サーバー側のセルフホスト型GitHub Runnerがイメージをプルし、デプロイまでを自動で行います。

テストに関しては、サービスレイヤーの各ユニットテスト（正常系・異常系）および全APIエンドポイントの結合テストを実装しています。

**主な機能：**

- アカウント作成・ログイン
- Lichess APIからのデイリーパズル自動取得と保存
- 直近10件のパズル表示
- スコアシステム（ログイン中に解決でポイント付与）
- ランキング表示
- ユーザー独自のパズル作成・閲覧・削除

このアプリは、私のソフトウェアエンジニアとしてのスキルを証明するために開発しました。詳細は以下の通りです。

データベース<br/>
<img src="{{ '/assets/css/img/DB_arc.jpeg' | relative_url }}" alt="DB Architecture"/>
<br/>
体系<br/>
<img src="{{ '/assets/css/img/Mern_arc.jpeg' | relative_url }}" alt="System Architecture"/>



---

## プロジェクト詳細：システムアーキテクチャ

本アプリは、フロントエンドとバックエンドを完全に分離したクライアント・サーバーアーキテクチャで構築されています。

バックエンドにはMongoDBを採用し、サービスレイヤーとルーティングレイヤーで構成されています。また、データバリデーションや認証のためのミドルウェア、テスト環境、ロギングユーティリティ、自動スクリプトを含んでいます。

フロントエンドからはReact Hooksまたはコンポーネント経由でAPIハンドラー層にアクセスします。また、認証コンテキストを使用してトークン管理を行っています。

### 使用パッケージ

- **セキュリティ:** `bcrypt`, `express-jwt`, `express-mongo-sanitize`, `joi`, `helmet`, `jsonwebtoken`
- **ユーティリティ:** `axios`, `dotenv`, `compression`, `cors`, `cron`, `mongoose`, `morgan`, `winston`, `swagger`, `eslint`, `cross-env`
- **テスト:** `jest`, `mongodb-memory-server`, `supertest`
- **フロントエンド:** `React`, `Vite`, `Chess.js`, `react-chessboard`, `tanstack`, `jwt-decode`, `react-router-dom`

### パッケージ解説

#### セキュリティ

- **bcrypt:** パスワードを暗号化（ハッシュ化）するためのパッケージ。
- **express-jwt / express-mongo-sanitize:** MongoDBへの注入攻撃（インジェクション）を防ぐミッドウェア。
- **joi:** 入力データのバリデーション用ミドルウェア。
- **helmet:** HTTPヘッダーを設定し、セキュリティを強化するミドルウェア。
- **jsonwebtoken:** セッション認証用のJSON Web Token。

#### ユーティリティ

- **axios:** LichessのパズルAPIへHTTP GETリクエストを送るために使用。
- **compression:** HTTPレスポンスを圧縮し、パフォーマンスを向上。
- **cors:** クロスオリジンリソース共有を設定し、フロントエンドからAPIへのアクセスを許可。
- **cron:** Lichessパズルを自動取得するためのスクリプト実行に使用。パズル更新が不定期なため、1日2回（朝・晩）チェックを行い、重複がない場合のみDBに保存します。（実行パス: `chess/backend/src/service/jobs`）
- **mongoose:** MongoDB操作のためのODM（Object Data Modeling）。
- **morgan / winston:** HTTPリクエストのログ出力およびシステムログの管理。
- **swagger:** APIドキュメントの自動生成。
- **eslint:** JavaScriptコードの品質管理（リンター）。
- **cross-env:** 環境変数をOSに依存せず設定するためのツール。

#### テスト

- **Jest:** ユニットテストおよび結合テストに使用したテストフレームワーク。
- **mongodb-memory-server:** テスト時に実DBを汚さないよう、メモリ上で動作するDBをモックとして使用。
- **supertest:** APIの結合テストおよびE2Eテスト用のパッケージ。

#### フロントエンド

- **React / Vite:** フロントエンドフレームワークおよびビルドツール。
- **Chess.js:** チェスのゲームロジック（ルール判定など）を制御。
- **react-chessboard:** 盤面をUIに表示するコンポーネント。Chess.jsと連携して動作。
- **tanstack (React Query):** 非同期データの取得・キャッシュ・同期を管理。
- **jwt-decode:** バックエンドで生成されたJWTをデコードし、セッションの有効性を確認。
- **react-router-dom:** React内での画面遷移（ルーティング）を管理。

---

### ファイル構成

#### バックエンド

````text
mern_chess/chess/src/
├── __tests__/      # ユニットテストおよび結合テスト
├── db/             # DB初期化スクリプトとモデル定義
├── middleware/     # joiバリデーションとJWT認証
├── routes/         # ルーティングロジック
├── service/        # ビジネスロジック
│   └── jobs/       # cronジョブスクリプト
├── swagger/        # Swagger API定義ファイル
├── tests/          # テストのセットアップおよび破棄設定
├── utils/          # Winstonロギング設定
├── app.js          # エントリーポイント
└── server.js       # サーバー設定

フロントエンド
```text
mern_chess/chess/frontend/src/
├── api/            # API通信の定義
├── components/     # Reactコンポーネント
├── contexts/       # 認証などのコンテキスト
├── hooks/          # API呼び出し等のカスタムフック
├── pages/          # ページテンプレート
├── App.jsx         # ルーティング設定
└── main.jsx        # エントリーポイント
````

### バックエンド詳細

バックエンドは「DBモデル ⇄ サービス ⇄ ルート」の標準的なパターンに従い、バリデーションとロギングのミドルウェアを組み込んでいます。ロジックは、ユーザー認証、パズル取得、ユーザー作成パズルの3つに分割して管理。

### ユーザー作成パズルについてはCRUD機能を備えており、認証済みユーザーは自身のパズルの作成・削除が可能です（閲覧は未認証でも可能）。DBサイズを適切に保つため、古いパズルを自動削除する仕組みも導入しています。

### セキュリティ

JWTを用いてセッション中のトークンを追跡し、認証が必要なアクションを制御しています。パスワードはDB保存前にハッシュ化。ユーザーからの入力はすべてサニタイズ（無害化）され、joiスキーマによって型と形式が厳密に検証されます。Webサイトとして期待される標準的なセキュリティ対策を実装しています。

### フロントエンド詳細

Reactのコンポーネントベースの構造を採用し、「Pages ⇄ Components ⇄ Hooks ⇄ API」という明確なデータフローを構築しました。

チェスのロジックにはChess.jsとreact-chessboardを使用していますが、パズル作成時の位置情報入力などの独自ロジックは自前で実装しています。ログイン状態に応じた動的なUI変更（作成ボタンの表示・非表示など）や、スコアボード表示機能を備えています。

## テスト、CI/CD、デプロイ

### テスト

Jestを用いてユニットテストと結合テストを実施しています。ビジネスロジックの各関数に対して正常・異常の両パターンをテストし、APIエンドポイントが正しくデータを送受信できることを保証しています。これにより、コード変更時のデグレ（不具合の再発）を最小限に抑えています。

### CI/CD

GitHub Actionsを利用し、プッシュごとに自動テストとリントを実行。テスト通過後にDockerイメージをビルドし、サーバー側のGitHub Runner経由で自動デプロイされるパイプラインを構築しました。この一連の流れにより、摩擦のない継続的デリバリー（Frictionless CI/CD）を実現しています。

### デプロイ

Digital OceanのDroplet上でホスト。CloudflareによるIP秘匿化とDigital Oceanのファイアウォールを組み合わせることで、外部攻撃（DDoS等）から保護された安全な環境を構築しています。
