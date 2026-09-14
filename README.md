# GK Generic GitHub Uploader

Guild Adventure Studio の GitHub 配置 Gate を汎用化した、静的HTMLだけで動くアップローダーです。

## 使い方
1. `index.html` をブラウザで開く（GitHub Pages等のHTTPS配信を推奨）。
2. Owner / Repository / Branch / Base Path / PAT を入力。
3. ZIPを選び「ローカル検査」。
4. 必要なら `DELETE_MANIFEST.txt` の削除許可、保護パス変更許可を設定。
5. 「差分・Gateを検証」。
6. 差分を確認して「GitHubへ1 Commitで配置」。

## Studioから転用した安全設計
- ZIP選択だけではGitHubへアクセス・書込しない
- PATは保存しない
- ZIP path traversal / `.git` を拒否
- ZIPファイル数・展開サイズ上限
- GitHub Tree `truncated` なら停止
- ローカル Git Blob SHA とGitHub作成Blob SHAを照合
- Gate後にHEADが変わったら配置停止
- Blob → Tree → Commit → ref更新の1 Commit反映
- ref更新は `force:false`
- 配置後にHEADと各Blob SHAを再検証
- 削除は `DELETE_MANIFEST.txt` 指定のみ
- 保護パス変更は二重明示承認
- Gate診断JSON出力
- 直前Commit以外へ進んでいたらRollback停止

## DELETE_MANIFEST.txt
ZIPのルート（共通ルートフォルダ内でも可）に置きます。1行1パスです。

```text
# コメント可
obsolete/file.js
old/assets/logo.png
```

削除はUIで「削除候補を許可する」をONにした場合だけ計画へ入ります。

## PAT権限
対象Repositoryの Contents を書き換えられる Fine-grained PAT を推奨します。PATはこのツールの localStorage には保存しません。

## 注意
ブラウザから `api.github.com` に直接接続します。組織ポリシーやPAT設定で拒否される場合があります。
