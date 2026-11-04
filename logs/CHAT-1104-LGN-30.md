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

## 報告

- 状態: 作業中
- ブランチ: work/1104-lgn-gnotice
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgn-gnotice/docs/logs/CHAT-1104-LGN-30.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-gnotice
- 確認用URL: なし
- マージ: 未
- issue: #663
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8c7dd9a3）: https://github.com/retroeater/mj-logs/tree/main/guide/8c7dd9a3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
