# CHAT-1011-ABM-05

- 着手日時: 2026-10-11
- 対象issue: #425・#549
- ブランチ: work/1011-abm
- 着手時HEAD: 9fecbded1a70a868189be3b68c7b13c01e8122f1

## 指示

【Claude作成】Claude Code 向け指示：「最強戦」シートの M「動画URL」列を生成と同じ経路で読んで確かめる（読むだけ） Chat-Ref: CHAT-1011-ABM-05 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・シート・生成物は変えない（生成は手元で行い、コミットしない） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-abm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-abm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが「最強戦」シートに M「動画URL」列を足し、URL を入れた（2026-10-11、平野さんの申告「1行入れました」）。生成が読めるか、#549（公開）の前に確かめる。
決定（2026-10-11、平野さん）

* #549 の公開は、M 列を足して 2026年度の URL を入れてから行う

前提（チャット側。平野さんの決定ではない）

* 列の形と読み方は docs/decisions/saikyo.md の CHAT-1011-ABM-02 の grill 10 と、CHAT-1011-ABM-03 で入れた `load_videos()`（「動画URL」の見出しで読む）のとおり（要確認）
* 平野さんの申告の「1行」が、URL を入れた行の数か、列を足したことかは分からない。どちらでも、読めた事実をそのまま書く

手順

1. 「最強戦」シートを生成と同じ経路で2回読み、見出し（M が「動画URL」か、L の右か）・全行数・K=Y の行数を書く。M に値のある行を全部（年度・対局日・対局・ステージ・卓・名前・URL）表にする。卓ごとにまとめ、URL のある卓の数と、4行で値が食い違う卓を書く。URL の形（`https://abema.tv/` で始まるか、短縮 URL か、前後の空白など）を書く
2. 手元で `python3 scripts/regenerate.py saikyo_pages` を走らせ、URL のある卓のページに「ABEMA で見る」が出ること（その部分の HTML を引用）、同じ対局のページの扱い、警告の有無を書く。今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）に差分が出ないことも確かめる。生成物はコミットせず、作業ツリーを元に戻す（`git checkout -- saikyo/` のような一括の戻しではなく、生成で変わったファイルを列挙してから戻す）
3. 翌朝の毎朝の再生成（`regenerate-saikyo.yml`）でこの URL が本番の卓のページに出る見込みかを書く（コードの読みからの見込みで、本番は確かめない）

止まる条件

* シートを2回読んで行数が違う
* M 列の見出しが「動画URL」でない、または L の右にない（読めた事実を書いて止まる。シートは直さない）
* ログ以外を変える必要が出た（変えずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-ABM-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-ABM-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1fe83b8c）: https://github.com/retroeater/mj-logs/tree/main/guide/1fe83b8c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1fe83b8c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1fe83b8c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1fe83b8c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1fe83b8c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1fe83b8c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1fe83b8c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
