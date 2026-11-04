# CHAT-1105-LGF-07

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1104-lgf-alias
- 着手時HEAD: 765678989（7cfc3188e に origin/cloudflare を取り込んだマージ）

## 指示

【Claude作成】Claude Code 向け指示：log/ に「載せない店」と理由の置き場を作り新宿地下ラーメンを外す／期間限定の出店の候補を洗い出す／利用条件を紹介文の末尾の (i) にたたむ Chat-Ref: CHAT-1105-LGF-07 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-LGF-05 の判断待ちの続き。LGF-05 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-alias の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lgf-alias を続けて使う（LGF-05 の店名の統一と一緒にプレビューを見てからマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、docs/decisions/*.md・docs/logs/ の末尾の追記どうしは両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1104-LGF-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1105-LGF-07` を足す。

目的
LGF-05 のプレビューを見た平野さんの決定を、同じ作業ブランチに足す。(1) 載せない店を理由つきで1か所にまとめ、新宿地下ラーメンを外す (2) 期間限定の出店（ラーメンフェスなど）の候補を洗い出す（外すのは平野さんが候補を見てから）(3) 利用条件をたたむ。
決定（2026-11-05、平野さん）

* LGF-05 の店名の統一は、プレビュー（中華蕎麦 きみのあーる）で OK
* 「新宿地下ラーメン」（place_id ChIJkdDwfBKNGGAR2oXHz9NjCc8）は、期間限定でいろいろな店舗が出店する場所なので、一律に載せない。載せない理由をどこかに残す。「過去メシ」も同じく理由を残す
* ほかにも期間限定で出店している店（ラーメンフェスなど）は、検索した人が訪れることができないので載せないことにしたい。「期間限定メニュー」と混同しないこと
* 利用条件は「利用条件 (i)」のような表記にして、見たい人だけが開いて見る形にしたい。できるなら、一番下の紹介文の文末に置く

前提（チャット側。平野さんの決定ではない）

* 今、log/ に載せない投稿は、石立岳大の行（更新が終わった）と、谷岡育夫の「過去メシ」（再投稿）の行（docs/notes/log-page.md、`scripts/lib/log_rows.py` の merged_rows または `generate_log_pages.py` の絞り込み。どこで外しているかを確かめる）
* 置き場（チャット側の案）: 店（place_id）単位で載せない一覧を `scripts/generate_log_pages.py` の定数（例 `HIDDEN_PLACES = {place_id: 理由}`）にし、理由をコメントではなく値として持つ。docs/notes/log-page.md に節「載せない投稿・店」を作り、規則ごとに理由と決めた日を書く（石立岳大・過去メシ・新宿地下ラーメン）。シート（【3】）には書かない（コードは【3】に書かない規則）。毎朝の取り込みで新しい投稿が入っても、この一覧の店は載らない
* 期間限定の出店の候補: `log/search.json` の店名・投稿のメニューから、出店・催事を表す語（例: フェス・ラーメンフェス・つけ麺博・博覧会・祭・催事・物産展・ポップアップ・POP UP・出店・特設・期間限定店。語は増やしてよい）で候補を出す。「期間限定」だけ（「期間限定メニュー」「期間限定の味」など）は候補にしない。本文はシートにしか無いので、この指示では読まない（search.json だけで出せる範囲。足りなければ、その旨を書く）
* 利用条件: 今は `generate_log_pages.py` の `TERMS_HTML` がページの末尾に見出し「利用条件」と2文を常に出している（Google Maps Platform 規約 3.2.2(a)(i) の知らせ。docs/notes/log-page.md「利用条件」）。規約が求めるのは、利用条件で知らせることで、常に開いた形で出すことまでは求めていない（CHAT-1104-LGN-27 の原文の引用。開いた形は LGN-30 のチャット側の前提だった）。案: 紹介文（`.mj-lead`）の文末に「利用条件 (i)」を置き、押すと2文とリンクが開く（`<details>`。トップの出典の「(i)」と同じ部品・書き方。キーボードでも開ける）。トップ・検索の結果・`?year=` などどの状態でも辿れること（紹介文がどの状態でも見えるかを確かめ、見えない状態があれば、その状態でも辿れる置き方を書いて止まらず進めてよい）
* 新宿地下ラーメンを外すと、カテゴリ「投稿回数の多い店」のカードが1枚入れ替わる。店の数・投稿の数は外した分だけ減る

手順

1. 載せない投稿の今の外し方（石立岳大・過去メシ）を確かめてログに書き、`HIDDEN_PLACES`（名前は合わせてよい）を足して新宿地下ラーメンを入れる。docs/notes/log-page.md に「載せない投稿・店」の節を足す。テストを足す（一覧の店が search.json・カテゴリに出ない）
2. 利用条件を紹介文の文末の「利用条件 (i)」にたたむ（上の案）。log-page.md の「利用条件」の項を直す。テストを直す・足す。docs/notes/self-review.md のとおり1・2の差分をレビューさせる
3. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、(a) 店の数・投稿の数の LGF-05 の生成（dd39e780b）との差と、外れた店・投稿の一覧 (b) 期間限定の出店の候補を全件表にする（鍵・店名・投稿の数・当たった語・当たった値〈店名かメニューか〉・投稿 ID）。候補は外さない（平野さんが選ぶ）

止まる条件

* 手順3の (a) で、新宿地下ラーメンの店（LGF-05 の search.json で投稿 9件）以外の店・投稿が減った、または毎朝の取り込みで説明できない差がある
* 利用条件の2文とリンクの中身（文・URL）が変わる（置き方と見た目だけを変える）
* 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、同じ関数（載せない投稿を外す処理・`TERMS_HTML` の置き場）を変えている、または取り込みで衝突する（並行の work/1104-lgf-stations は `convert_x_posts.py` だけを一時的に変える見込みで、重ならない）
* 終わったら、マージせずに判断待ちで止まる（LGF-05 と合わせて平野さんがプレビューを見る）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（トップ・紹介文の「利用条件 (i)」・候補の多い店の `?shop=` のパス）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fa43459d）: https://github.com/retroeater/mj-logs/tree/main/guide/fa43459d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1a06d89d.md
