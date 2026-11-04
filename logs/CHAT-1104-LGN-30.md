# CHAT-1104-LGN-30

- 着手日時: 2026-11-04
- 対象issue: #663
- ブランチ: work/1104-lgn-gnotice（origin/cloudflare から作った）
- 着手時HEAD: 8c7dd9a31

## 指示

【Claude作成】Claude Code 向け指示：#663 を log/ の末尾の「利用条件」（Google の2文）に替え、/policy/ は作らない
Chat-Ref: CHAT-1104-LGN-30
マージ: 承認済み（チャットで）。条件は下の「止まる条件」のマージの条件をすべて満たすこと（平野さんが 2026-11-04 に「条件付きでマージしてよい」と答えた）
貼る時機: いつでも（CHAT-1104-LGN-27 が判断待ちで止まった後。LGN-28・29 と別のセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-gnotice の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-lgn-gnotice を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgn-gnotice origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。LGN-27 の work/1104-lgn-policy は使わない・取り込まない。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら、両方を残して解いてよい。生成物だけの衝突は、取り込んだ後のスクリプトで生成し直す（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1104-LGN-27 のログ（`origin/work/1104-lgn-policy` の `docs/logs/CHAT-1104-LGN-27.md`）を、この作業ブランチに同じパスで写す（ファイルだけ。ほかのファイルは写さない）。写したログの `## 報告` の状態を「中断」にし、末尾に ` / 続き: CHAT-1104-LGN-30` を足す。理由として、2026-11-04 に平野さんが /policy/ を作らないと決めたことを書く。`## 指示` 欄は変えない。あわせて、work/1104-lgn-policy の先頭の SHA をログに記録する。

## 目的
#663 の目的は、Google Maps Platform 利用規約の要件を満たすことだった。要件は、Places API で店の place_id を取り、log/ から Google マップへリンクしている点にかかる。LGN-27 では、サイト全体の利用規約・プライバシーポリシーのページ /policy/ を作ってプレビューで止めた。その後、必須なのは Google の要件だけだと分かったので、/policy/ は作らない。代わりに log/ のページの末尾に、その2文とリンクを載せる。

