# CHAT-1117-LGV-44

- 着手日時: 2026-11-17
- 対象issue: #679
- ブランチ: work/1117-lgv-thread
- 着手時HEAD: 14ce6794d

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の平野さんのスレッドの投稿をテンプレートで読む形にし、マージして 2026年の 112件を本番に取り込む（洗い替えを含む）
Chat-Ref: CHAT-1117-LGV-44
マージ: 承認済み（チャットで、2026-11-17、平野さん）。ただし止まる条件に当たらないときに限る
貼る時機: いつでも（CHAT-1117-LGV-43 の続き。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1117-lgv-thread の push と、`git push origin work/1117-lgv-thread:cloudflare` を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1117-lgv-thread を続けて使う（CHAT-1117-LGV-43 の変更があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行。docs/decisions/*.md の末尾の節どうしを含む）は、両方を残して日付順に解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。`log/` などの生成物が衝突したら、CLAUDE.md「ブランチ運用」のとおり取り込んだ後のスクリプトで生成し直して解く
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1117-LGV-43 のログの `## 報告` が「判断待ち」で、work/1117-lgv-thread がリモートにあることを確かめ、そうでなければ何もせずに止まる。CHAT-1117-LGV-43 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1117-LGV-44` を足す。

## 目的
CHAT-1117-LGV-43 の試しを平野さんが見て、本番に入れると決めた。あわせて、平野さんの投稿は決まった形（テンプレート）なので、Claude に読ませずにそのまま読み取る形にする。マージし、スレッドの 2026年の 112件を本番に取り込み、洗い替え（90行）をして、log/ に出す。

### 決定（2026-11-17、平野さん）
- LGV-43 の形で本番に入れる（案a）。駅の付き方（松戸富田麺絆 → 東京、国際基督教大学 大学食堂 → 武蔵境）と、日付が投稿日になることは、そのままでよい
- 平野さんの投稿は次のテンプレートなので、本文から類推せずに、そのまま読み取ってよい:
  ```
  【店】（駅）
  メニュー
  ```
- LGV-43 の試しで止まった3件の値:
  - 2122104025969815647（さわやか）: 駅は 浜松
  - 2122090256870838443（くおん）: メニューは TOKYO CLASSIC
  - 2122087422657982541: 「重慶マーラータン」はメニューで、店名は 毎味毎客

### 前提（チャット側。平野さんの決定ではない）
- テンプレートの読み方（スレッドの投稿だけ。it_uesama は今までどおり）: 1行目が `【<店名>】（<駅>）` の形（括弧は全角・半角のどちらも可、前後の空白は除く）なら、店名と駅をそのまま取る。2行目以降をメニューにする（複数行なら今のメニューの区切りの決まりに合わせる。URL〈t.co など〉の行は除く）。Claude の読み取り（verdict・店名・メニュー・駅）は呼ばない。写真の判定（Haiku）・空欄の確かめ・Places の Text Search（問い合わせは 店名 + 駅。今の作りに合わせる）・駅の決め方（駅の一覧との照らし合わせ、都外の【県】の付け方、同じ名前の駅の扱い）は今の作りをそのまま通す
- テンプレートに合わない投稿（1行目が形に合わない）は、類推しない。「表示」N・理由「テンプレートに合わない」（定数）にし、毎朝の一覧（#679）に出す
- 上の3件は、テンプレートで読めば決定の値になる見込み（浜松は都外なので【静岡】の形になる見込み）。読んだ結果が決定の値と違うときは、【3】の当たるセルに決定の値を書く（チャットで値を示したセルは Code が書いてよい。docs/notes/log-page.md の決まり。2122087422657982541 は店名が変わるので place_id も変わる見込み。Text Search の結果で決まらなければ place_id は空のまま #679 に出す）
- 進め方（費用を抑え、本番に半端な状態を残さない順）:
  1. テンプレートの読み取り・テスト・docs を作業ブランチに入れる
  2. 作業ブランチの ref・dry_run で、スレッドの 112件を取り、テンプレートで変換した結果を出す（全件。投稿 ID・日付・店名・駅・メニュー・place_id の有無・写真の判定・「表示」・理由。本文は書かない）。LGV-43 の 30件の結果（Claude の読み取り）と違う所を書く
  3. 洗い替え: 【1】【2】の retroeater の 2026-01-01 以後の、スレッドに無い行（LGV-43 の (c) の 90行）を消す。消す前に、手順2で取ったスレッドの投稿 ID と突き合わせ、スレッドの投稿を消さないことを確かめる。3層に書くジョブが走っていないことを確かめてから、一度きりの処理で消し、消した全件をログに残す
  4. 一度きりの処理を戻し、cloudflare へマージする
  5. マージの後、`fetch-x-posts.yml`・`convert-x-posts.yml` を cloudflare の ref で本実行（dry_run でない）し、112件を【1】【2】に入れる（今の手動実行の入力の形に合わせる。次の毎朝の運行を待たない）。`regenerate-page.yml` を cloudflare の ref・`target_page: log_pages` で実行し、log/ を作り直す
  6. 本番の `https://ryoei.pro/log/search.json?v=<SHA>`（UA に `mj-internal`）で、店・投稿の数、平野良栄の 2026年の投稿の数、上の3件の値を確かめる。毎朝の一覧（#679）の作り直しを動かし（今の作りで手動で動かせるなら）、件数と内訳を書く
- 費用の上限: この指示は合わせて $6 まで（X の読み取りは手順2と5で2回かかる見込み。約 $1.4 × 2 と Places・Haiku）。超える見込みなら止まる
- テストを足す（テンプレートの読み取り: 全角・半角の括弧、メニューが複数行、URL の行、合わない形 → 理由と N、店名に括弧を含む、it_uesama には当たらない）。docs/notes/log-page.md を直す。docs/notes/self-review.md のとおり差分をレビューさせる

## 手順
上の「進め方」の 1〜6 のとおり。手順2の結果で止まる条件に当たらなければ、3 以降へ進む。

## 止まる条件
- 手順2で、テンプレートに合わない投稿が 112件のうち 10件を超える（数と例〈投稿 ID・1行目の形だけ。本文は書かない〉を書いて止まる。洗い替え・マージはしない）
- 手順2で、スレッドの投稿が 112件から大きく違う（±10件を超える。新しい投稿で増えた分は除く）
- 洗い替えで、消す行の中にスレッドの投稿・今ログに載っている投稿・【3】に当たる投稿がある（消さずに止まる）
- 3層に書くジョブが走っている（終わるのを待ってから進めてよい。30分待っても終わらなければ止まる）
- シートへの書き込み・削除の途中で失敗した（済んだところまでをログに書いて止まる。やり直さない）
- テスト・手動実行が通らない
- 費用が $6 を超える見込みになる
- 未マージの work/ ブランチが上のファイルの同じ箇所を変えている、または取り込みで衝突する（上の解き方で解けるものは除く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗は原因を書いて残りを進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。載った件数・#679 に出た件数と内訳・費用を書く。CHAT-1117-LGV-41・43 のログの `## 報告` の状態も「完了」に直す（続きの印は残す）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1117-LGV-44.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1117-LGV-44 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1117-lgv-thread
- ログ: https://github.com/retroeater/mj/blob/work/1117-lgv-thread/docs/logs/CHAT-1117-LGV-44.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1117-lgv-thread
- 確認用URL: なし
- マージ: 未
- issue: #679
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a5b85219）: https://github.com/retroeater/mj-logs/tree/main/guide/a5b85219

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5b85219/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2e4dd08c.md
