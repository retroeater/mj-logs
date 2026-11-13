# CHAT-1113-SEO-22

- 着手日時: 2026-11-13
- 対象issue: #458, #680
- ブランチ: work/1113-seo22
- 着手時HEAD: 613f050

## 指示

【Claude作成】Claude Code 向け指示：URL 検査の全件の結果を確かめてまとめ、title/ ほかが登録されない原因（参照元のページ0件・JS で描くリンク）を調べ、直し方の案を出して止まる（#458・#680）
Chat-Ref: CHAT-1113-SEO-22
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/decisions と、docs/gsc/ の説明・まとめの md だけ。ページ・コードは直さない）
貼る時機: 2026-11-13 の日本時間 09:00 以降（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1113-seo22〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-seo22 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-seo22 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-seo22 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、今が 2026-11-13 の日本時間 09:00（UTC 00:00）以降であることを確かめ、そうでなければ何もせず止まる。

## 目的
サイトマップの全 URL（2,398件）の URL 検査の残り（約401件）を取り、結果をまとめ、Google の登録状態をサイトマップ・ページの種類ごとに数で示す。途中までの結果で、title/ は登録済みが 111/385 件、参照元のページが0件の URL が 383/385 件だった（チャット側の集計）。原因（チャット側の推測: 入口・大会のページから下のページへのリンクを JS で描いていて、Google が見つけていない）を生成物とコードで確かめ、直し方の案を出して止まる。直すのは平野さんが案を見て決めた後の別の指示で行う。

### 決定（2026-11-13、平野さん）
- （CHAT-1112-SEO-19〜21 の決定のまま）全件を取り、結果を docs/gsc/ に残す。結果を見て直し方を決める

### 前提（チャット側。平野さんの決定ではない）
- 結果のファイル: `docs/gsc/inspect/2026-11-12.csv` に 1,997件（チャット側が数えた、要確認）。1回目（実行 43169347821）は読み取りの時間切れで 1,686件、2回目（実行 43251047782、`inspect_resume=true`）は 311件を取ったところで `429 Quota exceeded for sc-domain:ryoei.pro.`（RESOURCE_EXHAUSTED）で止まった。残りは約401件（live の残り・mtsuku・exam）
- **上限の区切りは、チャット側が前に書いた「日本時間17時（太平洋時間の0時）」ではなかった。** 1回目の開始（06:37Z）から2回目の止まり（16:18Z）までの約2,000件が同じ日として数えられたので、UTC の日付（日本時間 09:00 に切り替わる）か、24時間の幅で数えている見込み（チャット側の推測。公式の文書で確かめて書く）
- 残りの取得: 手順1で `fetch-gsc.yml` を `task=inspect`・`inspect_resume=true`・`commit=true`・ref cloudflare で起動する。約401件 × 約6秒で40分ほどの見込みで、同じセッションで合計45分まで待ってよい。また 429 の上限のエラーで止まったら、もう一度起動せず、取れた件数と時刻を書き、取れた分でまとめる（24時間の幅で数えているなら、日本時間 2026-11-13 16:20 以降に取れる見込み、と書く）
- 途中までのチャット側の集計（1,686件、要確認）:

  | サイトマップ | 件数 | 送信して登録されました | 検出 - インデックス未登録 | URL が Google に認識されていません | クロール済み - インデックス未登録 | 参照元0件 |
  |---|---|---|---|---|---|---|
  | pages | 54 | 53 | 1 | 0 | 0 | 13 |
  | wayhome | 45 | 34 | 10 | 1 | 0 | 42 |
  | saikyo | 892 | 592 | 212 | 86 | 2 | 587 |
  | title | 385 | 111 | 192 | 80 | 2 | 383 |
  | live（途中） | 310 | 27 | 223 | 60 | 0 | 310 |

- まとめの軸:
  - `coverageState`（と `verdict`）の件数を、サイトマップ別と、ページの種類別（title/ は入口・大会・期、saikyo/ は年度・対局・卓・players・ranking など、live/ は入口・大会・期・回など。canonical の表〈CHAT-1031-SEO-13〉と同じ単位）で表にする
  - `referringUrls` が0件の割合を同じ軸で。0件でない URL は、どのページから参照されているか（参照元の種類）を数える
  - `googleCanonical` と `userCanonical` が違う URL を全件。`lastCrawlTime` が無い URL の件数
  - `title/nihon-series/` と `title/joryu-nihon-series/` の行を並べる（#680）
- 原因の確かめ（コードと生成物で）: title/ の入口（`/title/`）・大会のページ（例 `title/houou/`）・saikyo/ の年度のページ・live/ の入口から、下のページへのリンクが、生成された HTML に `<a href="…">` として静的に出ているか、JS（`assets/title.js` など）が描いているか。種類ごとに「静的なリンクの数／JS が描くリンクの数」を表にする。`curl` の HTML（JS を動かさない）で数えてよい。本番を取るときは逐次・1分30回未満（Rate limiting rule #124）。生成物で足りるなら本番は取らない
- 登録済みの割合が高い種類（pages・wayhome）と低い種類（title・live）で、リンクの出し方・サイトマップでの扱い・ページの中身の量（本文の文字数）の違いを並べる
- 直し方の案（チャット側の例。実物に合わせて2〜3個、メリット・デメリットと、ページの種類ごとの件数での効き目を書く）: 下のページへの `<a href>` を静的に出す（JS の描画は残してよい）／中身の薄いページをまとめる・noindex にする／今のまま様子を見る。TTL・SKP など、同じ生成スクリプトを触る系列が動いていないかも書く
- まとめは `docs/gsc/inspect/<取得日>-summary.md` に、件数の表と判断に要る URL だけを書く（全件は CSV にある）
- 月1回の定期取得への組み込み（run-monthly に入れるか）は、「判断が必要なこと」に案として書く（WFL 系列の調整が要るかも書く。この指示では組み込まない）。1件あたり約6秒（1日目の実測）で、全件で約4時間・2日がかりになる点も書く

## 手順
1. 0章の確かめの後、上の前提のとおり残りの取得を起動し、結果（件数・所要時間・再試行の回数・エラーの件数）と、CSV の全体の行数をログに書く。URL 検査 API の上限の数え方を公式の文書で確かめ、docs/gsc/README.md の説明（「日本時間17時に切り替わる」などの記述があれば）を直す
2. 上の前提の軸でまとめ、原因の確かめの表を作り、`docs/gsc/inspect/` に md で置く。#680 と #458 に要点（件数）と md の場所をコメントする（本文・状態・ラベルは変えない）
3. ログを仕上げて `git push origin work/1113-seo22:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。`## 報告`「判断が必要なこと」に、直し方の案と、月1回の定期取得の案を書く

## 止まる条件
- 0章の条件が満たされない（何もせず止まる）
- 作業ブランチに docs/logs・docs/decisions・docs/gsc/ 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SEO-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SEO-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-seo22
- ログ: https://github.com/retroeater/mj/blob/work/1113-seo22/docs/logs/CHAT-1113-SEO-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-seo22
- 確認用URL: なし
- マージ: 未
- issue: #458, #680
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 613f050c）: https://github.com/retroeater/mj-logs/tree/main/guide/613f050c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/613f050c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/613f050c.md
