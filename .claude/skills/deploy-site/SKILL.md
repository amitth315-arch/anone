---
name: deploy-site
description: Update the あの娘HIMITSUスポット band website (index.html in the amitth315-arch/anone repo) and deploy it to GitHub Pages via git push. Use this whenever the user asks to change, add, or fix something on the band's site — news/schedule updates, text or copy edits, tweaks to sections, styling changes, fixing a typo — or explicitly asks to "deploy", "publish", "push", or "update the site". Covers the full loop: edit index.html, show a diff for review, commit, push to main, and confirm the change is live on https://amitth315-arch.github.io/anone/.
---

# デプロイ: あの娘HIMITSUスポット 公式サイト

バンド「あの娘HIMITSUスポット」の公式サイトを更新し、GitHub Pagesへデプロイするためのスキル。

## 前提

- リポジトリ: `amitth315-arch/anone`(public, デフォルトブランチ `main`)
- 公開URL: https://amitth315-arch.github.io/anone/
- GitHub Pagesは `main` ブランチのルート直下を配信するlegacyビルド。push後、追加のビルド手順やActionsワークフローは不要 — GitHub側が自動で反映する。
- `index.html` が本番サイト。`index_3.html` はドラフト/バックアップ的なファイルで、Pagesからはリンクされていない。**明示的に指示されない限り触らない。**
- x-bot(X自動投稿)の管理・デプロイはこのスキルの対象外。サイト本体の更新のみを扱う。

## 手順

### 1. 現在地を確認する

作業前に、ローカルの `anone` リポジトリが最新かを確認する。

```
git status
git pull origin main
```

pullで想定外のリモート変更が出てきた場合は、先にユーザーへ共有してから進める(誰か他の経路でサイトを更新している可能性がある)。

### 2. 編集する

ユーザーの依頼内容に沿って `index.html`(または明示的に指定されたファイル)を通常の編集ツールで直接書き換える。単一HTMLファイルにCSS/JSが埋め込まれているので、既存のトーン・デザイン言語(ネオンピンク基調のダークな編集系バンドサイト)を壊さないよう、周辺のスタイルと一貫性を保つ。

依頼が曖昧な場合(例:「トップの文言をもっとエモくして」)は、変更前に方向性を一言確認してから進める。依頼が具体的な場合(例:「ライブ日程に7/20の追加」)はそのまま編集してよい。

### 3. 差分を見せる

`git diff` で変更内容を確認し、要点を日本語で簡潔にユーザーへ提示する。何を、どこを変えたかが一目でわかる程度でよい(diffの全文を貼るだけでなく、一言サマリを添える)。

このステップは省略しない — pushは後戻りしづらい操作なので、ユーザーが確認できる機会を必ず挟む。

### 4. コミット

ユーザーが内容に問題なければ、変更をコミットする。

- コミット対象は今回実際に編集したファイルのみをステージする(`git add index.html` のように個別指定。`git add -A` や `git add .` は使わない)— 意図しないファイル(`index_3.html` や作業中の一時ファイルなど)を巻き込まないため。
- コミットメッセージは日本語で、内容が伝わる簡潔なもの(例: `ライブ日程に7/20渋谷公演を追加`)。

### 5. push してデプロイ

```
git push origin main
```

- push先は必ず `main`。force push は絶対に行わない。
- pushが失敗した場合(リモートが先行しているなど)、`git pull --rebase` などで安全に解消してから再試行する。壊れた状態で無理に上書きしない。

### 6. デプロイ確認

push後、GitHub Pagesの反映には通常1分もかからない。以下のいずれかで確認する。

```
gh api repos/amitth315-arch/anone/pages
```

`status` が `built` で、直近のcommitが反映されていれば成功。念のため実サイト(https://amitth315-arch.github.io/anone/ )を取得して、変更箇所が実際に表示されているかも確認するとより確実。

反映が確認できたら、ユーザーに完了と公開URLを報告する。数十秒待っても反映されない場合は、`gh run list` などでビルド失敗がないか調べ、状況をユーザーに共有する。

## やらないこと

- `index_3.html` を無指示で変更・コミットしない
- x-botフォルダの追加・変更・デプロイ(スコープ外)
- force push、`main` 以外のブランチへのpush
- ユーザーの確認なしでのpush(diffレビューのステップを飛ばさない)
