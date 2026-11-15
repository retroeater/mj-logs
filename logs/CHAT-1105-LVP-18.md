# CHAT-1105-LVP-18

- 着手日時: 2026-11-15
- 対象issue: #362
- ブランチ: work/1105-lvp-auto
- 着手時HEAD: 73d370b56

## 指示

【Claude作成】Claude Code 向け指示：/live の掲載を自動で決める仕組み（【2】の「掲載（自動）」・保留の常設 issue）を作業ブランチに作り、試し（dry-run）の結果で止める Chat-Ref: CHAT-1105-LVP-18 マージ: 判断待ちで止まる（試しの結果を平野さんが見てから決める） 貼る時機: 平野さんが【3】に 2026-11-05 の27行（TSV）を入れた後（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lvp-auto の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-lvp-auto を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-lvp-auto origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。【3】で `-oAfJvStOIo` の掲載が N、`WnnCehyq0yU` の掲載が Y になっていなければ（平野さんの入力がまだ）、何もせず止まる。

目的
公開している大会に新しい動画が出たとき、【3】の掲載を平野さんが毎回入れなくて済むように、掲載を自動で決め、決められないものだけ保留にして知らせる仕組みを作る。CHAT-1104-LVP-17 の調べの結果と案をもとにする。本番へ入れる前に、作業ブランチで試して止める。
決定（2026-11-04〜05、平野さん）

* 規則は CHAT-1104-LVP-17 の報告の「直した案」を採る: 公開版の生放送は題名に【無料放送】があれば Y・無ければ N（組を待たない）、ステージが予選だけの行は N、題名に「再UP」「再アップ」は保留、まとめ単位が空で題名がリーグ戦・節なら N（「プレーオフ」を含む題名は保留）、ほかは LVP-17 の案のまま（層1の「限定」Y は Y、公開の動画は Y、大会・期・ステージが読めない・配信予定は保留）。メンバー限定かどうかは層1の「限定」で見て、題名の【メンバー限定】は見ない
* 「タイトル戦」シートに「自動掲載」の列を足し、自動にする大会だけ Y にする。日本シリーズ（麻雀日本シリーズ・女流プロ麻雀日本シリーズ）・小島武夫杯帝王戦（#590）・紅龍戦（#398）は空にする。live/ のページが無い大会（JPMLリーグなど9大会）も空にする
* 自動は、導入より後に足される行だけでなく、今【3】の掲載が空欄の行すべてに当てる（「自動掲載」が空の大会の行は除く）。【3】に値がある行は上書きしない（【3】が正）
* 保留の知らせは LVP-17 の案A: 新しい常設 issue「/live の掲載の保留」を持ち、保留が残る日は本文を書き換えたうえで「今日の保留 N件」のコメントを毎日1件足す（コメントが通知のメールになる）。保留が無くなったら閉じ、また出たら開き直す
* 鳳匠戦 第1期 ベスト8 B卓の24分のメンバー限定配信 `-oAfJvStOIo` は載せない（平野さんが【3】に N を入れる）
* 世界麻雀TOKYO2025 の `9sP_ePg3xgg`（題名は【無料放送】）は今メンバー限定のコンテンツで、ページのメンバー限定の札は正しい（平野さんが YouTube で確かめた）
* 本番へ入れる前に、作業ブランチで試して止める

前提（チャット側。平野さんの決定ではない）

* 実装の置き場所の案は LVP-17 の報告のとおり: 【2】（`lib/live_candidate.py` の `HEADERS`）に列「掲載（自動）」と既存の「理由」列への理由、判定は `lib/live_layer3.py` の `opening_ids()` の隣に `auto_show()`、`write_live_channel_candidate.py` が【2】へ書く、生成側は `lib/live_layer3.merge_records()` で【3】が空欄なら（その大会の「自動掲載」が Y なら）【2】の値を使う、保留の知らせは `update-live-channel.yml` の層2の段の後に `upsert-issue` で足す（手動実行は dry-run）。実物に合わせて変えてよい（変えたら理由を書く）
* 決定で「今の空欄すべてに当てる」としたので、LVP-17 の案の「追加日が導入日以後だけ自動」の暫定と、その外すトリガは要らない
* 「タイトル戦」シートの「自動掲載」の列は、この指示ではシートに足さない（試しでは、コードの中で上の決定の大会の一覧を仮に使う）。列の位置と入れる値の表を報告に書き、平野さんが足す。足した後はシートの列を読む（仮の一覧は残さない）
* 告知動画（live/ の公開の告知）の制作はまだ始めていない。この仕組みのマージは、告知の期間の決まり（docs/notes/page-announcement.md）を見て、次の指示で決める

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。同じ主題の issue（掲載の自動・保留の知らせ）をクローズ済みも含めて検索し、あれば番号を書く（常設 issue はこの指示では作らない）。上の決定どおりに判定・【2】への書き出し・`merge_records()` の優先・保留の知らせの段を作業ブランチに作り、テストを足す（規則の各枝、【3】＞【2】の優先、「自動掲載」が空の大会、LVP-17 の3,677行の正解を使う回帰の検査〈小さな抜き出し〉）
2. 試す（シートと issue には書かない）: (a) 【3】に掲載がある全行で一致率を測り、LVP-17 の直した案の数（一致 97.9%）と比べる。(b) 今【3】が空欄の行に当て、大会ごとに Y・N・保留の件数と、Y・N・保留の全行の表（動画ID・題名・値・理由）を書く。(c) その結果で生成し、本番と比べて増える・減るページの URL を全件書く。(d) 保留の知らせの issue の本文とコメントの文面を、dry-run の出力で示す。全ページを再生成し、live/・title/・pro/ ほか掲載を読むページの差分を書く。テストを回す
3. 作業ブランチに push して止める（確認用の URL は最終報告にだけ書く）。`## 報告` の「判断が必要なこと」に、一致率、空欄の行の Y・N・保留の件数と表の要約、増える・減るページ、「タイトル戦」シートに足す「自動掲載」の列の位置と値の表、常設 issue の題・本文の案、マージの前に要るもの（シートの列・issue の作成・ワークフローの手動実行）を書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 0章の確かめに当たる
* (a) の一致率が 97.5% を下回る（不一致の上位の理由を書いて止まる）
* (b) で、「自動掲載」が空の大会（日本シリーズ・帝王戦・紅龍戦）の行に値が付く
* 【2】【3】の読み込みが失敗する、または読み直すたびに行数が変わる（件数を書いて止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/lib/live_layer3.py`・`scripts/lib/live_candidate.py`・`scripts/write_live_channel_candidate.py`・`.github/workflows/update-live-channel.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LVP-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LVP-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1105-LVP-18` のコミットは0件。`LVP` は同じセッションの続き（コンテナは作り直されていた）
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 作業ブランチ: work/1105-lvp-auto はローカルにもリモートにも無いので、`git checkout -b work/1105-lvp-auto origin/cloudflare` で作った
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-15 14:51 JST

## 報告

- 状態: 作業中
- ブランチ: work/1105-lvp-auto
- ログ: https://github.com/retroeater/mj/blob/work/1105-lvp-auto/docs/logs/CHAT-1105-LVP-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lvp-auto
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 73d370b5）: https://github.com/retroeater/mj-logs/tree/main/guide/73d370b5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/73d370b5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/73d370b5.md