### 決定（2026-11-04、平野さん）
- /policy/ は作らない。LGN-27 の作業（work/1104-lgn-policy）は cloudflare へ入れない
- Google の要件の2文とリンクは、log/ のページの末尾に「利用条件」として載せる
- 条件付きで cloudflare へマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- 要件は Google Maps Platform Terms of Service の 3.2.2(a)(i) で、LGN-27 の `## 経過` に原文の引用がある。チャット側が 2026-11-04 に原文を読み直した。3.2.2(a) の (ii)・(iii) は、利用者が Google マップを別の製品に組み込む場合の話で、log/ には当たらない。プライバシーポリシーを置けという要件は無い
- 文の案（LGN-27 の案の文から。Code が既存のページの書き方にそろえて直してよい。直したらログに書く）:
  - 見出し「利用条件」
  - 「このページは Google マップの機能・内容（店の Google マップの ID と、Google マップへのリンク）を含みます。Google マップの機能・内容の利用には、そのときどきの [Google マップ／Google Earth 追加利用規約](https://maps.google.com/help/terms_maps/) と [Google プライバシーポリシー](https://policies.google.com/privacy) が適用されます。」
- 置き場所は log/ のすべての状態（トップ・検索結果・`?year=` など）で、いつも見える末尾にする。上の (i) の中の国土数値情報の出典は変えない。リンクの属性（`target`・`rel`）は既存のページの外部リンクの形に合わせる
- 生成スクリプト（`scripts/generate_log_pages.py`）の定数で持ち、手で生成物を書かない。検査を1本足す（末尾に2つの URL があること）
- docs/notes/log-page.md に、末尾の「利用条件」とその理由（規約の条項）を短く書く。docs/decisions/operations.md に決定を足す
- #663 に、/policy/ は作らず log/ の末尾に載せたこと、マージの SHA をコメントし、閉じる（状況のラベルを外す）。issue の題が「利用規約・プライバシーポリシーのページを作る」のままなら、閉じる前に今回の形が分かる題に直す
- work/1104-lgn-policy はマージされないので、cleanup-branches.yml では消えない。消すのは平野さんが GitHub の画面で行う（リモートのブランチの削除は mj-git-guard が止める）。報告の「判断が必要なこと」に、ブランチ名と記録した先頭の SHA を書く

## 手順
1. #663 に着手中のコメントを残す（LGN-27 の着手中のコメントがあれば、同じ issue の続きなので止まらない）。上の変更を作り、テスト（`python3 -m unittest discover -s scripts/tests` と `node --test`）を回す
2. `regenerate-page.yml` を作業ブランチの ref、入力 `check`、`target_page: log_pages` で手動実行する。続けて入力無しで手動実行し、生成物をコミットさせる。docs/notes/self-review.md のとおり差分をレビューさせる
3. 下のマージの条件をすべて確かめ、満たしたら cloudflare へマージする。マージの後、本番の `https://ryoei.pro/log/` を `curl` で読み（UA に `mj-internal`）、末尾に2つの URL があることを確かめる。#663 にコメントして閉じる

## 止まる条件
- マージの条件（1つでも外れたらマージしない）:
  - (1) cloudflare との差分が、log/ の末尾の「利用条件」（生成スクリプト・生成物・style.css の要る分）、検査、docs（log-page.md・decisions・logs〈LGN-27 の写しを含む〉）、生成物の数の変化（毎朝の取り込みで投稿が増えた分）だけ
  - (2) `python3 -m unittest discover -s scripts/tests` と `node --test` が通る
  - (3) `check`（`target_page: log_pages`）で生成が止まらない
  - (4) `_headers` の `/*` の CSP に `https://cloudflareinsights.com` が残っている
- 冒頭に書いた以外の取り込みの衝突がある
- 作るのに、新しい依存・CSP の変更・シートへの書き込み・外部 API の呼び出し・ナビやサイトマップや `llms.txt` の変更が要る
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗に分ける。無関係なら原因を報告に書いて残りの手順を進めてよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。マージの条件の確かめの結果を条件ごとに書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-30.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-30 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1104-LGN-30` のコミットなし。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし。#663 は open（題「ログ: 利用規約・プライバシーポリシーのページを作る」、ラベル「分野: セキュリティ」「対象: resource_logs」。状況のラベルなし）
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1104-lgn-gnotice origin/cloudflare`（8c7dd9a31）
- work/1104-lgn-policy の先頭: e20490d5e8cf2fdfd4d7fa6364a8267475840d32（取り込まない・使わない）
- LGN-27 のログ: `git show origin/work/1104-lgn-policy:docs/logs/CHAT-1104-LGN-27.md` をそのパスに写し、`## 報告` の状態を「中断 / 続き: CHAT-1104-LGN-30」に、`## 報告` の前に中断の理由（2026-11-04 に平野さんが /policy/ を作らないと決めた）と先頭の SHA の行を足した。`## 指示` 欄は変えていない（写す前後で一致を確かめた）
- #663 に着手中のコメント（LGN-27 の着手中のコメントがある同じ issue の続き）
- 変更（3288d4e49）: `generate_log_pages.py` に `TERMS_HTML`（定数 `GOOGLE_MAPS_TERMS_URL`・`GOOGLE_PRIVACY_URL`）を足し、`body_html` のページの入れ物の最後（`#log_home`・`#log_result` の後）に置いた。`assets/log.js` が切り替えるのは `#log_home`・`#log_result` の hidden と `#log_result` の中身だけなので、どの状態でも見える。文は指示の案のまま（リンクの前後の空白だけ、既存の日本語の文の書き方に合わせて詰めた）。リンクは `SOURCE_HTML` と同じ `target="_blank"`（rel なし）と `NEW_TAB_HINT`。見出しは `h2`（`section aria-labelledby`）。style.css に `.mj-log-terms`・`.mj-log-terms-title` と注記の上の余白（17行）。国土数値情報の出典の `SOURCE_HTML` は変えていない
- 検査: `test_log_pages.py` の TermsTest（見出し・2つの URL と target・入れ物の最後・`#log_result` の後）。`python3 -m unittest discover -s scripts/tests` OK、`node --test scripts/tests/js/*.test.js .github/actions/upsert-issue/test/*.test.js` pass 40・fail 0
- docs（4e3d8dd78）: log-page.md（画面ごとの仕様に「利用条件」と理由〈規約 3.2.2(a)(i)〉、「段2 の後」の行から #663 を外した）、docs/decisions/operations.md
- `regenerate-page.yml`（作業ブランチ、`check`・`target_page: log_pages`、run 41778449435、4e3d8dd78、success）: 「3層から 3168件のうち 2436件・1740店（…「閉業」シートで外した 0店…）」「検査: 通りました(2436件・1740店。ファイルは書いていません)」
- 自己レビュー（新しい文脈のサブエージェント1回、docs/notes/self-review.md）: 指摘なし（要件・決定、log.js が隠さないこと、HTML・アクセシビリティ・CSS、検査が変更前に落ちること〈生成スクリプトを戻すと TermsTest が落ちる〉、docs）。補足の2点（rel が無いのは既存の `SOURCE_HTML` と同じ形・見出しの文字の大きさは注記として控えめ）は意図どおり。往復1回で収まった
- 再生成（入力無し、run 41778530568、success）→ d7b736fbb `chore: regenerate …`。変わったのは `log/index.html` の1行（末尾の利用条件）だけ（題に並ぶほかのパスは差分なし）
- マージの条件:
  - (1) `git diff --name-only origin/cloudflare...HEAD` は8ファイル: `scripts/generate_log_pages.py`・`log/index.html`（生成物）・`style.css`（利用条件の分）・`scripts/tests/test_log_pages.py`（検査）・docs/notes/log-page.md・docs/decisions/operations.md・docs/logs/CHAT-1104-LGN-27.md（写し）・docs/logs/CHAT-1104-LGN-30.md。満たす
  - (2) unittest OK・`node --test` pass 40・fail 0。満たす
  - (3) `check`（`target_page: log_pages`、run 41778449435）success・「検査: 通りました」。満たす
  - (4) `_headers` の `/*` の CSP に `https://cloudflareinsights.com` がある（1件）。満たす
  - 取り込み: cloudflare は作業ブランチの祖先（取り込みは不要・衝突なし）
- マージ: 直前の再 fetch で祖先を確かめて `git push origin work/1104-lgn-gnotice:cloudflare`（8c7dd9a31..d7b736fbb）
- 本番（UA に `mj-internal`）: check-run（d7b736fbb）は Workers Builds: mj・regenerate・sync とも success。`https://ryoei.pro/log/` は 200、`mj-log-terms` の節が1つあり `#log_result` の後、節の中に `https://maps.google.com/help/terms_maps/` と `https://policies.google.com/privacy` がある
- #663: 題を「ログ: 利用規約・プライバシーポリシーのページを作る」から「ログ: log/ の末尾に Google マップの利用条件を載せる」に直し、/policy/ を作らず末尾に載せたこととマージの SHA をコメントして閉じた（状況のラベルは付いていなかった）

## 報告

- 状態: 完了
- ブランチ: work/1104-lgn-gnotice
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-LGN-30.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-gnotice
- 確認用URL: なし（本番 https://ryoei.pro/log/ で確かめた）
- マージ: 済（d7b736fbb）。条件 (1) 差分は利用条件の生成スクリプト・生成物・style.css・検査・docs・ログ（LGN-27 の写しを含む）の8ファイル (2) unittest OK・node --test pass 40 (3) check の log_pages success (4) CSP に cloudflareinsights.com あり。本番の log/ の末尾に2つの URL
- issue: #663（題を直し、コメントして閉じた）
- 判断が必要なこと: work/1104-lgn-policy（先頭 e20490d5e8cf2fdfd4d7fa6364a8267475840d32、LGN-27 の /policy/ の案）はマージしないので cleanup-branches.yml では消えない。消すなら平野さんが GitHub の画面で削除する
- 未確認の項目: 実機（iPhone）での末尾の見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d7b736fb）: https://github.com/retroeater/mj-logs/tree/main/guide/d7b736fb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7b736fb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7b736fb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7b736fb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7b736fb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7b736fb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7b736fb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
