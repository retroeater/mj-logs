# CHAT-1105-LGV-01

- 着手日時: 2026-11-05 14:20 JST（05:20 UTC）
- 対象issue: #640（参照）
- ブランチ: work/1105-lgv
- 着手時HEAD: 275d8111

## 指示

【Claude作成】Claude Code 向け指示：「ログ」（log/）のリニューアルの告知動画の初版を本番から撮って送る
Chat-Ref: CHAT-1105-LGV-01
マージ: 判断待ちで止まる（制作のスクリプトは作業ブランチに置き、動画の確定までマージしない。docs/notes/page-announcement.md「進め方」）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1105-lgv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-lgv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。LGV は新しい識別子なので、重複が無いことを確かめる。log/ の告知の期間は、この指示の着手から投稿の完了まで（docs/notes/page-announcement.md「進め方」）。着手の日時をログの経過に書く。

## 目的
2026-11-04 に公開した「ログ」（log/、#640）のリニューアルを X で告知するための動画の初版を、公開中の本番から撮って平野さんに送る。

### 決定（2026-11-05、平野さん）
- 動画の構成（約35秒・白地・縦長・操作デモ）と X の投稿文は、チャット側の案のまま（下の表と投稿文）
- 投稿者は平野良栄・谷岡育夫の2人とも映してよい（谷岡育夫の投稿の写真・名前を含む。本人の許可は平野さんが取る）
- 茗荷谷は映さない（駅の検索・タグ・結果のどれにも出さない）
- 場面5は、2人とも投稿した店「支那麺 はしご」（銀座）を出してから、投稿者「谷岡育夫」で絞る
- 現在地は新宿駅にする
- 曲はこれまでと同じ（曲調 b、軽快・120 BPM）

| 場面 | 秒 | 画面 | テロップ |
|---|---|---|---|
| 1 タイトル | 0〜2.5 | 白地に「ログ」「麻雀プロが訪れた飲食店を検索」（最初のコマからはっきり出す） | なし |
| 2 トップ | 2.5〜6 | カテゴリの行（新着）を横に送る | 新着・投稿回数の多い店 |
| 3 駅で検索 | 6〜11 | 検索欄に「銀座」と打ち、候補「駅 銀座 … N店」に枠を出してから押す | 店名・メニュー・駅・投稿者で検索 |
| 4 結果 | 11〜15 | 「×銀座」のタグと店ごとの投稿を送る | 駅から近い順に、店ごとの投稿 |
| 5 店と投稿者で絞る | 15〜22 | 「はしご」と打ち、店名の候補を押す（2人の投稿が並ぶ）→ 投稿者の候補「谷岡育夫」を押す（谷岡育夫の投稿だけになる） | タグを重ねて絞り込み |
| 6 現在地 | 22〜27.5 | タグを外し、検索欄の右端の照準のアイコンに押す印（丸）を出してから押す →「×現在地」で近い順に並ぶ | 現在地から近い店も |
| 7 範囲 | 27.5〜31 | 結果で止める | 飲食店1,700店超・2,400件超 |
| 8 締め | 31〜34.5 | 白地に `img/ogp.png` の「ryoei.pro」が1秒で現れ、その後は動かない | なし |

投稿文（平野さんが投稿する。動画には入れない）:

```
「ログ」をリニューアルしました。
https://ryoei.pro/log/

・麻雀プロが訪れた飲食店1,700店超をXポストから
・店名・メニュー・駅・投稿者で検索
・現在地から近い店もすぐ見つかる
・新着・投稿回数の多い店をひと目で
```

### 前提（チャット側。平野さんの決定ではない）
- 撮るのは公開中の本番 https://ryoei.pro/log/ 。仕様は docs/notes/log-page.md。本番へまとめて要求を送るので UA に `mj-internal` を含める
- チャット側が 2026-11-05 13時台に本番の `log/search.json` で数えた値（要確認）: 1,737店・2,425件。「支那麺 はしご」（駅 銀座）は投稿9件（平野良栄8・谷岡育夫1）。撮るときの値をログに書く
- 現在地はブラウザの位置の上書き（Playwright の geolocation）で与える。座標は `data/stations_n02.json` の東京都の「新宿」の駅の点を使い、ログに書く。実在の人の位置は使わない
- 店名のリンク（Google マップ）は押さない。Google マップの画面・ロゴを映さない（log/ のピンは自前の SVG）
- 投稿の写真は X の画像。読み終わるのを待って撮り、灰色の空の枠や白いままのコマを残さない
- 場面7の数はテロップの文（「1,700店超・2,400件超」）のまま使う。撮るときのページの説明文の数（「飲食店N店（M件）」）が 1,700店・2,400件以上であることを確かめる
- 作り方は docs/notes/page-announcement.md「告知動画の型」と、近い実例 `scripts/promo_video/saikyo_renewal/`・`scripts/promo_video/dictionary/`（README.md）に従う

## 手順
1. 本番の log/ で、上の前提の値（店・件数、「支那麺 はしご」の投稿と駅、銀座の候補、新宿の駅の点）を確かめ、ログに書く。茗荷谷が映る場面が無いかを、撮る前に場面ごとに確かめる（銀座の結果・現在地の結果の送る範囲に茗荷谷の店・駅が入るなら、送る量を変えて映らないようにし、変えた所を書く）
2. 上の構成で動画を撮って作る。曲・形式・テロップの置き方は「告知動画の型」のとおり。撮った後にコマで確かめる（テロップが名指すものが映るか、場面5で2人の投稿 → 谷岡育夫だけになるのが映るか、茗荷谷・Google マップの画面が映らないか、白いままのコマが無いか）。長さ・各場面の秒の実際を表にして書く
3. mp4 を `SendUserFile` で送り、制作のスクリプトを作業ブランチの `scripts/promo_video/log/`（README.md つき）に置いて push する。判断待ちで止まる（動画の直し・確定は次の指示）

## 止まる条件
- 「支那麺 はしご」が銀座の店として出ない、2人とも投稿した店でない、または谷岡育夫で絞ると0件になる（値を書いて止まる。件数が9件・1件から増えただけなら止まらず、値を書く）
- 撮るときの説明文の数が 1,700店か 2,400件を下回る
- 新宿の駅の点が `data/stations_n02.json` に無い、または現在地のアイコンが押せない（「準備中」）
- 茗荷谷が映らない撮り方が見つからない
- 未マージの work/ ブランチが `scripts/promo_video/` の同じファイルを変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1105-lgv
- ログ: https://github.com/retroeater/mj/blob/work/1105-lgv/docs/logs/CHAT-1105-LGV-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lgv
- 確認用URL: なし
- マージ: 未
- issue: #640（参照）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d9c57896）: https://github.com/retroeater/mj-logs/tree/main/guide/d9c57896

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
