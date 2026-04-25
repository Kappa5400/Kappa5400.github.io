---
layout: default
title: Spring ERM App
permalink: /projects/Spring/
---

# Spring Boot 営業支援・備品管理（ERM）アプリ 概要

## リンク:

[https://itemhealtherm.tech/](https://itemhealtherm.tech/)

## イントロダクション

本プロジェクトは、営業職などの従業員が所有するPC、備品、車両などの資産（アイテム）を管理するための簡易ERM（Employee Resource Management）アプリです。

最大の目的は、保守や更新が必要になった際に**上司へ自動で通知メールを送信**し、管理漏れを防ぐことです。Spring Bootの習得と、実務で即戦力となるツールの開発を目的として構築しました。

**主な機能：**

- **自動監視（Cron）**: 毎日スクリプトを実行し、更新期限が近い備品を自動抽出。
- **自動メール通知**: 更新が必要なアイテムがある場合、上司にメールで通知（開発環境ではMailhogでキャッチ）。
- **データエクスポート**: 従業員情報や備品詳細をExcel形式でダウンロード可能。
- **権限管理**: 上司（Boss）ロールのみがCRUD操作やスクリプト実行を行えるセキュリティ設計。
- **CI/CDパイプライン**: Docker、GitHub Actions、DigitalOceanを用いた自動デプロイ。

データベース体系
<img src="{{ '/assets/css/img/ERM_ER_Chart.jpg' | relative_url }}" alt="DB Architecture" style="width: 100%; height: auto;"/>

システム体系
<img src="{{ '/assets/css/img/ERM_Architecture_Diagram.jpg' | relative_url }}" alt="System Architecture" style="width: 100%; height: auto;"/>

---

## プロジェクト詳細：システムアーキテクチャ

本アプリは、バックエンド（Spring Boot）とフロントエンド（Thymeleaf）を同一ポートで稼働させるモノリシックな構成を採用しています。複雑さよりも「保守のしやすさ」と「軽量さ」を重視しました。

データベースにはPostgreSQLを採用し、MyBatisを用いた生SQL制御によって柔軟なデータ操作を実現。Flywayによるマイグレーション管理で、DBスキーマの整合性を保っています。

### 使用パッケージ

- **セキュリティ:** `Spring Security`, `bcrypt`
- **ユーティリティ:** `Cron`, `Spring Mail`, `Jackson`, `MyBatis`, `Flyway`, `Mailhog`, `Apache POI`
- **テスト:** `Spring Test`, `JUnit`, `Mockito`
- **フロントエンド:** `Thymeleaf`, `JavaScript`, `HTML/CSS`

### パッケージ解説

#### セキュリティ
- **Spring Security:** 認証・認可を管理。特に「Boss」権限の確認プロセスをフィルタチェーンで実装。
- **bcrypt:** パスワードの安全なハッシュ化に使用。

#### ユーティリティ
- **Cron:** 毎日決まった時間に自動監視スクリプトを走らせるために使用。
- **Spring Mail:** 通知メール送信機能の核。
- **MyBatis:** 日本のIT業界（SIer等）で広く使われるスタックを意識。生SQLをマッピングしてService層と連携。
- **Flyway:** DBマイグレーションを自動化し、環境構築の再現性を確保。
- **Apache POI:** 従業員や備品データをExcel出力するために採用。
- **Mailhog:** 開発・テスト用にメールを捕捉し、Web UIで確認可能にするツール。

#### テスト
- **JUnit / Mockito:** ユニットテストおよび結合テストにおいて、コンポーネントの挙動を保証。

#### フロントエンド
- **Thymeleaf:** バックエンドと密接に連携する軽量なテンプレートエンジン。

---

### ファイル構成

#### バックエンド
```text
ERM-Employee-Item-Health-App/
├── .github/workflows/       # GitHub Actions (CI/CD)
├── src/
│   ├── main/
│   │   ├── java/com/healthapp/itemhealth/
│   │   │   ├── config/      # 設定（Flyway等）
│   │   │   ├── controller/  # ルーティング・API
│   │   │   ├── mapper/      # MyBatis インターフェース
│   │   │   ├── model/       # エンティティ（Boss, Employee等）
│   │   │   ├── security/    # 認証フィルタ
│   │   │   ├── service/     # ビジネスロジック（Excel出力等）
│   │   │   ├── scheduler/   # Cronジョブ（自動スクリプト）
│   │   │   └── health/      # ヘルスチェック・通知ロジック
│   │   └── resources/
│   │       ├── db/migrations/ # Flyway SQLスクリプト
│   │       ├── mappers/     # MyBatis XMLマッピング
│   │       ├── templates/   # Thymeleaf HTML
│   │       └── static/      # JS/CSS
│   └── test/                # ユニットテスト・結合テスト
├── Dockerfile               # コンテナ化設定
└── docker-compose.yml       # 開発・実行環境構築