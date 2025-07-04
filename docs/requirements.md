# 要件定義（MVPフェーズ）

## 目的

本要件定義は、Unity製ソーシャルゲームにおけるバックエンド/BFF(API)システムの初期開発（MVP）に必要な要件を整理したものです。  
対象はプレイヤーのアカウント管理、ゲームデータの永続化、運営用CMS、および基本的なCI/CD構築とKPI基盤の整備です。

---

## ビジネス要件

| 項目                    | 内容                                                                 |
|-------------------------|----------------------------------------------------------------------|
| リリース時期            | 開発開始から3ヶ月以内にストア審査提出                               |
| 想定ユーザー規模        | 初期 1万DAU / ピーク時 10万DAU を想定                                |
| 対応プラットフォーム    | iOS / Android（Unity製）                                            |
| 運営体制                | 少人数体制（エンジニア1～3名 + PM + デザイナ1名）                  |
| 外部連携                | Google Analytics, Firebase Crashlytics, Googleスプレッドシート 等 |

---

## 機能要件

### 1. アカウント管理

- 匿名ログイン（SNS連携は将来的に）
- アカウント引き継ぎ（ID/PASSベース）
- ブロック・BAN対応

### 2. ゲーム機能

- ガチャ（抽選ロジック・排出履歴記録）
- クエスト（進行状況保存・リワード付与）
- アイテム管理（所持・消費・付与）
- アプリ内課金

### 3. マスターデータ管理

- マスターデータバージョンチェック
- マスターデータ更新管理（S3経由で配信）
- スプレッドシートまたはCSVからインポート

### 4. CMS（管理ツール）

- プレイヤー検索・ステータス確認・BAN操作
- ガチャ/アイテム付与、イベント期間設定
- ログ確認（KPI含む）

### 5. CI/CD & 環境管理

- Jenkinsによる自動ビルド/デプロイ
- dev/staging/prod 環境の分離（ブランチ戦略と連動）
- バージョン・データ同期管理の自動化

### 6. KPI基盤

- 日次DAU/リテンション/KPIをスプレッドシートへ自動出力
- Firebase Analytics と連携
- Admin画面 or Slackで簡易モニタリング可能に

---

## 非機能要件

| カテゴリ          | 要件例                                                                 |
|-------------------|------------------------------------------------------------------------|
| セキュリティ       | JWTによるAPI認証、HTTPS通信、CMSのVPN経由＋IP制限                              |
| パフォーマンス     | APIレスポンスタイム 300ms以内、バッチ処理は1分以内に完了              |
| 拡張性             | ゲーム機能の増加に柔軟に対応できるAPI構成（RESTful）                   |
| スケーラビリティ   | EC2ベースのAutoScaling構成を採用                                   |
| モニタリング       | CloudWatch / Datadogによる監視構成、Slack通知含む                      |
| ログ管理           | CloudWatch Logs + Athenaによる定期的な分析基盤                         |
| バックアップ       | DB定期バックアップとCloudWatch Eventsによるリテンション管理            |

---

## フロー概要

1. クライアント → BFF（Laravel API）へHTTPS経由で通信  
2. BFF → MySQL（永続化）  
3. CMS → BFF管理用API群を利用  
4. JenkinsがCI/CDパイプラインを起動（GitHub連携）
5. Firebaseやスプレッドシートと連携し、KPIを自動集計・出力

---

## 今後のスコープ外だが留意するべき領域

- Redis（ランキング、レスポンスキャッシュ等）を利用
- 多言語対応
- マイクロサービス分離
- Unity Addressables対応

---

## ステークホルダーと役割

| 役割       | 担当範囲                             |
|------------|--------------------------------------|
| PM         | 要件定義、スケジュール管理           |
| リードエンジニア | 技術選定、設計、インフラ構築           |
| バックエンド実装エンジニア   | API実装、CMS構築、CI/CD構築           |
| フロントエンド実装エンジニア   | Unity実装、CI/CD構築           |
| デザイナー     | 管理画面UI設計、バナー/画像素材       |

---

以上が、本構成案におけるMVPフェーズの要件定義です。
今後、要件の確定や仕様追加に合わせて都度アップデートを行います。
