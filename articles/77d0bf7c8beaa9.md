---
title: "Github Actionsでconcurrencyを使って重複実行を回避する"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: 
  - "githubactions"
  - "ci"
  - "github"
published: true 
published_at: "2024-11-13 11:35"
---
GitHub Actionsを使ったCI/CDワークフローでは、同じブランチで新たなコミットがプッシュされるたびにワークフローが再実行されることが一般的です。
しかし、進行中のワークフローを手動でキャンセルするのは面倒ですし、リソースの無駄にもなります。
今回は、GitHub Actionsのconcurrencyオプションを活用して、同一ブランチで重複実行されるワークフローを自動的にキャンセルする方法をご紹介します。

## concurrency設定のメリット

concurrencyオプションを設定することで、以下のメリットが得られます：
- 無駄なワークフロー実行の防止
同じブランチやタグの実行がキャンセルされるため、無駄な実行が発生しません。
- 効率的なリソース利用
進行中のワークフローがキャンセルされ、新しいコミットが最新の状態でテストされるため、リソースの利用効率が向上します。
- 開発サイクルの迅速化
手動でワークフローをキャンセルする必要がなくなり、チーム全体の作業が効率化されます。

## 設定方法

設定はとても簡単で、以下のようにconcurrencyキーを追加します。
```yaml
concurrency:
  group: ${{ github.ref }}
  cancel-in-progress: true
```
- group
同一のブランチまたはタグを基準に並行実行をグループ化します。この例では${{ github.ref }}を使用しています。
- cancel-in-progress
trueに設定することで、新しい実行がトリガーされた場合に進行中のワークフローが自動キャンセルされます。

## 実際に利用してみて

設定後、プルリクエストに新しいコミットが追加されるたびに、GitHub Actionsが進行中のワークフローをキャンセルして最新の実行をトリガーするようになりました。
これにより、常に最新のコード変更に対してCI/CDが動作するため、手動キャンセルの手間もなく、結果的にリソースも節約できます。

## 終わりに

GitHub Actionsのconcurrencyオプションは、無駄なリソース消費を抑えつつ、効率的なワークフロー運用を可能にしてくれます。
ぜひ皆さんも、このオプションを活用して、よりスムーズなCI/CDの管理を実現してみてください。
