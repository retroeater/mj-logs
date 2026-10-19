# CHAT-1019-OUK-24

- 着手日時: 2026-10-19
- 対象issue: #591
- ブランチ: work/1019-ouk19（CHAT-1019-OUK-23 の続き）
- 着手時HEAD: 7d13e463b

## 指示

【Claude作成】Claude Code 向け指示：女流桜花の告知動画の制作のスクリプトと文書（work/1019-ouk19）を cloudflare にマージし、告知の記録を残す Chat-Ref: CHAT-1019-OUK-24 マージ: 下の確かめが通れば cloudflare にマージする（2026-10-19、平野さんが CHAT-1019-OUK-23 の動画を確定し、X に投稿した） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk19 の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-ouk19 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。衝突したら解かずに止まる。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-OUK-23 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-OUK-24` を足す。

目的・決定（2026-10-19、平野さん）

* CHAT-1019-OUK-23 の動画（59.6秒）で確定。2026-10-19 15:00 に X に投稿した: https://x.com/retroeater/status/2112061183469535406
* 投稿文（平野さんが入力したとおり。チャット側の案から平野さんが直した）:


```
「女流桜花」をリニューアルしました。
https://ryoei.pro/ouka/

・選手ごとの期別/通算成績・リーグ推移をまとめて
・ランキング、通算成績・期最高スコア等の8部門
・リーグごとの昇降級争いをアニメーションで再現
・「現女流桜花は？」等のAI自然言語検索（β版）

```

* 告知動画で順位の表を止める画面は、上位が映る位置にする。最後に表を送って下位・最下位の行だけを映すと「なお最下位はこの人です」という見え方になるため、そうしない（OUK-21 で 16位まで映す形を試し、OUK-22・23 で 1位が映る位置に戻した）

手順

1. 取り込み（必要なら）の後、`python3 -m unittest discover -s scripts/tests` を回す。差分が `scripts/promo_video/ouka/`・ログ・文書だけであること（生成物・ページのコードに差分が無いこと）を確かめる
2. 文書を直す:
   * `docs/notes/page-announcement.md`: 告知動画の一覧・例に ouka/（`scripts/promo_video/ouka/`、59.6秒、構成は houou/ の告知＋順位変動）を足す。投稿文の実例に上の投稿（URL・本文）を足す（houou_race の実例と同じ書き方。実例が増えて容量が足りなければ、一覧に1行だけ足して実例は `docs/notes/ouka-top.md` に置く）。「告知動画の型」に上の決定（順位の表は上位が映る位置で止め、下位・最下位の行だけを映す送り方はしない）を1行で足す
   * `docs/notes/ouka-top.md`（または decisions/houou.md の OUK の節）に「告知動画」の節を足す: 動画の長さ・場面と秒・字幕の全文（OUK-23 のログのとおり）、質問と答え、投稿の URL
   * 容量の検査（`check-assets.yml` と同じもの）を手元で回し、直した文書の前後のサイズと警告域・上限との差をログに書く。上限を超えるなら書かずに止まる
3. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。Workers Builds・check-assets の成否を書く
4. issue: #591 に告知の完了を1件コメントする（投稿の URL、末尾に `Chat-Ref: CHAT-1019-OUK-24`）

止まる条件

* テストが落ちて直せない。容量の検査が上限に入る
* 取り込みで衝突する（解かずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。CHAT-1019-OUK-19・OUK-21・OUK-22・OUK-23 のログの状態を「完了（CHAT-1019-OUK-24 でマージ）」の趣旨にする（cloudflare にあるログは、マージする作業ブランチ側で直す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-24 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章・取り込み

- Chat-Ref の重複なし。「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている（1段落にまとまって貼られた）
- work/1019-ouk19 はリモートとローカルが同じ（7d13e463b）。CHAT-1019-OUK-23 のログの状態に ` / 続き: CHAT-1019-OUK-24` を足した
- `origin/cloudflare` が HEAD の祖先でなかったので merge で取り込んだ（b019b1e5b）。衝突なし
- `python3 -m unittest discover -s scripts/tests` 1,430件 OK
- 取り込んだ後の origin/cloudflare との差: `scripts/promo_video/ouka/` の6ファイル（README.md・setup.sh・build.sh・capture.mjs・compose.py・composition/index.html）、`docs/logs/` の OUK-19・21〜24、`docs/decisions/houou.md`（OUK-19・OUK-23 の節）だけ。生成物・ページのコードの差は無い

### 文書

- `docs/notes/page-announcement.md`（6,902 → 8,163 bytes。上限・警告域なし）: 投稿文の型に女流桜花の投稿の実例（URL・本文、入力したとおり）を足した。「告知動画の型」に「順位の表を止める画面は上位が映る位置にする。下位・最下位の行だけを映す送り方はしない」の1行を足した。実例の表に ouka/ の行（59.6秒、houou/ の告知の流れ＋順位変動、`scripts/promo_video/ouka/`）を足した
- `docs/notes/ouka-top.md`（8,533 → 10,415 bytes。上限・警告域なし）: 「告知動画」の節（投稿の URL、長さ・曲、質問と答え、順位変動の止め方、場面・秒・字幕の表）を足した
- 容量の検査（`check-assets.yml` の「ガイド文書のサイズを確認」と同じ判定）: CLAUDE.md 24,972・handover.md 24,006・chat-side-operations.md 22,155（警告域 26,624 / 上限 28,672）、instruction-template.md 14,325（警告域 14,336 / 上限 16,384）。どれも警告域の手前で、今回は変えていない
- CHAT-1019-OUK-19・21・22・23 のログの状態を「完了（CHAT-1019-OUK-24 でマージ）」にし、判断が必要なこと・未確認の項目・エラーを「なし」にした（cloudflare にある OUK-21 のログも、取り込んだこの作業ブランチ側で直した）

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk19
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk19/docs/logs/CHAT-1019-OUK-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk19
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ff5acfce）: https://github.com/retroeater/mj-logs/tree/main/guide/ff5acfce

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e223c8f.md
