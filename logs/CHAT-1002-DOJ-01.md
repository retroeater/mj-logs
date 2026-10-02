# CHAT-1002-DOJ-01

- 着手日時: 2026-10-02
- 対象issue: #390, #426
- ブランチ: work/1002-doj
- 着手時HEAD: 890dd035b03f54fc3ee89360eccb4f8ccf462782

## 指示

【Claude作成】Claude Code 向け指示：道場部ゲストの10月分がカレンダーに入らない原因を裏取りし、書き込みなしで10月分を読む Chat-Ref: CHAT-1002-DOJ-01 マージ: 承認済み（チャットで、2026-10-02）。条件: 変更が docs/（docs/decisions/ を含む）だけのとき。 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-doj の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-doj を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-doj origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、道場部ゲストの同期（`scripts/lib/dojo_guest.py`・`scripts/sync_dojo_calendar.py`・`.github/workflows/sync-dojo-calendar.yml`）か docs/handover.md に触れているものを書く。

目的
連盟サイトに道場部ゲストの2026年10月分が出ているのに「mj_道場部ゲスト」カレンダーに入っていない（#390、通知先 #426）。チャット側の推定した原因を実物で裏取りし、10月分を書き込みなしで読んで、平野さんが書き込みの可否を決められる材料（日付と名前の一覧）をログに出す。コードは変えない。
決定（2026-10-02、平野さん）

* 10月分は Claude Code が書き込みなしの手動実行で読み、結果を報告する。カレンダーへの書き込みは、平野さんが名前を確かめてから次の指示で行う。
* 再発防止は次の2つにする。(B1) 連盟サイトの見出しを「～道場部～」に直してもらうよう、平野さんが依頼する。(B2) ページに新しい月の画像があるのに道場部ゲストの見出しが見つからないときは #426 に通知する（実装は次の指示。この指示では行わない）。
* 見出しが違っても同じ月の「ゲスト」の見出しやファイル名から推定して読む案（B3）は入れない。
* 毎朝の定期実行を自動書き込みに切り替えるのは見送る（今のまま、書き込みは手動実行のときだけ）。
* B2 のコード変更は、テストが通れば cloudflare へマージしてよい（次の指示の分）。

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-02 に https://www.ma-jan.or.jp/dojo_guest.html を読んだ結果（本文をテキストに直したもので、HTML そのものは見ていない）:
   * 「日本プロ麻雀連盟本部道場 2026年10月講師 ～麻雀教室～」→ `…/wp-content/uploads/202610B.jpg`
   * 「日本プロ麻雀連盟本部道場 2026年10月ゲスト ～麻雀教室～」→ `…/wp-content/uploads/202610R.jpg`
   * 9月は 講師～麻雀教室～（202609B）・スタッフ～麻雀教室～（202609Y）・ゲスト～道場部～（202609R）・スタッフ～道場部～（202609G）の4枚
   * ページの `article:modified_time` は 2026-09-28T23:35:45+00:00
* チャット側の推定: 10月のゲストの見出しが「～道場部～」でなく「～麻雀教室～」になっているため、見出し「YYYY年M月ゲスト ～道場部～」で画像を探す同期が10月分を見つけられず、9月を最新月として「画像は前回の読み取りから変わっていません」で終わっている（CHAT-1001-GSC-01 のログの run 36800180432 と同じ終わり方）。handover.md の「10月分はまだ画像が出ておらず未読」は、見つけられていなかっただけの可能性がある。
* チャット側は 202610R.jpg の中身を開けていない。道場部ゲストの表だという根拠は、ファイル名の末尾 R が9月の道場部ゲストと同じことと、平野さんの「道場部ゲストの10月分が公開された」という発言だけ。9月分の画像は営業時間 16:30〜23:30・金曜「公式ルール」の表だった。
* 10/2 の定期実行の結果、#426・#390 の今の状態はチャット側は見ていない。
* 10月分はカレンダーにまだ入っていない見込み（平野さんの発言）。件数は確かめていない。
* 今回の読み取りは、2026-09-26 の `max_tokens` の修正後のコードで初めての読み取りになる見込み（handover.md「期限付き・確認待ちタスク」の #390 の行）。

手順

