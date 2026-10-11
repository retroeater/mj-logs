# CHAT-1011-RDN-13

- 着手日時: 2026-10-11
- 対象issue: なし
- ブランチ: work/1011-rdn-x
- 着手時HEAD: 6dc8aab7

## 指示

【Claude作成】Claude Code 向け指示：乗っ取られた X アカウント（吉田彩乃さん）へのリンク・画像が本番に残っていないかを確かめ、全ページを再生成して消す
Chat-Ref: CHAT-1011-RDN-13
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（生成物の更新は `regenerate-page.yml` の手動実行に任せ、このブランチでは生成物をコミットしない。判断が残れば状態は判断待ち）
貼る時機: 平野さんが「プロ」シートの吉田彩乃さんの行の「X ID」「X画像」をクリアしたと、チャットで伝えた後
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-rdn-x の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1011-rdn-x を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-rdn-x origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
吉田彩乃さんの X アカウント（ID `ayanooo0826`）が乗っ取られたため、平野さんが SNS ブックと「プロ」シートの値をクリアした。本番（ryoei.pro）に、このアカウントへのリンクとプロフィール画像が残っているページを洗い出し、全ページを再生成して消す。あわせて、X の ID・画像を「プロ」シートから読んでいるページと SNS ブックから読んでいるページがどう分かれているかを記録する（SNS 移行の引き継ぎに使う）。
決定（2026-10-11、平野さん）

* 吉田彩乃さんの X アカウントは乗っ取られたため、X の ID・画像を一時的にクリアした（SNS ブック側。「プロ」側はこの指示の前にクリアする）

前提（チャット側。平野さんの決定ではない）

* CHAT-1011-RDN-12 のログ: `jpml_pros.html` の X のセルは SNS ブックの値の変化で空になった（jpml_pros は SNS ブックを読む）。「プロ」の「X ID」には `ayanooo0826` が残っていた（その後に平野さんがクリアした。要確認）
* CHAT-1010-RDN-01 のログ「手順3」の時点では、houou_race・saikyo/・live/・books/・title/・帰り道・jpml_test・誕生日・道場部ゲストが「プロ」の X ID（I）・X画像（J）を読んでいた。その後 XAP 系列（SNS ブック、#514）でどこまで SNS ブックに切り替わったかは、このチャットでは未確認（要確認）
* X の画像 URL は `pbs.twimg.com/profile_images/…` の形で、ID とは別の文字列。生成物にはどちらも入りうる

手順

1. 本番の洗い出し: その時点の origin/cloudflare の生成物（docs・scripts を除く全ファイル。`houou/`・`title/`・`saikyo/`・`live/`・`wayhome/`・`dic/`・JSON を含む）を `ayanooo0826` と、吉田彩乃さんの行に入っていた X画像の URL（SNS ブック・「プロ」の変更履歴から取れなければ、RDN-12 のログや直近の生成物の `pbs.twimg.com` の URL で当たりを付ける）で grep し、ファイルごとに件数を書く。本番の URL（`?v=<未使用の値>` を付ける）でも、代表的なページ数件で残っていることを確かめる。
2. 読み元の表: 「プロ」シートの「X ID」「X画像」と SNS ブックのどちらを読んでいるかを、X を出すスクリプトごとに表にする（`lib/pro_sheet.py` の呼び出しと SNS ブックの読み取り関数を grep で追う。`docs/notes/sns-book.md` があれば先に読む）。「プロ」と SNS ブックの両方の X ID・X画像が、吉田彩乃さんの行で空になっていることを生成と同じ経路で確かめる（空でなければ止まる）。
3. 再生成と確認: `regenerate-page.yml` を `target_page` = `all` で手動実行する（`docs/notes/static-generation.md`「ワークフローを手動実行するとき」を先に読み、実行元のブランチは cloudflare にする）。完了後に cloudflare を fetch し、手順1の grep をやり直して 0件になったことを確かめる。0件にならないページがあれば、そのページの読み元（手順2の表）と、何をクリアすれば消えるかを書く。本番の URL でも手順1と同じページで消えたことを確かめる。

止まる条件

* 「プロ」または SNS ブックの吉田彩乃さんの行に X ID・X画像の値が残っている（どちらに何が残っているかを書いて止まる。平野さんがクリアする）
* `regenerate-page.yml` の実行が失敗した（失敗したページと理由を書いて止まる。既知の失敗〈books_pages〉だけなら進めてよい）
* 変更が `docs/logs/`・`docs/decisions/` の外に及びそうになった（コードは変えない。読み元を SNS ブックに切り替える実装は XAP 系列の作業）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。手順2の表は `## 報告` にも載せる（引き継ぎに使う）
* マージは冒頭の「マージ:」の行のとおり（ログのみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-RDN-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-RDN-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き
- ブランチ: work/1011-rdn-x はローカル・リモートとも無し。`git checkout -b work/1011-rdn-x origin/cloudflare`（6dc8aab7）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

## 報告

- 状態: 作業中
- ブランチ: work/1011-rdn-x
- ログ: https://github.com/retroeater/mj/blob/work/1011-rdn-x/docs/logs/CHAT-1011-RDN-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-rdn-x
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 81c65eab）: https://github.com/retroeater/mj-logs/tree/main/guide/81c65eab

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/81c65eab/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
