# CHAT-1019-WRC-18

- 着手日時: 2026-10-19
- 対象issue: #599（クローズ済み。告知の完了のコメント）
- ブランチ: work/1019-wrc14 → cloudflare へマージ
- 着手時HEAD: b3bee0adb

## 指示

【Claude作成】Claude Code 向け指示：WRCリーグの告知動画の制作のスクリプトと文書（work/1019-wrc14）を cloudflare にマージし、告知の記録を残す Chat-Ref: CHAT-1019-WRC-18 マージ: 下の確かめが通れば cloudflare にマージする（2026-10-19、平野さんが CHAT-1019-WRC-16 の動画を確定し、X に投稿した） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 起点のブランチから work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrc14 の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-wrc14 を続けて使う（このログもここに置く）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。`docs/decisions/` の末尾への追記どうしの衝突は、両方の節を残して日付・番号の順に並べて解いてよい（解いた後の見出しの並びをログに書く）。それ以外の衝突は解かずに止まる。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-WRC-16 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-WRC-18` を足す。

目的・決定（2026-10-19、平野さん）

* CHAT-1019-WRC-16 の動画（44.3秒）で確定。2026-10-19 18:00 に X に投稿した（予約投稿）: https://x.com/retroeater/status/2112106481051210234
* 投稿文（平野さんが入力したとおり。チャット側の案から平野さんが直した）:


```
「WRCリーグ」をリニューアルしました。
https://ryoei.pro/jwrc/

・選手ごとの期別/通算成績・最高到達を1ページで
・ランキング9部門、通算成績・通過回数・通過率等
・予選通過ボーダー争いをアニメーションで再現
・「現タイトルホルダーは？」等のAI自然言語検索（β版）

```

* 告知動画の決定（WRC-14〜16）: 見本の選手は石立岳大（通算1位・優勝2回。現タイトルホルダーの紺野真太郎はシード枠からの優勝で予選に出たのは第9期までのため見本にしない）、質問は「通算成績の1位は？」、ランキングは通過回数への1回の切り替え、順位変動は第19期で、途中で一度通過ラインまで送って見せ、最終結果は1位が映る位置で止める（ouka/ の「上位が映る位置で止める」決定のまま）

手順

1. 取り込み（必要なら）の後、`python3 -m unittest discover -s scripts/tests` を回す。cloudflare との差分が `scripts/promo_video/jwrc/`・ログ・文書だけであること（生成物・ページのコードに差分が無いこと）を確かめる
2. 文書を直す:
   * `docs/notes/page-announcement.md`: 告知動画の一覧に jwrc/（`scripts/promo_video/jwrc/`、44.3秒、構成は ouka/ の告知からリーグの推移を除き、順位変動で通過ラインを見せる）を足す。投稿文の実例に上の投稿（URL・本文）を足す（ouka/ と同じ書き方。容量が足りなければ一覧に1行だけ足し、実例は `docs/notes/jwrc-top.md` に置く）。「告知動画の型」に、表の途中の線（通過ライン・降級ラインなど）を見せたいときは、途中で一度送って見せてから上位の位置に戻して止める形を1行で足す
   * `docs/notes/jwrc-top.md`（または decisions の WRC の節）に「告知動画」の節を足す: 動画の長さ・場面と秒・字幕の全文（WRC-16 のログのとおり）、質問と答え、見本の選手とその理由、投稿の URL
   * 上の「決定」を docs/decisions の WRC の決定と同じ置き場所に足す
   * 容量の検査（`check-assets.yml` と同じもの）を手元で回し、直した文書の前後のサイズと警告域・上限との差をログに書く。上限を超えるなら書かずに止まる
3. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。Workers Builds・check-assets の成否を書く。#599（クローズ済み）に告知の完了を1件コメントする（投稿の URL、末尾に `Chat-Ref: CHAT-1019-WRC-18`）

止まる条件

* テストが落ちて直せない。容量の検査が上限に入る
* 取り込みで、上の解き方で解けない衝突が出る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。CHAT-1019-WRC-14・WRC-15・WRC-16 のログの状態を「完了（CHAT-1019-WRC-18 でマージ）」の趣旨にする（cloudflare にあるログは、マージする作業ブランチ側で直す）。CHAT-1019-WRC-13 の状態（告知待ちの判断待ち）も「完了（告知は CHAT-1019-WRC-18）」の趣旨にする
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRC-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRC-18 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7b7fe6f5）: https://github.com/retroeater/mj-logs/tree/main/guide/7b7fe6f5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
