# CHAT-1019-ENG-01

- 着手日時: 2026-10-19
- 対象issue: 未定（手順1で検索・起票）
- ブランチ: work/1019-eng
- 着手時HEAD: b8020e176

## 指示

【Claude作成】Claude Code 向け指示：「番組 > English」（video_en.html）を廃止し、メニュー「番組」を 放送対局・帰り道・プロテスト・Mつく の順にする Chat-Ref: CHAT-1019-ENG-01 マージ: 承認済み（チャットで。2026-10-19 に平野さんが「確かめ〈テスト・プレビュー・本番〉まで任せて cloudflare へマージしてよい」と答えた） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-eng の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-eng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-eng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「番組 > English」（`video_en.html`）を廃止し、メニュー「番組」の並びを 放送対局・帰り道・プロテスト・Mつく にする。
決定（2026-10-19、平野さん）

* 「番組 > English」を廃止する
* メニュー「番組」の並びは 放送対局・帰り道・プロテスト・Mつく（この順）
* 旧 `video_en.html` は転送せず 404 にする（「連盟 > リンク」〈jpml_links.html〉の廃止と同じ扱い）
* 女流勉強会（#576）をメニュー「番組」のどこに入れるかは、公開のときに決める（`docs/decisions/exam.md` の「女流勉強会は公開のとき『Mつく』と『プロテスト』の間に入る」を置き換える）

前提（チャット側。平野さんの決定ではない）

* 今の「番組」は 放送対局・帰り道・Mつく・プロテスト（`/exam/`）・English の順（CHAT-1019-EXM-06 のログの本番の確かめ。要確認）
* English の生成は `scripts/generate_video_en.py`、旧プロテストと同じブックの別のシートを読む（EXM-06 のログ。要確認）。シートは消さない（消すかは平野さんが後で決める。旧「テスト」シートと同じ扱い）
* 片付けの範囲は EXM-06 の `jpml_test` の片付けの表と同じ形でよい: ページ・生成・テスト・`regenerate.py` の対象・`check_unused_names.py` などの利用先・navbar・`llms.txt`・サイトマップ・ワークフローの名指し・`docs/notes/static-generation.md` の「ページの一覧」。共有の JS・CSS のコメントと、過去の記録の docs（handover-archive・site-findings など）は残してよい
* 本文に English の名前が出る決定（`docs/decisions/page-release.md` の「English | 番組 | ryoei.pro」など）は、廃止した旨が分かるように直す（要確認）

手順

1. 同じ論点の issue（English の廃止・番組メニューの並び）を検索し（クローズ済みも含めて確認）、open のものがあれば止まって報告する。無ければ起票してこの作業の issue にする。`git grep` で `video_en`・`generate_video_en`・English のメニュー項目の参照を洗い出し、扱い（消した・直した・残した）を表でログに書く。前提と食い違えば止まる
2. 廃止と並べ替え: 旧ページ・生成・テストと各参照を消す（手順1の表のとおり）。`navbar.js` の「番組」を 放送対局・帰り道・プロテスト・Mつく にする。`_redirects` には足さない。`docs/decisions/` に決定を書き、`docs/decisions/exam.md` のメニューの位置の記述を上の決定で置き換える。#576 の本文のメニューの位置（EXM-06 で「Mつく」と「プロテスト」の間に直した箇所と段2のチェックリストの navbar の行）を「公開のときに決める」に直す（直前の `updated_at` を確かめてから部分置換）。`docs/notes/video-exam.md` などメニューの並びを書いた文書も直す
3. 確かめてマージする: テスト、`python3 scripts/regenerate.py all` を作業ブランチと origin/cloudflare のクローンで回し、差分が「消した旧ページ」「`navbar.js`」「`llms.txt`・サイトマップ」「手順1の表の変更」とデータの変化（両方の木で同じく変わるもの。コミットしない）だけであることを確かめる、容量の検査、`git merge-tree` で未マージの全ブランチとの衝突、work/ ブランチのプレビューで PC とスマホ幅のメニュー（番組を開いた状態）と `/video_en.html` が 404 であることを確かめて画像を `docs/logs/CHAT-1019-ENG-01/` に置く。通ればマージし（docs/notes/branch-operations.md「マージするとき」）、本番でメニューの並び、`/video_en.html` の 404、`llms.txt`・サイトマップに `video_en` が無いことを確かめる。issue に結果を書いてクローズする（残る作業があれば別の issue に起票する）

止まる条件

* 同じ論点の open issue がある、issue に他セッションの着手中コメントがある
* 前提（今のメニューの並び、生成の場所）が食い違う
* 未マージの work/ ブランチが `navbar.js` の「番組」の項目の並び・English の生成・`video_en` の参照の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更〈例: `navbar.js` の現在地の判定〉では止まらず、ログに書く）
* `regenerate.py all` の差分に、上の見込みで説明できない種類が出た（全部書いて止まる）
* マージ後の check-run が今回の変更で失敗した（無関係な失敗なら原因を報告に書いて本番の確かめ・クローズを進めてよい。今回の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-ENG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-ENG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `CHAT-1019-ENG-01` のコミット無し。`ENG` は全ブランチのコミット・docs/logs の履歴で未使用
- 作業ブランチ: ローカル・リモートとも work/1019-eng 無し → `git checkout -b work/1019-eng origin/cloudflare`
- 0. 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1019-eng
- ログ: https://github.com/retroeater/mj/blob/work/1019-eng/docs/logs/CHAT-1019-ENG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-eng
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b8020e17）: https://github.com/retroeater/mj-logs/tree/main/guide/b8020e17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0a1e266.md
