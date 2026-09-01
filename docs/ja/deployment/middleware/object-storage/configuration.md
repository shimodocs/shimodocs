# オブジェクトストレージ構成

[← ShimoDocs Suite デプロイメント文書](../../README.md)

このドキュメントは、実装、運用、または統合担当者が ShimoDocs 外部のサードパーティへの接続 S3 オブジェクトストレージをステップごとに行います。


# 1. 事前操作確認

## S3 オブジェクトストレージ要件

`Only object storage fully compatible with the S3 protocol is supported. Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, and AWS S3 are recommended. For local deployment, MinIO can be considered.`



> [!TIP]
>
> パブリッククラウドを優先、対応が必要 HTTPS 外部アクセス



## ネットワーク接続

オブジェクトストレージインスタンスに接続するための K8s ビジネスクラスターのネットワーク用ポートを開放する必要がある

```js
telnet IP 9000
```
## アクセス制御と権限
- 完全なAK/SK認証情報を提供する
- PutObject、GetObject、DeleteObject、ListObjects、CopyObject、InitiateMultipartUploadなどのコアインターフェースを完全にサポートする必要がある



# S3 ストレージ要件の説明
- 遅延要件: 内部ネットワーク環境におけるストレージの平均応答時間 API は50ms未満が推奨されます。公共のネットワーク環境では200ms未満が推奨されます。高い遅延は、ドキュメントの開封速度や添付ファイルのアップロード体験に直接影響します。
- 同時実行能力：企業の予想されるピークをサポートする必要があります QPS. ShimoDocs は、マルチユーザーのコラボレーションおよびバッチのインポート/エクスポート中にバーストトラフィックを生成するため、ストレージ側では過度に厳しいレート制限ポリシーを設けないようにする必要があります。
- 可用性 SLA: 本番環境でのストレージの可用性は 99.9%以上であることが推奨されます。
- パブリックネットワークストレージとの通信は、 HTTPS/TLS 暗号化チャネル。
- 時刻同期: S3 ストレージサービスと ShimoDocs アプリケーションサーバーは NTPを介して同期する必要があります。そうしないと S3 署名の検証に失敗します。
