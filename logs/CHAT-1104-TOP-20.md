# CHAT-1104-TOP-20

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top5
- 着手時HEAD: a4a9ed1b1

## 指示

【Claude作成】Claude Code 向け指示：llms.txt の5大会の行の「言葉で質問できる欄」を今の形（ナビの AI 検索）に直す（#101）
Chat-Ref: CHAT-1104-TOP-20
マージ: 判断待ちで止まる（チャット側が差分を見てから、平野さんにマージを聞く）
貼る時機: いつでも（TOP-19 と並べて走らせてよい。触るファイルが重ならない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top5 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。origin/cloudflare を起点に work/1104-top5 を新しく作る。リモートにすでにあれば止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

ページの中の質問欄は 2026-11-04 に全ページで廃止し、入口を共通ナビの AI 検索のアイコンにまとめた（CHAT-1104-TOP-12〜14）。`llms.txt`（手書き、#161）の5大会の入口の行は、まだ「〜の記録を言葉で質問できる欄」と、ページの中に欄があるように書いている。今の形に直す。

### 決定（2026-11-04、平野さん）

- ページの中の上部の質問欄は全ページで廃止。入口は共通ナビの AI 検索のアイコン（CHAT-1104-TOP-12 の決定）
- `llms.txt` の houou/・ouka/・jwrc/・wrcr/・saikyo/ の行の古い記述は TOP のチャットで直す（PRP 系列からの伝言、2026-11-04）

### 前提（チャット側。平野さんの決定ではない）

- 直す行（cloudflare 2026-11-04 時点でチャット側が見た。行番号でなく文で探す）: 鳳凰戦・女流桜花・WRCリーグ・WRC-Rリーグ・麻雀最強戦の入口の行の末尾「…へのメニューと、<大会>の記録を言葉で質問できる欄。」
- 直し方の案: 「…へのメニュー。画面上部の AI 検索（ナビの『AI に質問』）で<大会>の記録を言葉で質問できる（このページから聞くと<大会>に絞って答える）。」。ページの中に欄がある書き方にしない。Worker の動き（問いに大会名・選手名が無いときは開いているページの大会だけで答える、AIC-26）と食い違わないことを docs/notes/ask-worker.md で確かめる。食い違えば Worker の動きに合わせる
- 同じ `llms.txt` のうち、連盟プロの行・サイトマップの行・人数の2か所は PRP 系列が未マージの work/1101-prp-pub で変える（PRP 系列からの伝言）。それらの行は変えない。取り込みで衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）
- ほかに `llms.txt` で、廃止した欄を今あるものとして書いている所があれば同じ形で直す（新トップ top/ は未公開なので `llms.txt` に載せない）

## 手順

1. `llms.txt` の該当の行を洗い出し、`## 経過` に直す前と後を並べて書く。未マージの work/ ブランチで `llms.txt` を変えているものを一覧にし、変えている行が重ならないことを確かめる。#101 に着手中コメント
2. 直し、`docs/decisions/top.md` に1行足す。自己レビュー（docs/notes/self-review.md）をし、cloudflare との差分の一覧をログに書いて止まる（生成物は変わらないので再生成は不要。`check-assets.yml` が通ることだけ確かめる）

## 止まる条件

- 未マージの work/ ブランチが同じ行を変えている
- Worker の動きが docs/notes/ask-worker.md の記述と食い違い、どちらに合わせるか決められない
- #101 に他セッションの着手中コメントがある

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい。`llms.txt` は公開されるファイルなので、ドキュメントのみの変更にあたらない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a4a9ed1b）: https://github.com/retroeater/mj-logs/tree/main/guide/a4a9ed1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
