# CHAT-1114-SEO-28

- 着手日時: 2026-11-14
- 対象issue: #565
- ブランチ: work/1113-seo27
- 着手時HEAD: 5b5f5af595

## 指示

【Claude作成】Claude Code 向け指示：SEO-27 の取り込みの衝突を (b) で解き、work/1113-seo27 を cloudflare へマージする（#565）
Chat-Ref: CHAT-1114-SEO-28
マージ: 承認済み（チャットで。2026-11-14、平野さん。条件は下の「止まる条件」の差分の範囲。範囲の外の変化が1つでもあればマージせずに止まる）
貼る時機: いつでも（SEO-27 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-seo27 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1113-seo27 を続けて使う（SEO-27 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みの衝突は下の「決定」の解き方で解いてよい（解いた後の該当箇所をログに引用する）。ほかに docs/decisions/*.md・docs/logs/ の末尾の追記どうし、または両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1113-SEO-27 は、cloudflare の取り込みで CHAT-1113-GDR（4文書の整理、#492）と衝突して止まった（SEO-27 のログの `## 報告`「判断が必要なこと」）。平野さんの答え (b) で衝突を解き、cloudflare へ入れる。

### 決定（2026-11-14、平野さん）
- SEO-27 の衝突の解き方は案 (b): 知見 #5（マージの可否の例に「異論がなければ推奨どおり」を足す）は今回は足さない。docs/notes/chat-side-operations.md は cloudflare 側（GDR の整理の後）の行のままにする。#5 は、守られなかった事実だけを docs/decisions に書く
- work/1113-seo27 を cloudflare へマージしてよい（下の条件で）

### 前提（チャット側。平野さんの決定ではない）
- 解き方の細目（SEO-27 の報告の案のとおり）: docs/notes/handover-archive-2026.md は両方の節を残し、cloudflare 側の「### 2026-11-13 の整理で4文書から外した記述（#492）」を先、SEO-27 の節を後に並べる。SEO-27 の節の #5 の事例の記述が「chat-side-operations.md を置き換えた」と読めるなら、「(b) により規則は足さず、事実だけ記録」に直す。docs/decisions/operations.md は両方の節を残し、SEO-27 の節の #5 の行を上の決定に合わせて直す（「同じ行の中で置き換え」の記述を残さない）
- docs/notes/chat-routines.md の3項目（#1〜#4）は衝突していない（SEO-27 のログ「3. マージ」）。cloudflare 側が 27,088 バイトに増えていたので、取り込み後に3項目がGDR の整理と食い違わないか（同じ趣旨の記述が既にあるか）を確かめ、同じ趣旨があれば置き換え・統合してよい（処理をログに書く）

## 手順
1. work/1113-seo27 に origin/cloudflare を取り込み、上の決定・前提のとおりに衝突を解く。chat-side-operations.md が cloudflare 側と同じ（`git diff origin/cloudflare -- docs/notes/chat-side-operations.md` が空）ことを確かめる。chat-routines.md の3項目を取り込み後の内容と照らす。`check-assets.yml` が見る文書の上限・純増の警告（docs/notes/chat-side-operations.md の冒頭）に当たらないことを確かめ、バイト数の前後をログに表で書く
2. SEO-27 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1114-SEO-28` を足す。`git push origin work/1113-seo27:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。#565 に、マージしたこと（#5 は (b) で規則に足さなかったこと）をコメントする（本文・状態・ラベルは変えない）
3. ログを仕上げる

## 止まる条件
- 差分（cloudflare との）に、docs/notes/chat-routines.md・docs/notes/handover-archive-2026.md・docs/decisions/operations.md・docs/logs/ 以外のファイルがある（chat-side-operations.md が差分に残るときも止まる。マージせずに止まる）
- 取り込みで、上で解いてよいとしたもの以外の衝突が出た
- chat-routines.md の3項目が取り込み後の記述と矛盾し、どちらが正か判断が要る（同じ趣旨なら止めず統合してよい）
- 文書の上限を超える、または check-assets の警告が出る
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1114-SEO-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1114-SEO-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-seo27
- ログ: https://github.com/retroeater/mj/blob/work/1113-seo27/docs/logs/CHAT-1114-SEO-28.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-seo27
- 確認用URL: なし
- マージ: 未
- issue: #565
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e8adaade）: https://github.com/retroeater/mj-logs/tree/main/guide/e8adaade

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8adaade/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8adaade/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8adaade/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8adaade/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8adaade/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8adaade/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6aa6642b.md
