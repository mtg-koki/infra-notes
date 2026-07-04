# Google Cloud コンピュート

## 概要

Compute Engineを基盤に、コンテナ（GKE）、サーバーレス（Cloud Run、Cloud Functions）まで、ワークロード特性に応じたコンピュートサービスの選定観点をまとめる。

## 主要サービス

- Compute Engine（マシンタイプ、プリエンプティブル/Spot VM）
- Google Kubernetes Engine（GKE、Autopilotモード/Standardモード）
- Cloud Run（コンテナベースのサーバーレス）
- Cloud Functions（イベント駆動サーバーレス）
- マネージドインスタンスグループ（MIG、水平スケーリング）

## 設計ポイント

- コンテナ運用の管理負荷を下げたい場合はGKE Autopilot、ノード制御が必要ならGKE Standardを選ぶ。
- HTTPリクエスト駆動でスケールtoゼロが必要な場合はCloud Run、軽量なイベント処理はCloud Functionsを選ぶ。
- Spot VMは中断を前提とした設計（バッチ処理、ステートレスワーカー）で活用し、コストを最適化する。

## 運用ポイント

- GKEのノードアップグレード・サレンダーポリシーを計画的に管理する。
- Cloud Runの同時実行数・コールドスタート特性を踏まえてタイムアウト/リソース割り当てを設計する。
- Compute Engineのライブマイグレーション（ホストメンテナンス時の自動移行）の挙動を理解しておく。

## ベストプラクティス

- （実務で得た知見をここに追記する）
