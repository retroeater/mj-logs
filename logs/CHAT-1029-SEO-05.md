# CHAT-1029-SEO-05

- 着手日時: 2026-10-29
- 対象issue: #5・#560（コメント）
- ブランチ: work/1029-seo2
- 着手時HEAD: c5b9e665c（origin/cloudflare は 4aa1e3f28）

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1029-SEO-02 の作業ブランチ work/1029-seo2 を cloudflare へマージし、残り（S3・S5(e)）を issue へ移す
Chat-Ref: CHAT-1029-SEO-05
マージ: 承認済み（チャットで。2026-10-29、平野さん「マージ→よい」）
貼る時機: いつでも（SEO-02 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo2 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1029-seo2 を続けて使う（SEO-02 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1029-SEO-02 の変更（S1・S4・S5 の (a)〜(d)・(f)）を cloudflare へマージする。SEO-02 で残った S3（Mつくの title）と S5(e)（ouka/・jwrc/・wrcr/ の「未公開」の記述）を issue へ移し、SEO-02 のログを閉じられる形にする。

### 決定（2026-10-29、平野さん）
- SEO-02 の変更を cloudflare へマージしてよい（S1・S4・S5 の (a)〜(d)・(f)。差分は index.html・llms.txt・docs/notes の3文書と docs/decisions・docs/logs）
- Mつくの「チーム名」が空の5回は、チーム名が無い回である（シートの値は正しい。入れる値は無い）
- S5(e) は work/1028-lgn のマージの後に直す。docs/handover.md 2章「配信」の canonical の記述（「例外は wayhome/ の個別ページ」）も実態と合わないので、一緒に直す

### 前提（チャット側。平野さんの決定ではない）
- 承認の基準: マージするのは SEO-02 のログの `## 報告` の「判断が必要なこと」の1行目に挙げた範囲だけ。取り込んだ後の差分（`git diff origin/cloudflare...HEAD --stat`）が index.html・llms.txt・docs/notes/cloudflare.md・docs/notes/ogp.md・docs/notes/redirects.md・docs/decisions/・docs/logs/ の範囲に収まっていることを確かめる
- 生成スクリプトに触れないので、マージで動く再生成は無い見込み（動いたら、変わったページの範囲を報告に書く）
- S3 の続き（チーム名の無い回の title の形。チャット側の案は「作る人（公開日）」）は平野さんの判断待ち。この指示では直さない
- SEO-03（別のセッション）が docs/decisions/seo-bing.md に追記している可能性がある

## 手順
1. work/1029-seo2 に origin/cloudflare を取り込む。衝突は、docs/decisions/*.md・docs/logs/ の末尾の追記どうしと、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。取り込んだ後、上の前提の差分の範囲を確かめ、`python3 -m pytest scripts/tests -q`（CLAUDE.md の手順があればそれに従う）が通ることを確かめる
2. issue へ移す: #5 に S3（SEO-02 のログの「判断が必要なこと」の S3 の行を引用。チーム名が空の5回はチーム名の無い回であること〈上の決定〉と、title の形は判断待ちであること）、#560 に S5(e) と handover 2章の canonical の記述（work/1028-lgn のマージの後に直す。上の決定）をコメントする（本文・状態・ラベルは変えない）。SEO-02 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1029-SEO-05` を足し、S3・S5(e) の行に移した issue の番号を書く
3. 上の「決定」を docs/decisions/README.md の書き方に従って分野のファイル（docs/decisions/seo-bing.md）に足し、ログを仕上げて `git push origin work/1029-seo2:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。本番（`?v=<未使用の値>` 付き）で `/`（`<html lang="ja">`）と `/llms.txt`（Mつく・プロテストの各話の行）を確かめ、ログに書く。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 取り込みで、上の手順1で解いてよいもの以外の衝突が出た
- 取り込んだ後の差分に、前提の範囲の外のファイルがある
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて本番の確かめへ進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1029-seo2
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo2/docs/logs/CHAT-1029-SEO-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo2
- 確認用URL: なし
- マージ: 未
- issue: 作業中
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4aa1e3f2）: https://github.com/retroeater/mj-logs/tree/main/guide/4aa1e3f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74d7dc08.md
