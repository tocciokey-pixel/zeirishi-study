# zeirishi-study

## 目的
税理士試験の学習テーマを登録し、忘却曲線に沿って「今日の復習」を表示するアプリです。

このリポジトリは静的サイト（`index.html`）ですが、端末間でデータとPDFを同期するために **Supabase**（DB + Storage）を使います。

---

## セットアップ（Supabase）

### 1) Supabaseプロジェクトを作成
- Supabaseで新規プロジェクト作成

### 2) Storage バケットを作成
- Storage にバケット `zeirishi-pdfs` を作成
- **PublicはOFF（非公開）** のままでOK（アプリ側で署名付きURLを発行して開きます）

### 3) テーブル `themes` を作成
Supabaseの SQL Editor で次を実行してください。

```sql
create table if not exists public.themes (
  id bigserial primary key,
  name text not null,
  subject text not null,
  learn_date date not null,
  done_reviews jsonb not null default '[]'::jsonb,
  pdf_path text,
  pdf_name text,
  created_at timestamptz not null default now()
);
```

### 4) RLS（Row Level Security）について
このままだと誰でも読み書きできてしまうため、公開運用する場合は認証を入れる必要があります。
まず動かすための最短手順としては、以下のどちらかを選んでください。

- **最短で動かす（非推奨）**: `themes` のRLSをOFF、Storageもポリシーで書き込み/読み取りを許可
- **推奨**: Supabase Auth（ログイン）を入れて、RLSで自分だけアクセス可能にする

※ 現状の `index.html` は「最短で動かす」前提で作っています（Authは未実装）。

### 5) `index.html` に設定値を入れる
`index.html` の以下をあなたのプロジェクトの値に差し替えます。

- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`

場所は `index.html` 内の「Supabase 設定」ブロックです。

---

## 使い方
- 「追加」タブでテーマ名を入れて登録（PDFは任意で添付可能）
- 「今日」タブで復習対象が表示され、PDFがあれば「PDFを見る」ボタンで開けます
