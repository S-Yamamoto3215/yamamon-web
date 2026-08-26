# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 概要

Hugo で構築された個人サイト（https://yamamon.page/）。コンテンツは Markdown のみで、
アプリケーションコードやテストは存在しない。テーマ `gokarna` は git submodule。

## コマンド

```bash
# submodule（テーマ）の取得 — clone 直後は必須。これを忘れるとビルドが失敗する
git submodule update --init --recursive

# ローカルサーバー（下書き含む）
hugo server -D

# 本番と同じビルド（Netlify と同じオプション）
hugo --gc --minify

# 新規記事 / 新規ページ（archetypes が front matter を生成する）
hugo new content/posts/YYMMDD.md      # type: "post" のテンプレート
hugo new content/career/xxx.md        # type: "page" のテンプレート
```

デプロイは Netlify が `main` への push を検知して自動実行（`netlify.toml`）。
`public/` は gitignore 済みで、コミットしない。

**Hugo のバージョン差異に注意**: `netlify.toml` は `HUGO_VERSION = "0.145.0"` を固定している。
ローカルの Hugo がこれより新しい場合、ローカルで通ってもビルドが落ちることがある。
Hugo の新機能を使う場合は `netlify.toml` 内の 4 箇所すべての `HUGO_VERSION` を合わせて更新する。

## 設定

設定ファイルは `hugo.toml` ではなく **`config.toml`**（ルート）。

- Google Tag Manager のスニペットは `params.customHeadHTML` にインラインで埋め込まれている。
  解析タグの変更はここを編集する。
- グローバルナビは `[menu.main]` の配列。新しいトップレベルセクションを追加したら、
  ここにもエントリを足さないとナビに出ない。

## コンテンツ構造と規約

### `content/posts/` — ブログ記事

- ファイル名は日付ベースの `YYMMDD.md`（例: `250520.md`）。タイトルからのスラッグは使わない。
- front matter は `type: "post"`、`date` / `lastmod` / `tags` / `title` / `showTableOfContents` を持つ。
- 本文は日本語。既存記事に合わせて、`---` 区切りと `##` 見出しで構成する。
- タグは既存の値（`KPT`, `Diary`, `Shopify` など）を再利用する。新しいタグを増やす前に
  `grep -h "^tags:" content/posts/*.md` で既存タグを確認する。

### `content/career/` — 職務経歴

`type: page` の階層構造で、**インデックスページが手書きの Markdown テーブルで子ページへリンクしている**
のがこのセクションの要点。Hugo の自動リストではないため、リンクの整合性は手動で保つ必要がある。

- `content/career/_index.md` — スキルセット表と全プロジェクトの一覧表（会社単位）
- `content/career/freelance/_index.md` — フリーランス案件の一覧表。
  「契約終了したプロジェクト」と「現在進行中のプロジェクト」の 2 つの表に分かれている。
- `content/career/freelance/<期間>.md` — 案件ごとの詳細。ファイル名は稼働期間
  （`2203-2301.md` = 2022/03〜2023/01、`2506-now.md` = 継続中）。

**案件ファイルを追加・リネームするときは、必ず親の `_index.md` の表も更新する。**
特に案件が終了して `XXXX-now.md` を `XXXX-YYMM.md` にリネームする場合、
`freelance/_index.md` 内の `[more](/career/freelance/XXXX-now)` リンクを新しいパスに直し、
その行を「現在進行中」の表から「契約終了した」の表へ移す（Hugo はリンク切れを検出しない）。

案件詳細ページのセクション構成は統一されている:
`## 概要` / `## 規模` / `## 役割` / `## 実施したこと` / `## 印象に残っていること` / `## コメント`

## テーマのカスタマイズ

`themes/gokarna/` は submodule なので直接編集しない。上書きしたい場合は、
同じパスのファイルをリポジトリルート側の `layouts/` に置いて Hugo のルックアップ順で上書きする。
