# CHAT-1013-SNM-15

- 着手日時: 2026-10-13
- 対象issue: なし（調査のみ）
- ブランチ: work/1013-snm-check
- 着手時HEAD: 109374aa0

## 指示

【Claude作成】Claude Code 向け指示：「プロ」の「表示」を N にしたときの扱いを全部の読む所で確かめる。正本の「リーグ」タブを読む所・引く数式を洗い出し、別ブックの「リーグ」タブと中身を比べる（調査のみ。コードもシートも変えない） Chat-Ref: CHAT-1013-SNM-15 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-snm-check の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜14 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。work/1013-snm-check を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-snm-check origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
(1) 平野さんは「プロ」の「表示」を、名簿にはいるがまだ本番に出したくない人（公開の時機を調整したい人）に N を入れて使う。N の人が「シートに存在しない行」として扱われるか（どのページ・スクリプトにも出ないか）を、全部の読む所で確かめる。(2) 平野さんは正本の「リーグ」タブを、別のブックの「リーグ」タブに移したい。今「リーグ」タブが使われているか（コード・数式）を確かめ、使われていれば読む先を変えるための材料（直す箇所・中身の比較）を揃える。この指示ではコード・生成物・シートは変えない。
決定（2026-10-13、平野さん）

* 「プロ」の「ソートキー」はシートで並べ替えに使うので残す。「表示」も残す（名簿にはいるが本番に出す時機を調整したい人に N を入れる）
* 正本の「リーグ」タブを、次のブックの「リーグ」タブに移す。使われていれば読む先を変える: https://docs.google.com/spreadsheets/d/10g_Xub35Od6vg8zFlKuB-9Kwgyg-HapWWsnfMur7J34/edit?gid=1405225134#gid=1405225134 （ID は URL のとおりに写し、手で打ち直さない）

前提（チャット側。平野さんの決定ではない）

* 「プロ」は `lib/pro_sheet.py` の `fetch_pros()` が読み、在籍は「表示」= Y で絞る（CHAT-1010-RDN-04 のログ）。ただし名簿のブック（`lib/meibo.py`）を直接読む所（`check_meibo.py`・`sync_birthday_calendar.py`・jpml_pros の英字の姓名・所属、houou/ の読み・ローマ字など）や、SNS ブックの【1】（「プロ」の在籍者から作る）・【2】（一覧から消えた人は「一覧に無い」）の扱いで、N の人がどう扱われるかは未確認（要確認）
* 正本の「リーグ」タブは `lib/pro_stats.py` が成績の集計に読んでいる（CHAT-1010-RDN-07 のログ。14行、見出しの並びで鳳凰と桜花の組を引く）。ほかに読む所や、正本のほかのタブ（「鳳凰」の「リーグキー」など）の数式が引いているかは未確認（要確認）
* 別のブックのファイル名・タブの並び・共有の設定は、このチャットでは読んでいない（要確認）
* 使う skill は無い

手順

1. 「表示」= N の扱い: リポジトリ全体（`scripts/`・`.github/workflows/`・ページ側の JS）で、「プロ」と名簿のブックを読む所を洗い出して表にする（在籍を何で決めているか: 「プロ」の表示 = Y か、名簿にいることか）。そのうえで、名簿にいる在籍者1人の「表示」を N にした模擬（読み取りの差し替え。シートは変えない）で、全ページの生成と、名簿の検査・誕生日カレンダーの同期（書き込まない形）・SNS ブックの毎日の更新（dry-run）・写真の検知・道場部ゲストなどを動かし、その人がどこに出る・出ないか、止まるか、検査の issue に何が出るかを表にする。「シートに存在しない行」として扱われない所（その人が出てしまう・止まる・知らせが出る）を一覧にする。カレンダーなど本番に書く処理は、書き込まない形で判定だけを見る
2. 「リーグ」タブ: リポジトリ全体で正本の「リーグ」タブを読む所を洗い出し、読む列・使い道を表にする。正本を xlsx でエクスポートし（CHAT-1011-RDN-10 と同じ方法）、ほかのタブの数式が「リーグ」を引いているか（タブ・セル範囲・数式の例・件数）を書く。別のブックを生成と同じ経路で2回読み、ファイル名・タブの一覧・「リーグ」タブの見出し（並び順のまま）・行数を書き、正本の「リーグ」タブと値を比べる（違いを行ごとに書く）。別のブックが「リンクを知っている全員が閲覧可」か（ログインなしで読めるか）も書く
3. まとめ: ログの `## 報告` の項目の後ろに、(1) N の人の扱いの結論（「シートに存在しない行」として扱われるか、そうでない所の一覧と、直すならどこか）、(2) 「リーグ」タブを読む先を変えるときに直す箇所（コード・数式）と、正本の「リーグ」タブを消す前にすること、を書く。docs/decisions/pros.md に上の「決定」を足す

止まる条件

* 別のブックが読めない（ID を確かめたうえで、読めなかったことを書いて止まる。手順1は終えてから止まってよい）
* 「プロ」の行数が 1,000〜1,300 を外れる、2回の読みで変わる
* コード・生成物・シートを変える必要が出た（変えずに書く）
* docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た（変えずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の表（読む所・模擬の結果）、手順2の表（読む所・数式・別のブックの見出しと比較）、`## 報告` の後ろのまとめがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1013-snm-check
- ログ: https://github.com/retroeater/mj/blob/work/1013-snm-check/docs/logs/CHAT-1013-SNM-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-check
- 確認用URL:
- マージ: 未
- issue: なし
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 346c65b1）: https://github.com/retroeater/mj-logs/tree/main/guide/346c65b1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
