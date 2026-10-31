# CHAT-1101-SEO-14

- 着手日時: 2026-11-01
- 対象issue: #560・#638・#113
- ブランチ: work/1101-seo14
- 着手時HEAD: d750c1c85

## 指示

【Claude作成】Claude Code 向け指示：canonical を新しい基準に合わせて付け外しし（#560・#638）、記録の3か所を直して、条件を満たせば cloudflare へマージする
Chat-Ref: CHAT-1101-SEO-14
マージ: 承認済み（チャットで。2026-11-01、平野さん「マージしてよい」。条件は下の「止まる条件」の差分の範囲。範囲の外の変化が1つでもあればマージせずに止まる）
貼る時機: いつでも（SEO-13 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1101-seo14〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-seo14 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-seo14 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-seo14 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1031-SEO-13 の案の表（`## 経過` の表1〜22）と平野さんの答えに合わせて canonical を付け外しし、基準の記録の3か所（#113・docs/notes/cloudflare.md・docs/handover.md 2章）を新しい基準に直す。#638 を閉じる。#560 は S5(e)（work/1028-lgn のマージ待ち）が残るので閉じない。

### 決定（2026-11-01、平野さん）
- SEO-13 の Q1: `saikyo/vs/` は canonical を付けない（外す）。選手の組という主題を選ぶクエリのため（2026-10-31 の基準どおり）
- SEO-13 の Q2: title/ は入口（`/title/`）だけ canonical を付けない（外す）。大会・期のページは付けたまま。入口の扱いは暫定で、#485 で旧 `jpml_titles.html` の `?name=` の受け取りをやめたら、入口にも自分自身の canonical を戻す
- SEO-13 の「案が今の canonical から変わる行」はすべて行う: 表2（houou/・ouka/・jwrc/・wrcr/ のトップ4件に付ける）・表4（同 ranking/ の35件に付ける）・表5（同 race/ の4件に付ける）・表8（saikyo/players/ から外す、#638）・表16（mtsuku/ の一覧に付ける）・表18（exam/ の一覧に付ける）
- 下の「止まる条件」の差分の範囲に収まれば、プレビューを見ずに cloudflare へマージしてよい（canonical は画面に出ないため）

### 前提（チャット側。平野さんの決定ではない）
- 付けるのは自分自身を指す canonical（`lib/page.py` の `PageMeta.canonical`。値の形は今付いているページと同じ、og:url と同じ URL）。手書きのページは無い見込み（要確認。あれば HTML を直接直す）
- 変わらない行: 表1（トップ階層、PRO 系列に任せる）・表3（各大会の players/、付けないまま）・表6・19〜22（noindex、公開のときに決める）・表7・9・11・12・14・15・17（付けたまま）
- 記録の3か所の書き方（今の文面は SEO-13 のログ `## 経過`「手順2」）:
  - docs/notes/cloudflare.md の canonical の段落: 基準を「クエリがページの主題（選手など）を選ぶページには付けない。表示の絞り込み・切り替えだけなら自分自身を付ける」に書き換え、付けないページ（トップ階層・各大会と saikyo/ の players/・saikyo/vs/・title/ の入口〈#485 まで〉・noindex のページ）と理由を書く。件数は今回の生成の後の値にする
  - docs/handover.md 2章「配信」の canonical の行: 基準の1句と、詳細は docs/notes/cloudflare.md「配信設定」を参照する形にする（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」に従う）
  - #113: 基準を置き換えたこと（2026-10-31 の決定、docs/decisions/seo-bing.md）と、今回の付け外しの要約をコメントする（閉じたまま）
- `scripts/lib/page.py:68-70` のコメントと `generate_mtsuku.py`・`generate_exam.py` の docstring の「付けない」の理由（SEO-13 の報告）も、新しい基準に合わせて直す
- docs/decisions/saikyo.md:123 の「canonical は個別ページも年度ページも自分自身」は今回も変わらない（年度・対局・卓は付けたまま）。saikyo/ のトップ・ranking/・years/ は付けたまま、players/・vs/ は外す、を決定の記録に書く
- 生成は `regenerate-page.yml` で行う（コンテナにはシートの鍵が無い）。先に `dry_run`・`compare_ref`（その時点の origin/cloudflare）で差を確かめる
- 告知の期間中のページがあれば（docs/notes/page-announcement.md）、その生成を外して進め、外したものを報告に書く
- PRO 系列（work/1030-pro など）が `jpml_pros.html` と pro/ を変えている。この指示ではそれらに触れない

## 手順
1. 生成スクリプトで canonical を上の決定のとおり付け外しし（トップ4・ranking 35・race 4・mtsuku/ と exam/ の一覧に付ける。saikyo/players/・saikyo/vs/・title/ の入口から外す）、コメント・docstring を直す。テストを足す（付ける・外すページの canonical の有無と向き先）。`python3 -m unittest discover -s scripts/tests` が通ることを確かめる
2. `regenerate-page.yml` を work/1101-seo14 で `dry_run`・`compare_ref` 付きで起動し（target は変えた生成すべて）、差の行を全件分類してログに書く（ページの種類ごとの件数: canonical の追加・削除・ほか）。見込みは、追加 4＋35＋4＋1＋1＝45件、削除 1（saikyo/players/）＋1（saikyo/vs/）＋1（title/ の入口）＝3件。下の範囲に収まれば、書く実行で生成してコミットする
3. 記録の3か所（docs/notes/cloudflare.md・docs/handover.md 2章・#113 へのコメント）を上の前提のとおり直す。上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足し（saikyo/ の分は saikyo.md にも1行）、SEO-13 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1101-SEO-14` を足す。ログを仕上げて `git push origin work/1101-seo14:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。本番（`?v=<未使用の値>` 付き）で `houou/`・`houou/ranking/total/`・`mtsuku/`（付いた）と `saikyo/players/`・`saikyo/vs/`・`title/`（無い）、`title/houou/`（付いたまま）の canonical を確かめてログに書く。#638 に結果をコメントして閉じる（「状況:」のラベルがあれば外す）。#560 に結果をコメントする（残りは S5(e)。閉じない）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 手順2の差に、次の範囲の外の変化が1つでもある（全件を書いて、マージせずに止まる）: (a) 上の決定の行のページの `<link rel="canonical">` の追加・削除（見込みと件数が違うときも、範囲の内なら件数と名前を書いて進めてよい。見込みより多い・少ないのが ±3件を超えたら止まる） (b) 生成の後に動く sitemap の `<lastmod>`（変わったページの分だけ）。ほかのシートの変化による差が混ざって切り分けられないときも止まる
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- 記録の追記先が、この指示の前提と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換えてよい）
- 未マージの work/ ブランチが、変える生成スクリプトの同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。重なる生成だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて本番の確かめへ進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-SEO-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-SEO-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 指示欄の末尾は指示文の最後の行と一致。
- 同じ Chat-Ref のコミット・ログ: なし。work/1101-seo14 はローカル・リモートとも無く、origin/cloudflare（d750c1c85）から作成。
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d750c1c8）: https://github.com/retroeater/mj-logs/tree/main/guide/d750c1c8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
