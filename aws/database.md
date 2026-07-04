# AWS データベース

## 概要

リレーショナル（RDS/Aurora）からNoSQL（DynamoDB）、インメモリ（ElastiCache）まで、データモデルと要件に応じたデータベース選定の観点をまとめる。

## 主要サービス

- RDS（マネージドリレーショナルDB: MySQL/PostgreSQL/SQL Server等）
- Aurora（RDS互換の高性能・高可用DB）
- DynamoDB（フルマネージドNoSQL）
- ElastiCache（Redis/Memcached、キャッシュ層）
- Database Migration Service（DB移行）

## 設計ポイント

- トランザクション整合性・複雑なクエリが必要ならRDS/Aurora、スキーマレスかつ高スループットが必要ならDynamoDBを選ぶ。
- DynamoDBはアクセスパターンを先に設計し、パーティションキー設計でホットパーティションを避ける。
- Auroraはリードレプリカとフェイルオーバー特性を活かし、読み書き分離と可用性を両立する。

## 運用ポイント

- RDS/Auroraの自動バックアップ・ポイントインタイムリカバリの設定を確認し、RPO要件を満たす。
- DynamoDBはオンデマンド/プロビジョンドキャパシティをトラフィックパターンに応じて選択し、スロットリングを監視する。
- スロークエリ・接続数上限はPerformance InsightsやCloudWatchで監視する。

## ベストプラクティス

- （実務で得た知見をここに追記する）
