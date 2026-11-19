# CHAT-1119-CFX-02

- 着手日時: 2026-11-19
- 対象issue: #601
- ブランチ: work/1119-cfx
- 着手時HEAD: f13217ad9

## 指示

【Claude作成】Claude Code 向け指示：認証情報の台帳（#601）に Cloudflare の User API Token `mj-builds-read` をコメントで足す
Chat-Ref: CHAT-1119-CFX-02
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1119-cfx を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1119-cfx origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1119-CFX-01 の報告で残った「#601（認証情報の台帳）に `mj-builds-read` を足すか」に答える。台帳にコメントで足す。コード・docs は変えない（変わるのはログだけ）。

### 決定（2026-11-19、平野さん）
- #601 の台帳に Cloudflare の User API Token `mj-builds-read` を足す（コメントで）

### 前提（チャット側。平野さんの決定ではない）
- 足す中身（CHAT-1119-CFX-01 で docs/notes/cloudflare.md「APIトークンの棚卸し」に書いた内容が正。そこから写し、食い違えば docs の方に合わせる）: 名前 `mj-builds-read`、種類 User API Token（平野さんのアカウントに紐づく）、権限 Account.Workers Builds Configuration（読み取り）、使う所 Secret `CF_BUILDS_TOKEN`（`check-build-failure.yml`、#634）、作成は 2026-10 下旬（ダッシュボードの「22d ago」、2026-11-19 時点。申告値）、扱い「削除しない」（2026-11-19 の決定、docs/decisions/cloudflare.md の CFX-01 の節）。Secret の元のトークン名は GitHub から読めず、対応は最終使用の時期による推定であることも書く
- #601 の台帳の今の形（表・列）は読んでいない（要確認）。台帳の形に合わせて書く。台帳に `mj build token`（Workers Builds が使うもの）が無いなど、ほかの Cloudflare のトークンの漏れに気づいたら、足さずに「判断が必要なこと」に書く
- トークンの値・アカウントのメールアドレスは書かない（mj-logs に写るため）

## 手順
1. #601 の状態・本文・コメントを読む。Closed なら止まって報告する。台帳がどこ（本文・コメント）にあり、どの形かを確かめる
2. 台帳の形に合わせて `mj-builds-read` の行（または項目）を、#601 へのコメントで足す。末尾に `Chat-Ref: CHAT-1119-CFX-02` の行を書く。issue の本文・状態・ラベルは変えない
3. 書いたコメントの URL と中身をログに引用する

## 止まる条件
- #601 が Closed、または台帳がどこにあるか分からない
- 台帳にもう `mj-builds-read`（または `CF_BUILDS_TOKEN` の元のトークン）がある（同じ趣旨なら足さず、既存の記述を報告する）
- 他セッションの着手中コメントが #601 にある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1119-CFX-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1119-CFX-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f13217ad）: https://github.com/retroeater/mj-logs/tree/main/guide/f13217ad

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c2fd0dd9.md
