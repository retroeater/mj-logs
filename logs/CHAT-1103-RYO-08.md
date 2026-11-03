# CHAT-1103-RYO-08

- 着手日時: 2026-11-03
- 対象issue: #657（コメントのみ）
- ブランチ: work/1103-ryo8
- 着手時HEAD: 15de4f915

## 指示

【Claude作成】Claude Code 向け指示：r/ の中身をスプレッドシート「R_良栄」から作る土台を作る（ドライブの「Claude受渡用」の読み取りの確認を含む。見た目は変えない・未公開のまま）
Chat-Ref: CHAT-1103-RYO-08
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1103-RYO-07 は完了・マージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1103-ryo8 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-ryo8 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
r/ を将来も出来事を足しながら育てられるように、中身（プロフィール・出来事・駅・路線・ライフライン・素材）をスプレッドシート「R_良栄」から読んで作る土台を作る。今の r/ の見た目（屋外の空間・一覧）は変えず、今コードに直接書いている値をシートからの読み取りに置き換える。地下鉄の路線図・NY の散歩道・トライベッカなどの新しい場面は次の指示で作る。

### 決定（2026-11-03、平野さん）
- r/ はイベントを足しながら継続的にメンテナンスする。毎回最初から作り直して既存の部分が変わってしまうのは避けたい
- 中身はスプレッドシート、見た目（駅の中の配置・作り込んだ場面）はコードに分ける。スプレッドシートの名前は「R_良栄」（【本番環境】の下）。平野さんが作り、読み取り用のサービスアカウントに閲覧者、書き込み用のサービスアカウント（live-channel-writer）に編集者で共有済み
- 初版のデータはチャット側が作り、平野さんがインポート済み（シートは 説明・基本・出来事・駅・路線・ライフライン・素材 の7つ）
- 写真はドライブのフォルダ「Claude受渡用」（【ryoei.pro】の下）で受け渡す。読み取り用のサービスアカウントに閲覧者で共有済み。X のアーカイブ（写真のフォルダと投稿の一覧）と、ICU 高校の特別授業のスライドの PDF を平野さんが置く（スライドは置き済み、アーカイブは届き次第）
- 路線図は特別授業のスライドの「点と点をつなぐ（人生を複線化する）」の図にする。ライフラインチャートの谷は地下深く、山は地上の高架にする。家族の出来事は言葉で出さず、灯りや物だけで示す。授業の後半（不条理・VUCA・プリンシプル・型）は入れない（これらの見た目は次の指示）

