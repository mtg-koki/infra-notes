# Azure ネットワーキング

## 概要

VNetを中心としたAzureのネットワーク設計。ハブ&スポーク構成、オンプレ接続（ExpressRoute/VPN Gateway）、グローバル配信（Front Door）を含めて扱う。

## 主要サービス

- Virtual Network（VNet、サブネット、NSG、ルートテーブル）
- VPN Gateway / ExpressRoute（オンプレ接続）
- Azure Firewall / Front Door / Application Gateway
- Private Link / Private Endpoint（PaaSへのプライベート接続）
- Azure DNS

## 設計ポイント

- ハブ&スポーク構成を基本とし、共有サービス（ファイアウォール、DNS、VPN）をハブに集約する。
- PaaSサービス（Storage、SQL Database等）へのアクセスはPrivate Endpointを用い、パブリックエンドポイントを無効化する。
- NSGはサブネット/NIC単位で適用範囲を明確にし、ルールの重複・矛盾を避ける。

## 運用ポイント

- NSG Flow Logs / Traffic Analyticsで通信を可視化し、異常検知に活用する。
- ExpressRouteの回線冗長化（プライマリ/セカンダリ）と切り替え試験を実施する。
- IPアドレス管理はAzure Virtual Network Managerやドキュメントで一元管理する。

## ベストプラクティス

- （実務で得た知見をここに追記する）
