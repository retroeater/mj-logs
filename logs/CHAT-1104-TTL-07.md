# CHAT-1104-TTL-07

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: c853e6aea

## 指示

【Claude作成】Claude Code 向け指示：title/ の期ページの順位の右端をそろえ、大会ページ・期ページの検索欄を消し、「松本ちづる」の SNS ID を反映して再生成・マージする Chat-Ref: CHAT-1104-TTL-07 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1104-TTL-06 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番の title/ を平野さんが見た指摘3点を直す（例: https://ryoei.pro/title/pro-queen/24.html）。
決定（2026-11-04、平野さん）

* 期ページの決勝メンバーの行で、› の無い行（X の無い選手。例: 第24期プロクイーンの4位）は順位が右端に寄ってしまう。› の有無にかかわらず、順位の位置をほかの行と同じ右端の位置にそろえる
* 検索欄は入口（タイトル戦）にだけ残し、大会ページ・期ページ（「プロクイーン」「第24期」など）からは消す（検索の対象はどのページでも全体のため。チャット側の意見「最強戦・鳳凰戦の下のページにも検索欄は無い」にも同意）
* 「松本ちづる」の SNS ID を平野さんが追記した（SNS ブック）。再生成して反映する
* 条件つきでプレビューを見ずにマージしてよい: 全期ページで、順位の右端の位置が行ごとにずれない（› のある行と無い行の差を測って 0px）・検索欄は入口にだけ残る・そのほかの差分はシートの変化で説明できるものだけ

前提（チャット側。平野さんの決定ではない）

* 順位の右端は、› のある行の順位の右端にそろえる（› の無い行は › の幅〈と間の余白〉の分だけ空ける）。作りは任せる
* 大会ページ・期ページで検索欄を消した後の題名と行の間の余白は、入口の題名と検索欄の間と同じ値を目安にする（決まった値の表に無ければ報告に書く）。`?q=` 付きで大会ページ・期ページを開いたときの扱いは、今そうした URL が作られているか（`jpml_pros.html` などのリンク）を調べ、作られていれば入口の `/title/?q=` へ向け直す（調べた結果と処理を報告に書く）
* 「松本ちづる」の SNS ID の行の内容（X の ID・写真の URL が入ったか）は、生成と同じ経路で読んで報告に書く（要確認。値はログに書かず、入ったかどうかと、その選手が出る期ページの数だけ書く）
* 再生成は title/ の生成だけでよい。#662（Sheets API の読み取り上限〈429〉で並走する再生成がページを飛ばす件）があるので、ほかの再生成が走っている時間と重なって title/ が飛ばされたら、1回だけやり直す

手順

1. 期ページの順位の右端をそろえ、大会ページ・期ページの検索欄を消す（`generate_title_pages.py`・`assets/title.js`・`style.css`）。docs/notes/title-pages.md と docs/decisions/title.md を直す
2. title/ を生成し直してテストを通す。全期ページ（365 前後）を headless Chromium（360px、UA に mj-internal）で開き、決勝メンバーの各行の順位の右端の x 座標を測って、› のある行と無い行の差の最大を書く（0px でなければ止まる）。検索欄の数（入口 1・ほか 0）を数える。差分を種類に分けて数を書く（順位の行の CSS／HTML・検索欄の削除・松本ちづるの写真と X のリンク・`sitemap-title.xml` の lastmod・`search.json`）
3. CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」のとおり cloudflare へマージし、本番の `/title/pro-queen/24.html`（4位の順位の位置）・`/title/houou/`（検索欄が無い）・`/title/`（検索欄がある）と、松本ちづるが出る期ページ1つを確かめる（URL に `?v=<未使用の値>`）。Workers Builds の本番の check-run の成否を書く

止まる条件

* › のある行と無い行の順位の右端の差が、どれか1つの期ページで 0px でない
* 検索欄が入口以外に残る、または入口から消える
* 差分に、上の種類とシートの変化で説明できない変化がある（想定外の行を全部書いて止まる。マージしない）
* 「松本ちづる」の SNS ID が読めない（シートに入っていない・名前が一致しない）。ほかの2点は進めてよい（報告の「判断が必要なこと」に書く）
* 未マージの work/ ブランチが同じ関数・同じ CSS の行を変えている、または取り込みで衝突する（`work/1101-prp-pub` の `member_link()` の変更は、行き先だけで順位の位置に関わらなければ止まらず、ログに書く）
* テストが落ちた（自分の変更で落ちると分かっているテストは直してよい）
* マージ後の check-run・自動再生成の失敗: 今回の変更による失敗なら止まって報告する。title/ と無関係なページの生成の失敗（#533・#662 の「生成に失敗したページを飛ばしました」など）なら、原因を報告に書いたうえで本番の確かめを進めてよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: 未
- マージ: 未
- issue: なし
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c853e6ae）: https://github.com/retroeater/mj-logs/tree/main/guide/c853e6ae

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c853e6ae/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