### 前提（チャット側。平野さんの決定ではない）
- スプレッドシートの探し方: 指示文に ID は書かない。読み取り用のサービスアカウントで Drive API（読み取りのスコープ `drive.readonly` か `drive.metadata.readonly`）を使い、名前「R_良栄」のスプレッドシートと名前「Claude受渡用」のフォルダを探して ID を取り、`scripts/` の定数に置く（ログ・issue・ジョブのログには ID を書かない）。Drive API が GCP で有効でない・スコープが通らないときは、何が要るか（平野さんの手作業）を書いて止まる（docs/notes/sheets-reader.md の形で手順を書く）。鍵は Actions にしか無いので、確かめと生成は `regenerate-page.yml`（作業ブランチの ref・入力 `check`・`target_page: ryoei_pages`）など既存のワークフローの手動実行で行う
- シートの列（チャット側が作った初版。実物を読んで食い違えば実物に合わせ、ログに書く）: 説明（シート・列・意味）／基本（項目・値・表示・出典・備考）／出来事（ID・開始・終了・駅ID・種類・題・説明・見せ方・表示・出典・備考）／駅（ID・名前・時代・見せ方・出典・備考）／路線（ID・名前・色・駅の並び・出典・備考）／ライフライン（年・高さ・ラベル・出典・備考）／素材（ID・出来事ID・種類・出どころ・題・使い方・表示・出典・備考）。各シートの行数を `## 経過` に書く（チャット側が作った時点の行数: 基本17・出来事38・駅40・路線13・ライフライン15・素材27。平野さんが直していれば変わる）
- 読み取りと検査（`scripts/lib/` に読み取りの関数、検査は `scripts/tests/`）: ID の重複、出来事の駅ID・素材の出来事ID・路線の駅の並びの参照切れ、日付の書式（YYYY／YYYY-MM／YYYY-MM-DD／空）、見せ方・表示の値。当たれば生成を止め、行と理由を書く。表示＝N の行は出さない
- 家族の出来事の扱い: 見せ方が「灯り」の行と駅は、題・説明・駅名を公開されるファイル（HTML・JSON）に一切書き出さない（年と見せ方と ID だけ）。これを検査で確かめる（「結婚」「長男」などの語が `r/` の生成物に出ないこと）
- 置き換える範囲: 今 `scripts/generate_ryoei_pages.py` に直接書いている名前・読み・肩書き・SNS・自己紹介・数字で見る・出演7本を、シートの「基本」と「素材」（使い方＝スクリーン・看板・リンク）から読む形にする。空間（`assets/ryoei/world.js`）が読むデータは、生成時に JSON（例 `r/world-data.json`）に書き出して渡す形にし、今の場面の見た目が変わらないこと。出来事・駅・路線・ライフラインは JSON に書き出すだけで、まだ画面には出さない（次の指示で路線図に使う）
- 既存が変わらないための決まり（docs/notes/ryoei-world.md に書く）: ID は変えない・使い回さない、写真の線画は素材 ID から名前を決めて一度だけ作り、元が変わったときだけ作り直す（仕組みは次の指示で使うので、決まりだけ書く）、駅の中は番号付きの置き場所で、新しい物は空きに入る
- 毎朝の再生成: r/ はシートを読むようになるので、毎朝の再生成（`update-live-channel.yml` の `target_page`）に `ryoei_pages` を戻す（RYO-06 でほかのページのデータを読まなくなったため外したが、平野さんの最初の決定〈RYO-02「毎朝の再生成の対象に r/ を足す」〉に戻る）。docs/notes/github-actions.md の表も直す
- ドライブの「Claude受渡用」: 中のファイルとフォルダの名前・種類・大きさの一覧だけを `## 経過` に書く（中身は読まない。PDF だけは開けるかを確かめ、ページ数を書く。リポジトリには入れない）
- 見た目が変わらないことの確かめ: 直す前と後の `r/index.html` の一覧の部分と、空間の最初の画面・屋外上映の場の画面（360px）を並べて撮り、`docs/logs/CHAT-1103-RYO-08/` に置く。差分はシートから読んだことで説明できるもの（並び・文言のゆれ）だけで、ほかが出たら止まる

## 手順
1. #657 に着手中コメントを書く。上の Drive API の探し方で「R_良栄」と「Claude受渡用」に届くかを、作業ブランチでの手動実行で先に確かめる（届かなければ止まる）。シートの列と行数、フォルダの一覧を `## 経過` に書く
2. 読み取り・検査・生成の置き換え・JSON の書き出し・毎朝の再生成への追加・docs（ryoei-world.md・github-actions.md・decisions）を作り、`regenerate-page.yml` の手動実行で `r/` を作り直す
3. 検査と画面の比べを流し、ブランチ名の別名 URL でプレビューできる状態にして止まる

## 止まる条件
- Drive API・サービスアカウントで「R_良栄」か「Claude受渡用」に届かない（要る手作業を書いて止まる）
- シートの検査（重複・参照切れ・書式）に当たる行がある（行と理由を書いて止まる。平野さんがシートを直す）
- 家族の出来事の題・説明・駅名が生成物に出る
- 見た目の差分がシートから読んだことで説明できない
- テスト・`check_asset_limits.py`・手動実行が今回の変更で失敗する（一時的な接続の失敗は、失敗したジョブだけを 1 回再実行して success なら止まらない）
- 新しい依存（pip・npm）・新しいワークフローが要る（足さずに止まる）
- 未マージの work/ ブランチが `scripts/generate_ryoei_pages.py`・`assets/ryoei/`・`scripts/regenerate.py`・`.github/workflows/update-live-channel.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-RYO-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-RYO-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1103-ryo8
- ログ: https://github.com/retroeater/mj/blob/work/1103-ryo8/docs/logs/CHAT-1103-RYO-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-ryo8
- 確認用URL:
- マージ: 未
- issue: #657
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9a9f47ee）: https://github.com/retroeater/mj-logs/tree/main/guide/9a9f47ee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a9f47ee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
