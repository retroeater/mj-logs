# CHAT-0930-DUP-12

- 着手日時: 2026-10-01
- 対象issue: なし
- ブランチ: work/0930-dup-12
- 着手時HEAD: 45ec3982

## 指示

【Claude作成】Claude Code 向け指示：サイト全体の navbar の「タイトル」を「タイトル戦」に変え、関連して直すところも直す（プレビューまで） Chat-Ref: CHAT-0930-DUP-12 マージ: 判断待ちで止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/0930-dup-12 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-dup-12 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-dup-12 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-DUP-11 のログの `## 報告` を読み、#232 のマージが cloudflare に入っていなければ止まる（DUP-11 と同じ title/ のページを生成し直すため、順番を守る）。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、navbar・共通の部品・title/ の生成に触れているものを書く。

目的
サイト全体の navbar の項目「タイトル」を「タイトル戦」に変える。あわせて、同じ項目を指す表記で直すべきところも直す。
決定（2026-10-01、平野さん）

* navbar の「タイトル」を「タイトル戦」に変える。関連して直すべきところがあれば、それも直す。

前提（チャット側。平野さんの決定ではない）

* title/ の入口の見出し・パンくず・og:title（DUP-11）はすでに「タイトル戦」。navbar だけが「タイトル」で残っているとみている（実物で確かめる）。
* 「関連して直すところ」の候補: navbar を持つすべてのページ（生成するページと手書きのページ）、フッターやサイト内のほかの案内リンクで同じ title/ を指す「タイトル」、テスト・文書（CLAUDE.md・docs/notes/）で navbar の項目名として「タイトル」と書いているところ。「タイトルホルダー」「(旧)タイトル」タブ（#473）・シート名・動画のタイトルなど、別の意味の「タイトル」は変えない。迷うものは直さずに一覧にする。
* 1文字増えるので、スマホの幅で navbar が折り返したり、はみ出したりしないかが心配（見た目は平野さんがプレビューで確かめる）。

手順

1. 調べる: navbar の定義の場所（共通の部品・各生成スクリプト・手書きのページ）と、title/ を指す「タイトル」の表記を grep で洗い、直すもの・直さないもの（理由）・迷うものに分けてログに書く。
2. 実装: 直すものを直し、テストを直す・足す。テスト・配信上限・CLAUDE.md の検証を通す。全ページを生成し直し、差分を種類に分けて書く（navbar の表記だけの変更のページ数、それ以外の差分の有無。取り込み時点のシートの変化で説明できるものはその旨を添えて書くだけでよい）。
3. プレビュー: push し、プレビューで、トップ・title/ の入口・ほかの種類のページ1つの navbar が「タイトル戦」になり、幅 360px 程度で折り返し・はみ出しが無いかを確かめて書く（画面の取得ができればログに添える）。プレビュー URL はログに書かず、最終報告の「確認用:」の行にだけ書く。判断待ちで止まる。

止まる条件

* DUP-11 のマージが cloudflare に入っていない。未マージのブランチが navbar・共通の部品・title/ の生成に触れている。
* navbar の項目名が、外部（シート・Apps Script・ワークフロー）から入ってきていて、リポジトリの中だけでは変えられない。
* 生成物に、navbar（と手順1で直すとしたもの）とシートの変化で説明できるもの以外の差分が出た。テスト・配信上限・検証が通らない。
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。報告に比較URLと、迷うものの一覧を入れ、「確認用:」の行にプレビュー URL を書く。
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-DUP-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-DUP-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-01 Chat-Ref の重複確認: DUP-12 のコミットなし。`origin/work/0930-dup-12` は無いため `git checkout -b work/0930-dup-12 origin/cloudflare` で作成

## 報告

- 状態: 作業中
- ブランチ: work/0930-dup-12
- ログ: https://github.com/retroeater/mj/blob/work/0930-dup-12/docs/logs/CHAT-0930-DUP-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-dup-12
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 34256c97）: https://github.com/retroeater/mj-logs/tree/main/guide/34256c97

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/34256c97/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/34256c97/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/34256c97/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/34256c97/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/34256c97/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/34256c97/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14245a4d.md
