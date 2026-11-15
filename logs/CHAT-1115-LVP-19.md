# CHAT-1115-LVP-19

- 着手日時: 2026-11-15
- 対象issue: #362
- ブランチ: work/1105-lvp-auto
- 着手時HEAD: afb20261b

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1105-LVP-18（/live の掲載を自動で決める仕組みを作り、試しで止める）を、【3】の直しを確かめてから続ける Chat-Ref: CHAT-1115-LVP-19 マージ: 判断待ちで止まる（試しの結果を平野さんが見てから決める） 貼る時機: 平野さんが【3】の重なった27行を直した後（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lvp-auto の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-lvp-auto（CHAT-1105-LVP-18 のログがある）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1105-LVP-18 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1115-LVP-19` を足す。

目的
CHAT-1105-LVP-18 は、平野さんが【3】に入れた27行が、もとからある空欄の行を埋めずに末尾（行4377〜4403）へ重ねて足されていたため、0章で止まった。平野さんが直した後に、LVP-18 の目的・決定・前提・手順・止まる条件・完了条件をそのまま行う。
決定（2026-11-15、平野さん）

* LVP-18 の「決定」はそのまま（指示文は LVP-18 のログの `## 指示`。貼ったときに改行と見出しの記号が落ちているが、文面は同じ）
* 【3】の重なった27行は、平野さんが直した（足された行の掲載の値を、もとからある行に入れ、足された行を消す）

前提（チャット側。平野さんの決定ではない）

* 27本の対応と値は LVP-18 のログの `## 経過` の表のとおり。値は Y 21本・N 6本（同じログの `## 報告` の「Y 19本・N 8本」は表と合わない。表が正しいと見ている。要確認）
* 平野さんの直しは 2026-11-16 04:00 JST の毎朝の再生成より前の予定。間に合わず live/ の生成が止まった run があれば、その run と、直した後の run の結果を書く

手順

1. 【3】を読み、LVP-18 の表の27本がそれぞれ1行だけになっていること（行4377〜4403 が消えている）と、もとからある行の掲載が表の「足された行の掲載」と同じ値であることを、動画ID で確かめる。`--check live_pages` を回し、止まる理由が出ないことを確かめる
2. LVP-18 の手順1〜3を行う（0章の【3】の確かめは、上の手順1で置き換える）
3. `## 報告` は LVP-18 の完了条件のとおりに書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 手順1で、27本のどれかが2行あるか見つからない、または掲載が表と違う（違う行を全部書いて止まる。直すのは平野さん）
* `--check live_pages` が止まる理由を出す
* LVP-18 の「止まる条件」に当たる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-LVP-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-LVP-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1115-LVP-19` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。作業ブランチ work/1105-lvp-auto はリモートにあり、origin/cloudflare は HEAD の祖先
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-15 15:29 JST

## 報告

- 状態: 作業中
- ブランチ: work/1105-lvp-auto
- ログ: https://github.com/retroeater/mj/blob/work/1105-lvp-auto/docs/logs/CHAT-1115-LVP-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lvp-auto
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 73d370b5）: https://github.com/retroeater/mj-logs/tree/main/guide/73d370b5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/73d370b5.md
