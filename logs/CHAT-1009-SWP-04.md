# CHAT-1009-SWP-04

- 着手日時: 2026-10-08
- 対象issue: なし
- ブランチ: work/1009-swp-race
- 着手時HEAD: a6988a56

## 指示

【Claude作成】Claude Code 向け指示：横断レビューの追加 — 鳳凰戦「順位変動」（houou_race）を同じ5観点で点検する（読むだけ）
Chat-Ref: CHAT-1009-SWP-04
マージ: ドキュメントのみ（docs/logs/・docs/decisions/）の変更なので、完了報告のうえ cloudflare へ入れてよい。それ以外のファイルを変える必要が出たら、マージせず判断待ちで止まる
貼る時機: いつでも（新しいセッションに、この指示だけを貼る）
作業ブランチ: クラウドセッションで実行する。work/1009-swp-race を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-swp-race origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-race の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

CHAT-1006-SWP-01（サイト全体の横断レビュー）で未公開のため対象外にした `houou_race.html`（鳳凰戦「順位変動」）が公開されたので、同じ観点で点検し、指摘の一覧をログに書く。何も直さず、issue も起票しない。

### 決定（2026-10-09、平野さん）

- `houou_race` は公開されたので、横断レビューの対象に含める

### 前提（チャット側。平野さんの決定ではない）

- 観点と、指摘1件ごとに書く項目は SWP-01 と同じ。CHAT-1006-SWP-01 のログの「### 手順2 サブエージェントの依頼文（5本）」の共通部（「指摘1件ごとに書く項目」など）と G1〜G5 の個別部を読み、そのまま使う。1ページなので、サブエージェントは使わなくてよい
- 「挙げなくてよいこと」には、SWP-01 の一覧に加えて、このページの見せ方の決定（docs/decisions/houou.md、docs/notes/houou-race.md）を入れる。決めたとおりの見た目・動き（帯のすべりこみ、カウントアップ、▲の表記など）は指摘にしない
- 鳳凰戦の新ページ群 houou/（#518、work/1008-hou は未マージ）で、順位変動は `houou/race/` に移り、旧 URL は公開の段で 301 になる予定（docs/decisions/houou.md による。要確認）。そのため、各指摘の行き先は「現行で直す」「houou/ の移設で扱う（#518）」「新サイトの要件」「見送り」のどれかにする
- サイト全体に共通する指摘（共通ナビ・スキップリンクなど、SWP-01 の G1-01・G1-02・G2-02・G2-03・G2-04・G4-01）は、このページでも出るかだけを書き、新しい指摘にしない
- 表示の確認は、作業ツリーを `python3 -m http.server` で配信し、Playwright の Chromium で開く（SWP-01 と同じ）。本番（ryoei.pro）はブラウザで巡回しない。データの JSON の取得失敗・0件などの状態は、手元の配信で応答を差し替えて確かめる
- 使う skill は無い

## 手順

1. 確かめる: CHAT-1006-SWP-01 のログの上の節、docs/decisions/houou.md、docs/notes/houou-race.md を読む。`houou_race` が公開されていること（navbar・sitemap・`llms.txt` に載り、noindex が無い）を確かめる。未マージの work/1008-hou で順位変動がどう扱われているか（移設先・旧 URL）をブランチのログと差分で確かめる（読むだけ）
2. 点検する: 幅 1280px・390px・360px、文字 200%（ルートの文字サイズを 200% にする近似。手段を書く）、キーボードだけの操作、選ぶ部品（期・前後期・リーグ・組）の hover・押下・フォーカス・無効の見た目、再生中・停止・節のラベルを押したとき、JSON の取得失敗・データの無い組み合わせ、title・description・h1・OGP・favicon
3. 一覧をログの `## 経過` に表で書く（SWP-01 と同じ列、通し番号は R-01 の形）。「現行で直す（小）」と重要と判断したものは実物で再現を確かめ、確かめていないものには「未検証」と書く。実機（iPhone の Safari）で確かめるべきものには、平野さんがそのまま開ける本番の URL と見る点を1行で書く。`## 報告` の「判断が必要なこと」に、平野さんが決めること（どれを現行で直すか、houou/ の移設で扱うもの）を書く

## 止まる条件

- `houou_race` が公開されていない（noindex がある、navbar に無いなど）
- 表示の確認の手段（http.server と Playwright の Chromium）がこのセッションで動かない（手段を入れ替えず、試したことと結果を書いて止まる）
- docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- 何も直していない・issue を起票していないこと（`git diff origin/cloudflare --stat` が docs/logs/・docs/decisions/ だけであること）を確かめて報告に書く
- 画面写真や中間ファイルはコミットしない
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-08 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-04"` に該当なし。識別子 SWP は同じチャットの SWP-01〜03 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1009-swp-race origin/cloudflare`。

## 報告

- 状態: 作業中
- ブランチ: work/1009-swp-race
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-race/docs/logs/CHAT-1009-SWP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-race
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0e33fde4）: https://github.com/retroeater/mj-logs/tree/main/guide/0e33fde4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0e33fde4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0e33fde4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0e33fde4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0e33fde4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0e33fde4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0e33fde4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
