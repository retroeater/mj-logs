# CHAT-1103-TOP-10

- 着手日時: 2026-11-03
- 対象issue: #101, #654
- ブランチ: work/1103-top
- 着手時HEAD: 0db1107c5

## 指示

【Claude作成】Claude Code 向け指示：新トップ top/ の TOP-07〜09 を cloudflare へマージし、本番で横断の質問欄を確かめる（#101）
Chat-Ref: CHAT-1103-TOP-10
マージ: 承認済み（チャットで。この指示文を貼ることが承認）。条件: (1) origin/cloudflare を取り込んだ後にテスト（`python3 -m unittest discover -s scripts/tests`）が通る (2) cloudflare との差分が TOP-09 のログ `## 経過` 5 の11ファイル（`scripts/generate_top_page.py`・`assets/top_ask.js`・`assets/top.js`・`assets/top.css`・`scripts/tests/test_top_page.py`・`top/index.html`・docs/notes/top-page.md・docs/decisions/top.md・TOP-07〜09 のログ）とこの指示のログ・決定だけで、新しい変更を含まない (3) 取り込んだ後の `regenerate-page.yml` の作業ブランチでの手動実行（`target_page: top_page`）が成功し、top/ 以外の生成物に差分が出ない。1つでも満たさなければマージせずに止まる
貼る時機: いつでも（TOP-09 と同じセッションの続きで可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1103-top を続けて使う（TOP-07〜09 の成果をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成されたページの衝突は CLAUDE.md「ブランチ運用」のとおり取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1103-TOP-09 のログの `## 報告` の状態が「判断待ち」であることを確かめ、その状態の末尾に ` / 続き: CHAT-1103-TOP-10` を足す（TOP-07 は ` / 続き: CHAT-1103-TOP-08`、TOP-08 は ` / 続き: CHAT-1103-TOP-09` が付いている見込み。付いていなければ足す）。

## 目的

未公開の新トップ `top/`（noindex・メニュー未掲載）に、TOP-07〜09 の変更（上部と横断の質問欄・3つの面のサムネイルと「もっと見る」・誕生日）を本番で入れ、横断の質問欄の答えの見え方を本番で確かめる。今のサイトのトップ `index.html` は変えない。

### 決定（2026-11-03、平野さん。TOP 系列のチャットで）

- TOP-07〜09 をまとめて cloudflare へマージしてよい（プレビューを見たうえで。マージの可否はほかの問いと分けて聞いた）
- 入力欄の中の文言は短い形「例：現鳳凰位は？」のまま（TOP-09 の判断待ちへの回答。大会別の欄の形にそろえる）
- 大会日程の行で YouTube の枠がまだ無いものは、灰色の箱（大会名の頭の1字）のまま（TOP-08 の判断待ちへの回答 A）
- TOP-07 の仮置き（一行の説明「日本プロ麻雀連盟の選手・大会・放送対局のデータ」・注意書きは入力欄の下・答えの選手のカードは頭文字の丸・大会のチップ・名前〈写真なし〉）と、TOP-08 の仮置き（誕生日を焼く範囲は今日の1日前〜8日後・「もっと見る」の形と押した後の動き・読み上げの文言・明日以降の日付の見出し・行の折り返し・箱の字）は決定とする（入力欄の文言・例のチップは TOP-09 のとおり）

### 前提（チャット側。平野さんの決定ではない）

- `assets/top_ask.js` は `assets/houou_ask.js` の写しで、一時的な重複（TOP-07 のログ。PRP 系列が `houou_ask.js` を変えている最中だったため）。外すきっかけは「質問欄を1つにまとめるとき（#654）」。この指示で #654 に1行コメントして残す（本文・状態・ラベルは変えない）
- 横断の API は AIC-20（#654 のコメント）。`ALLOWED_ORIGIN` は本番だけなので、プレビューでは答えを確かめられなかった
- 本番での確かめに送る問いは1日の上限（全体200問・IP ごとに10問）に数える。送るのは2問まで。UA に `mj-internal` を含める

## 手順

1. origin/cloudflare を取り込み（上の衝突の扱い）、マージの条件 (1)〜(3) を確かめる。`docs/decisions/top.md` に上の「決定」を足す（ログと同じコミットでよい）
2. 条件を満たせば `git push origin work/1103-top:cloudflare` でマージする（push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD`）。本番の `https://ryoei.pro/top/?v=<未使用の値>` が 200・noindex で、TOP-09 の「この版で見えるはずの印」（入力欄の中が「例：現鳳凰位は？」で例のボタンが無い）と TOP-08 の印（見出し「誕生日」の横に今日の日付・「もっと見る」・新着の番組のサムネイル）が出ていること、`https://ryoei.pro/` が今の `index.html` のままであることを確かめる（15分で反映されなければ「未確認の項目」に回して完了にする）
3. 本番の `/top/` で横断の質問を2問送り（headless Chrome で入力欄から。例「現鳳凰位は？」と「佐々木寿人の成績は？」）、答えの表示（大会ごとの改行・選手のカード・出典・データの時点）を画面で確かめてログに書く（画像は作業ブランチの `docs/logs/CHAT-1103-TOP-10/` に置いてよい）。崩れがあれば直さずに書く（直しは次の指示）。#654 に `top_ask.js` の重複と外すきっかけを1行コメントする。#101 にマージしたことをコメントし、着手中を解除する

## 止まる条件

- TOP-09 が判断待ちでない、または work/1103-top がすでにマージ済み
- マージの条件のどれかを満たさない（満たさなかった条件と中身を書く）
- 上の扱いで解けない衝突が出た
- `https://ryoei.pro/` の内容が変わった（見つけたら直さずに報告する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9a9f47ee）: https://github.com/retroeater/mj-logs/tree/main/guide/9a9f47ee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
