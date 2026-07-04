# Azure ID管理（Entra ID）

## 概要

Microsoft Entra ID（旧Azure AD）を中心としたID管理。認証・条件付きアクセス・特権アクセス管理（PIM）を含め、Azure/Microsoft 365横断のID基盤として扱う。

## 主要サービス

- Entra ID（ユーザー、グループ、アプリ登録、エンタープライズアプリケーション）
- 条件付きアクセス（Conditional Access）
- Privileged Identity Management（PIM、時限的な特権昇格）
- マネージドID（システム割り当て/ユーザー割り当て）

## 設計ポイント

- 人間のアクセスは条件付きアクセスで多要素認証・デバイス準拠を必須化する。
- ワークロードの認証はマネージドIDを優先し、シークレットの埋め込みを避ける。
- 特権ロール（Global Administrator等）はPIMで常時付与を避け、必要時のみ昇格する運用にする。

## 運用ポイント

- サインインログ・監査ログをLog Analytics/Sentinelに集約し、異常なサインインを検知する。
- アクセスレビュー（Access Reviews）を定期実行し、不要なロール割り当てを削除する。
- ゲストユーザー（B2B）のアクセス範囲を定期的に棚卸しする。

## ベストプラクティス

- （実務で得た知見をここに追記する）
