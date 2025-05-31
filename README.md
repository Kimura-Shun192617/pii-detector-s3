pii-detector-s3
S3にアップロードされたファイル内のPII（個人識別情報）を自動で検知するGo製ツール

## ✅ 概要
- pii-detector-s3 は、AWS S3 バケットにアップロードされたファイル（PDFやテキスト）から個人識別情報（PII）を抽出・検出する軽量なGoアプリケーションです。
- セキュリティ監査・データガバナンスの一環として、クラウドストレージ内のPIIの自動検出を実現できます。

## 🏗️ 構成
```
pii-detector-s3/
├── cmd/
│   └── detector/              ← メイン実行ファイル（main.go）
├── internal/
│   ├── s3/                    ← S3ファイルの取得ロジック
│   │   └── client.go
│   └── pii/                   ← PII検出ロジック（正規表現など）
│       └── detector.go
├── go.mod
├── README.md                  ← 本ファイル
└── architecture.png           ← アーキテクチャ図
```

## 🔍 検出対象のPII
- 以下のような日本国内で一般的な個人情報形式を、正規表現を用いて検出します。

- PII種別	例
- メールアドレス	example@example.com
- 電話番号	090-1234-5678
- 郵便番号	123-4567
- マイナンバー	1234-5678-9012
- クレジット番号	1234-5678-9012-3456（Luhn検査含む）

※ 各種正規表現は internal/pii/detector.go に定義。

## ⚙️ 使用方法
1. 環境変数を設定（S3アクセス用）
```
export AWS_ACCESS_KEY_ID=xxxxx
export AWS_SECRET_ACCESS_KEY=xxxxx
export AWS_REGION=ap-northeast-1
export BUCKET_NAME=my-secure-bucket
export FILE_KEY=uploads/invoice.pdf
```

2. 実行
```
go run ./cmd/detector/main.go
```

3. 出力例
```
[INFO] Fetching file from S3: uploads/invoice.pdf
[INFO] Extracted text length: 10342 chars
[WARN] Detected email: yamada.taro@example.com
[WARN] Detected phone: 090-1234-5678
[OK] PII detection completed.
```

## 🔒 セキュリティ
- S3は事前に **バージョニング・暗号化（SSE-S3 or KMS）**を有効化推奨。
- IAMポリシーは **最小権限（Least Privilege）**で設定。

## 🔧 今後の拡張候補
- Lambda対応によるイベント駆動スキャン
- PDF → OCR対応（tesseractなど）
- PII検出精度向上（MLモデルへの置き換え）

## 🎯 ユースケース
- 法人向け文書管理システムの個人情報自動検出
- ISMS対応や監査ログ出力要件の補助
- 請求書・契約書等への誤アップロード検出

## 🧑‍💻 対象者
- クラウドセキュリティ／データガバナンスに関心のあるSRE
- エンタープライズ向けSaaS構築者
- AWS × Goの実践プロジェクトを構築したい方
