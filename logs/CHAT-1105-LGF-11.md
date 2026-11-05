# CHAT-1105-LGF-11

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1105-lgf-ui
- 着手時HEAD: 7aed54e53

## 指示

【Claude作成】Claude Code 向け指示：「利用条件・出典」の (i) のアイコンを外し、work/1105-lgf-ui（LGF-10）と一緒に cloudflare へマージする Chat-Ref: CHAT-1105-LGF-11 マージ: 承認済み（チャットで）。下の「止まる条件」のマージの条件をすべて満たしたときだけ 貼る時機: いつでも（CHAT-1105-LGF-10 の判断待ちの続き。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgf-ui の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-lgf-ui を続けて使う（LGF-10 の変更と一緒にマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、docs/decisions/*.md・docs/logs/ の末尾の追記どうしは両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の文書の衝突は解かずに止まる。生成物（log/）が衝突したら手で解かず、取り込んだ後に `regenerate-page.yml`（作業ブランチの ref・`target_page: log_pages`）で作り直す 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-LGF-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1105-LGF-11` を足す。

目的
LGF-10（期間限定の2件を載せない・現在地のアイコン）を cloudflare へ入れる。あわせて、紹介文の末尾のラベル「利用条件・出典」の後ろの (i) のアイコンを外す。
決定（2026-11-05、平野さん）

* LGF-10 のプレビューを見て、マージしてよい
* 紹介文の末尾のラベルは「利用条件・出典 (i)」をやめ、「利用条件・出典」（(i) のアイコン無し）にする

前提（チャット側。平野さんの決定ではない）

* ラベルは `scripts/generate_log_pages.py` の `TERMS_HTML` の `<summary>`（LGF-07・08）。外すのは (i) のアイコン（`INFO_ICON`）だけで、押すと開く動き（`<details>`）・開いた中身（利用条件の2文・出典の1文・リンク）・文言「利用条件・出典」は変えない。押せることが分かるよう、語の下線などの今の見た目は残す。使わなくなった CSS の規則・`INFO_ICON` の参照があれば消す
* docs/notes/log-page.md の「利用条件」「出典」の項とテストのラベルの期待をそろえる
* 並び順（LGF-10 の経過「3.」）はこの指示では変えない

手順

1. ラベルから (i) を外し、テスト・log-page.md を直す。テスト（`python3 -m unittest discover -s scripts/tests` と `node --test scripts/tests/js/*.test.js`）を回す。`regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直す
2. origin/cloudflare を取り込み（必要なら）、プレビュー（Playwright、UA に mj-internal、幅 390）でトップの紹介文の末尾が「利用条件・出典」（アイコン無し）で、押すと LGF-08 と同じ中身が開くこと、検索欄の右のアイコンが LGF-10 と同じであることを確かめる
3. マージの条件をすべて満たしたら cloudflare へ fast-forward でマージし、本番の `https://ryoei.pro/log/`（`?v=<マージの SHA>`）で同じことと、`?shop=ChIJT2WXm-aNGGAR24zlaGQ7fiQ` が「見つかりませんでした」になることを確かめる。15分たっても本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする。LGF-10 のログの状態は CLAUDE.md「作業ログ」節に従って直す

止まる条件

* マージの条件（1つでも外れたらマージしない）:
   * (1) テストが通る
   * (2) 手順1で足した差分が、ラベルの (i) を外す変更（`generate_log_pages.py`・`style.css`・テスト・docs/notes/log-page.md）と log/ の生成物の該当行だけ
   * (3) origin/cloudflare の search.json と比べた店・投稿の数の差が、LGF-10 の2件（東京ラーメンショー極み麺・寒鱈まつり）と毎朝の取り込みの分だけで説明できる
   * (4) 開いた中身（利用条件の2文・出典の1文・リンクの URL）が LGF-08 とバイト単位で同じ
* 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
* マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗を分けて書く。無関係なら原因を報告に書いて残りの手順を進めてよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7864a244）: https://github.com/retroeater/mj-logs/tree/main/guide/7864a244

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2aae12da.md
