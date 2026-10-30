# CHAT-1030-SEO-10

- 着手日時: 2026-10-30
- 対象issue: なし
- ブランチ: work/1030-seo9
- 着手時HEAD: 2a0c94a51

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1030-SEO-09 の前提 (2) の言い方を直して申送りを docs/decisions/operations.md に記録し、cloudflare へ入れる
Chat-Ref: CHAT-1030-SEO-10
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions/operations.md だけ）
貼る時機: いつでも（SEO-09 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1030-seo9 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1030-seo9 を続けて使う（SEO-09 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで docs/decisions/*.md・docs/logs/ の末尾の追記どうしが衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1030-SEO-09 は、前提 (2) の参照先が誤っていて止まった（SEO-09 のログの `## 報告`「判断が必要なこと」）。(2) の言い方を参照先に合わせて直し、SEO-09 の手順1〜3（申送りの記録と cloudflare への取り込み）を行う。

### 決定（2026-10-30、平野さん）
- SEO-09 の前提 (2) は、言い方を参照先に合わせて直し、「足さなかったもの」に残す（ガイド文書には足さない）

### 前提（チャット側。平野さんの決定ではない）
- (2) の直した文面（チャット側の案。docs/decisions/README.md の書き方に合わせて直してよい）: 「プレビューはチャット側が別名 URL を開いて確かめる（docs/notes/chat-side-operations.md『確認用 URL』）。SEO-04 では WebFetch で開けず、Chrome を試さずに平野さんに頼んだ。開けないときに WebFetch と Chrome の両方を試すのは本番の規則（同『読み方』）と同じ扱いで足り、チャット側の行動の問題で機械的に確かめられないので、規則は足さない」
- (1)・(3)・(4)・(5)、足したもの（MCK への伝言）、前回の振り返りの約束の扱いは、SEO-09 の指示文の前提のまま（SEO-09 で (1)・(3)・(4) の参照先は一致を確かめ済み）。MCK への伝言は 2026-10-30 に平野さんが送った
- 節の見出しは `## 2026-10-30（CHAT-1030-SEO-10）` にする（記録するのはこの指示。SEO-09 の決定「申送りを行う」もこの節に含める）

## 手順
1. docs/decisions/operations.md の末尾の節と docs/decisions/README.md を読み、SEO-09 の前提の中身を (2) だけ上の文面に置き換えて、`## 2026-10-30（CHAT-1030-SEO-10）` の節として足す。追記の後の行数と、上限のある文書なら残り容量をログに書く（上限が無ければ「上限なし」）
2. SEO-09 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1030-SEO-10` を足す
3. ログを仕上げて `git push origin work/1030-seo9:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- docs/decisions/operations.md に、SEO 系列の申送りの節がもうある（重ねずに止まる）
- (2) の直した文面の参照先（chat-side-operations.md「確認用 URL」「読み方」）の中身が、前提と食い違う（その点を報告に書いて止まる）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1030-SEO-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1030-SEO-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1030-seo9
- ログ: https://github.com/retroeater/mj/blob/work/1030-seo9/docs/logs/CHAT-1030-SEO-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1030-seo9
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d0635eab）: https://github.com/retroeater/mj-logs/tree/main/guide/d0635eab

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b6cb6cd.md
