# CHAT-1113-SKP-28

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-skp-abema-regen
- 着手時HEAD: fb8971f22（着手時に origin/cloudflare を merge で取り込んで a28a048b9）

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1113-SKP-27 の続き）【3】を直した後に再生成してアマ最強位決定戦のライブ枠を確かめ、全日本プロ選手権・アマ最強位決定戦の出場者の登録と SNS の状況を一覧にする
Chat-Ref: CHAT-1113-SKP-28
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（このセッションのコミットはログだけ。SKP-26・SKP-27 のログも一緒に入る。ページの再生成のコミットは regenerate-saikyo.yml が cloudflare へ入れる）
貼る時機: いつでも（CHAT-1113-SKP-27 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1113-skp-abema-regen を続けて使う（SKP-26・SKP-27 のログがあり、判断待ちの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1113-SKP-27 のログ（docs/logs/CHAT-1113-SKP-27.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1113-SKP-28` を足す。

目的
SKP-27 で、アマ最強位決定戦（2026-11-22、ベスト16 A〜D卓）にライブ枠の「▶ 再生」が出なかった（ABEMA ブック【3】の行の鍵が卓と合わなかった）。平野さんが【3】を直したので、再生成して本番で確かめる。あわせて、新しく出た全日本プロ選手権（2026-11-15）とアマ最強位決定戦の出場者について、平野さんがマスタ登録と SNS のチェックをするための一覧を作る。
決定（2026-11-13、平野さん）

* 義兄弟合戦の3卓（p34・p35・p36）は平野さんが本番で目視して OK（SKP-27 の未確認の項目「題名の卓」はこれで済み）
* 全日本プロ選手権（2026-11-15）のページが出たのは意図どおり。その出場者のマスタ登録と SNS のチェックをしたい
* ザ・リベンジは対局者の決定（11月末の予定）まで「表示」N のまま。【3】のザ・リベンジの行が卓に当たらないのは想定どおり

前提（チャット側。平野さんの決定ではない）

* 平野さんの申告（未確認、2026-11-13）: ABEMA ブック【3】のアマ最強位決定戦の行を確かめて直した（どの列を直したか、行がもともと無かったかは伝えられていない）
* SKP-27 の生成の「動画URLのある卓」は 625（【2】の当たった卓 621 ＋ どの卓とも違う鍵の【3】の行 4）。アマ最強位決定戦の4卓が【3】で当たれば、ページに「▶ 再生」（ライブ枠）が出る
* 出場者の名前の引き方は docs/notes/saikyo-page-design.md「scripts/check_saikyo_unregistered.py」（登録が無い／重複などで使われない）。SNS の ID・写真は docs/notes/sns-book.md
* シート・ブックへの書き込みはしない（マスタ登録・SNS の入力は平野さん）

手順

1. check: `gh run list` で再生成どうしが並走しないことを確かめる（実行中があれば待つ。15分を超えたら止まる）。regenerate-page.yml を作業ブランチの ref・入力 check で、対象を最強戦のページに絞って手動実行し、止まる理由・警告を全部ログに書く（止まる理由があれば止まる）。「動画URLのある卓」を SKP-27 の 625 と並べ、アマ最強位決定戦の4卓が【3】で当たったかを書く
2. 本番への反映: regenerate-saikyo.yml を ref cloudflare、abema: update、再生成ありで手動実行し、終わるまで待つ（合計30分まで延長してよい）。再生成のコミットの変更ファイルを全部一覧にし、「アマ最強位決定戦のライブ枠」「シート側のほかの変化」「説明できない」に分ける。本番を `.html?v=<未使用の値>` 付き・UA に `mj-internal` を含めて読み、アマ最強位決定戦の4卓の「▶ 再生」がライブ枠（abema.tv/channels/…）を指していることを表にする。【3】の行のうち卓に当たらないものの数も書く（ザ・リベンジの分は想定どおり）
3. 出場者の一覧（読むだけ）: check-saikyo-unregistered.yml を ref cloudflare で、入力 dry_run の既定（true、issue を書かない）のまま手動実行する。全日本プロ選手権（2026-11-15）とアマ最強位決定戦（2026-11-22）の出場者の全員について、1人1行で次を表にする（略さず全員）: 対局・卓・名前・引けたか（「プロ」／「連盟プロ以外」／登録が無い／重複などで使われない）・X の ID の有無・写真の有無（生成物のアバターが代替か）。調べた経路（どのファイル・出力から読んだか）も書く。登録が無い人・写真の無い人は、平野さんが直す先（「連盟プロ以外」に行を足す、SNS ブックの【2】ID に入れる、など）を docs のとおりに添える

止まる条件

* 手順1の check に止まる理由がある
* 手動実行のどれかが失敗した（手順2の前なら update は実行しない。原因をジョブのログから書く）
* 手順2の後で、アマ最強位決定戦の卓のどれかに「▶ 再生」が出ない。このときはコミットを戻さず、当たらない理由を書いて判断待ちにする（【3】の行は行番号でなく卓の鍵〈年度・対局日・対局・ステージ・卓〉で示す）
* 「説明できない」変更ファイルがある（戻さず、一覧と理由の推定を書いて判断待ちにする）
* 手順3は判断待ちの理由にしない（一覧を書くだけ。手順2で止まっても手順3は行う）
* 未マージの work/ ブランチが saikyo/ の生成・scripts/lib/abema_book.py・scripts/update_abema_book.py の同じ関数を変えている（`git branch -r --no-merged origin/cloudflare` から確かめる。別の箇所なら止まらずログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue: なし（issue は変えない。check-saikyo-unregistered は dry_run なので常設 issue も書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8da336f0）: https://github.com/retroeater/mj-logs/tree/main/guide/8da336f0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8da336f0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