1. 原因を裏取りする。#390・#426 の本文と最新のコメント（他セッションの着手中コメントの有無を含む）、`sync-dojo-calendar.yml` の 10/1 以降の実行（特に 10/2 の定期実行）の結果を確かめて書く。dojo_guest.html を同期と同じ取り方で取得し、2026年10月の見出しの実際の文字列（空白・波線の種類を含めてそのまま）と直後の画像の URL、同期がこの HTML でどの月を最新と判断するかを、Claude API を呼ばない範囲で実際に動かして書く。クラウドから連盟サイトを取得できなければ、できなかったことを書き、ワークフローの実行ログで分かる範囲で確かめる。推定と食い違えば「止まる条件」のとおり止まる。
2. 手順1で確かめた10月のゲストの画像の URL と対象の月 2026-10 を入力にして、`sync-dojo-calendar.yml` を cloudflare で書き込みなしで手動実行する（入力の名前は yml の実物で確かめる。起動は docs/notes/cloud-sessions.md のとおり）。完了を待つのは15分まで。超えたらその時点の状態を書き「未確認の項目」に回して先へ進む。結果をログに書く: 読み取り件数、日付と名前の一覧（全件）、照合できない名前、既にある予定の件数、追加する件数、新規ゲスト（判定の根拠つき）、10月が誕生日のゲスト、画像の注記、#426 に通知が出たか。画像が道場部ゲストの表であること（麻雀教室の表でないこと）を、読み取り結果か画像の表示で確かめられる範囲で確かめ、確かめられなければその旨を書く。あわせて、次の指示で書き込むときの入力と、そのとき画像をもう一度読む（Claude API を呼ぶ）かどうかを、コードを読んで書く。実行が失敗で終わったときは、最終報告に「失敗通知のメールが届くが、この指示の試運転によるもので対応不要」と書く。
3. 結果を #390 にコメントする（クローズしない）。docs/handover.md の「最終更新」と「期限付き・確認待ちタスク」の #390 の行の今の内容を読んでから、手順1・2の結果に合わせて直す（「画像が出ていない」が事実と違っていたならそこを直す。文書の直しは writing-for-agents の skill を使う）。この指示の「決定」を docs/decisions/ の該当の分野へ足す（無ければ作る）。CLAUDE.md の検証と容量の上限を通し、マージの行のとおり cloudflare へ入れる。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）。

止まる条件

* 推定した原因と実物が食い違う（HTML に2026年10月の「ゲスト ～道場部～」の見出しがある、10/2 の定期実行が10月分を読めている、202610R.jpg が道場部ゲストの表でない、など）。分かったことを書き、手順2へ進まずに止まる。
* カレンダーへの書き込みが必要になった。この指示では、書き込みありの実行をしない。
* コードやワークフローなど docs/ 以外の変更が要ることになった。
* 0章の一覧に、道場部ゲストの同期か docs/handover.md に触れている未マージのブランチがある。#390・#426 に他セッションの着手中コメントがある。
* 検証が通らない。
* ブランチの作成や cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、原因の裏取りの結果、手順2の結果（日付と名前の一覧は「経過」にあれば報告からはその場所を指すだけでよい）、「判断が必要なこと」に10月分を書き込んでよいかを入れる。状態は、手順3まで済めば「完了」とする（書き込みの可否は次の指示で扱う）。
* マージは冒頭の「マージ:」の行のとおり。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-DOJ-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-DOJ-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 DOJ: unshallow 後、全ブランチのコミット（`Chat-Ref` を含む本文）と `docs/logs/` の履歴に `DOJ` の使用なし。`CHAT-1002-DOJ-01` のコミットも無し
- `work/1002-doj` はローカル・リモートとも無かったため `git checkout -b work/1002-doj origin/cloudflare` で作成（起点 890dd03）

## 報告

- 状態: 作業中
- ブランチ: work/1002-doj
- ログ: https://github.com/retroeater/mj/blob/work/1002-doj/docs/logs/CHAT-1002-DOJ-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-doj
- 確認用URL: なし
- マージ: 未
- issue: #390, #426
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 428dc963）: https://github.com/retroeater/mj-logs/tree/main/guide/428dc963

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/428dc963/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/428dc963/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/428dc963/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/428dc963/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/428dc963/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/428dc963/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/16ce2cf4.md
