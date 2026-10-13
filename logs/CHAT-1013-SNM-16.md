# CHAT-1013-SNM-16

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-snm-league
- 着手時HEAD: 9bba7d550

## 指示

【Claude作成】Claude Code 向け指示：成績の集計が読む「リーグ」タブを正本からその他マスタに切り替え、正本の「リーグ」タブを消しても止まらない形にする。check_leagues_dropped.py の故障を直す（CHAT-1013-SNM-15 の続き） Chat-Ref: CHAT-1013-SNM-16 マージ: 承認済み（チャットで。2026-10-13 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認） 貼る時機: 平野さんがその他マスタの「リーグ」タブで、女流桜花の ID 0 の名前を「女流桜花」から「桜花」に直した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-snm-league の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜15 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。work/1013-snm-league を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-snm-league origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-SNM-15 のログの `## 報告` を読み、状態が「完了」でなければ何もせず止まる。その他マスタの「リーグ」タブを読み、女流桜花の ID 0 の名前が「桜花」になっていることを確かめる（なっていなければ何もせず止まる）。

目的
CHAT-1013-SNM-15 の調べで、正本の「リーグ」タブを読むのは `lib/pro_stats.py`（jpml_pros の成績の集計）だけで、数式は引いていないと分かった。読む先を、平野さんが移したその他マスタの「リーグ」タブに変える。あわせて、正本の「リーグ」タブ（今の先頭のタブ）を消したときに、先頭のタブの名前に頼る判定で全ページの生成が止まらない形にする。手動実行の検査 `check_leagues_dropped.py` が今の cloudflare で止まる件も直す。正本の「リーグ」タブは、このマージの後に平野さんが消す。
決定（2026-10-13、平野さん）

* 成績の集計が読む「リーグ」タブは、正本ではなくその他マスタの「リーグ」タブにする（正本の「リーグ」タブは消す）
* 女流桜花の ID 0 の名前は「桜花」にそろえる（その他マスタの「女流桜花」を平野さんが直す。jpml_pros の表示は今と同じ）
* `check_leagues_dropped.py` の故障は、この指示で直す
* このマージは、下の「止まる条件」の確かめが通れば承認する

前提（チャット側。平野さんの決定ではない）

* CHAT-1013-SNM-15 のログ `## 報告`「まとめ (2)」の直す箇所: `lib/pro_stats.py` の `fetch_sources()`・`league_maps()`（その他マスタの「リーグ」は縦の並び〈タイトル戦・リーグ名・リーグID〉）、テスト、文書。その他マスタの先頭のタブが「リーグ」になり、`lib/live.FIRST_SHEET`（「連盟プロ以外」）と実際の先頭が食い違っている。正本の `FIRST_SHEET = "リーグ"` が4か所（`lib/pro_sheet.py`・`lib/pro_stats.py`・`lib/houou_tab.py`・`generate_houou_race.py`）にあり、正本の「リーグ」タブを消すと「「プロ」シートがありません」で全ページの生成が止まる
* 先頭のタブとの比べは、gviz が無いタブ名を渡されると先頭のタブを返すことへの備え（無いタブを読んだことに気づくため）。`lib/sheets._sheet_gids()`（htmlview のタブの一覧）がある（CHAT-1012-SNM-04 のログ）
* `check_leagues_dropped.py` は `generate_houou_leagues.QUERY` が c28057dc で無くなって止まる（SNM-15 のログ「気づいたこと」）
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる。その他マスタの「リーグ」タブと正本の「リーグ」タブを読み、ID 0 の名前を除いても値の対応（鳳凰戦・女流桜花の ID とリーグ名）が同じであることを確かめる（違いがあれば行ごとに書く）
2. 直す:
   * `lib/pro_stats.py` がその他マスタの「リーグ」タブを見出しで読む形にする（テスト・文書も）
   * 無いタブを読んだことの判定を、先頭のタブの名前に頼らない形にする（タブの一覧で有無を確かめる、など。方法は任せる）。正本とその他マスタの両方で、先頭のタブを消したり並べ替えたりしても止まらないようにする。`FIRST_SHEET` の定数は要らなくなれば消し、残すなら実際の先頭と合わせる
   * `check_leagues_dropped.py` を今のコードで動く形に直す（直した後に手元で動かし、結果をログに書く）
3. 確かめてマージ:
   * (a) 今のシートで、直す前（その時点の origin/cloudflare）と直した後のコードで全ページを1ページずつ続けて生成し、差を比べる
   * (b) 正本の「リーグ」タブが無い模擬（タブの一覧と読み取りの差し替え。シートは変えない）と、その他マスタの先頭のタブを「連盟プロ以外」に戻した模擬で、直した後のコードの全ページを生成し、(a) の直した後と比べる
   * 止まる条件に当たらなければ、docs/decisions/pros.md に上の「決定」を足し、cloudflare へマージする。ログの `## 報告` の項目の後ろに、平野さんがこの後にすること（正本の「リーグ」タブを消す。消してよい時機）を書く

止まる条件

* その他マスタと正本の「リーグ」タブで、ID 0 の名前のほかに値の対応が違う（行ごとに書いて止まる）
* (a) で生成物に差がある（シートの変化で説明できる差は、直す前と後を続けて生成して出ないことを確かめる。説明できない差が1件でもあれば止まる）、または直す前は通ったページが直した後に止まる（books_pages が「書籍」の行数で止まるのは、これまでと同じなら許す）
* (b) で (a) の直した後と差がある、またはページが止まる
* 無いタブを読んだことに気づけなくなる（タブ名の誤りで別のタブの中身を読んでしまう）形になる
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の比較、直した箇所（ファイル・関数・何を変えたか）、`check_leagues_dropped.py` を動かした結果、テストの結果、(a)(b) の結果、直した文書の一覧がログにある
* マージ後の自動再生成（regenerate-page.yml、push）の run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1013-snm-league
- ログ: https://github.com/retroeater/mj/blob/work/1013-snm-league/docs/logs/CHAT-1013-SNM-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-league
- 確認用URL:
- マージ: 未
- issue: なし
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9bba7d55）: https://github.com/retroeater/mj-logs/tree/main/guide/9bba7d55

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9bba7d55/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9bba7d55/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9bba7d55/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9bba7d55/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9bba7d55/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9bba7d55/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
