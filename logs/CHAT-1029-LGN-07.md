# CHAT-1029-LGN-07

- 着手日時: 2026-10-29
- 対象issue: #367・#639
- ブランチ: work/1029-lgn-eki（LGN-05・06 から続けて使う）
- 着手時HEAD: 359770dfe（origin/cloudflare を取り込む前）

## 指示

【Claude作成】Claude Code 向け指示：既存の行の本文の駅を Claude Haiku で読み直し、【2】を書き換えて work/1029-lgn-eki をマージする Chat-Ref: CHAT-1029-LGN-07 マージ: 承認済み（チャットで）。条件は下の「止まる条件」のマージの条件 (1)〜(6) をすべて満たすこと（平野さんが 2026-10-29 に、この条件でマージしてよいとほかの問いと分けて答えた） 貼る時機: いつでも（CHAT-1029-LGN-06 が判断待ちで止まった後。LGN-06 と同じセッションに貼ってよい）。2026-11-02 02:00 JST の run-weekly より前に終える 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-lgn-eki への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1029-lgn-eki を続けて使う（LGN-05・06 の作業があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1029-LGN-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1029-LGN-07` を足す。

目的
LGN-06 は、既存の行の本文の駅を本文から拾い直すと誤りが多く、駅が空の行が 12.5% になったため止まった（LGN-06 のログ `## 経過`「手順1」）。この指示では、既存の行の本文の駅を Claude Haiku で読み直してから【2】を書き換え、LGN-05・06 の作りと一緒に cloudflare へ入れる。
決定（2026-10-29、平野さん）

* 既存の行の本文の駅は Claude Haiku（`claude-haiku-5-5`）で本文を読み直して取る
* 読み直した駅と今の【2】の「駅」の一致・不一致を別に一覧で出し、不一致は平野さんが別途確かめる
* 【3】の「駅」「店名」「店URL」の値は旧「ログ」の手書き（Google マップから写したものではない）。そのまま残す
* 書き換えで「要確認」が変わる既存の行の「表示」は変えない
* マージは下の条件 (1)〜(6) をすべて満たしたら行ってよい（LGN-06 の (1)〜(5) に、Claude の費用の上限 (6) を足した）
* LGN-04〜06 の決定はそのまま（規約に全部合わせる、Places は place_id だけ、「Places の店名」の列は消す、駅・都道府県は国土数値情報）

前提（チャット側。平野さんの決定ではない）

* 読み直しは、変換の Claude の呼び出しと同じ形で、駅だけを返す短いプロンプトにしてよい（費用を下げるため）。対象は判定が飲食店の行（2,668行の見込み、LGN-06 の dry-run）。ほかの行は変えない
* 費用の上限はスクリプトの定数で $2 にし、届いたらそれ以上呼ばずに止まる（書かない）。まず dry-run で回数と費用の見込みを出してから本番
* 一致・不一致の一覧: 同じスプレッドシートの新しいシート（名前は Code が決めてよい。例「駅の照合_1029」）に、【2】の行番号・投稿ID・今の駅・読み直した駅・新しい駅（一覧で確かめた後）・一致かどうか、を書く。平野さんが【3】を直すときは、【3】の行番号も並べる（docs/notes/chat-routines.md の、層の行番号と投稿IDを並べる規則）。今の駅は Google 由来の値なので、平野さんの確かめが済んだらこのシートを消す前提（消す時期は平野さんが決める。報告の「判断が必要なこと」に書く）
* 5% の分母は飲食店の行（LGN-06 の作りのまま）
* LGN-06 の差分のレビューの指摘（`## 経過`「差分のレビュー」の表で「次の指示で直す」としたもの）を直す。特に、戻すときは列の数をバックアップにそろえる・安全のための分岐のテストを足す・try の外の例外・`except Exception` と SystemExit。本文から一覧の駅名を拾う処理はやめる
* LGN-06 の前提（一度きりの mode・関数は使い終えたら消すか理由を書く、出典の表記を resource_logs.html に入れる、【3】には書かない）はそのまま

手順

1. 読み直しの作りを足し、LGN-06 のレビューの指摘を直してテストを足す。`convert-x-posts.yml` を作業ブランチの ref で、読み直しの mode の dry-run で手動実行し、回数と費用の見込みを出させる（$2 を超える見込みなら止まる）。続けて本番で読み直しを実行し（【2】には書かず、結果は照合のシートと作業ファイルに置く）、照合のシートを書く。読み直した駅で relist の dry-run を行い、条件 (3) を確かめる（満たさなければ書かずに止まる）
2. LGN-06 の手順2のとおり、バックアップを作ってから【2】を書き換え（「Places の店名」の列を消す・駅・駅の候補・要確認・要確認の理由を書き直す）、読み直して条件 (1)(2)(3) を確かめる。外れたら、バックアップから【2】を元に戻して止まる
3. LGN-06 の手順3のとおり、一度きりの mode を消すか残すかを決めて直し、テスト・新しい見出しでの変換の dry-run（mode all）・`regenerate-page.yml` での resource_logs.html の生成を行い（条件 (4)(5)）、差分をレビューさせる。条件をすべて満たしたら cloudflare へマージし、#639 にマージの SHA と結果（どのログのどの節かを指す）をコメントする

止まる条件

* マージの条件（1つでも外れたらマージしない）:
   * (1) 【2】の全行のバックアップ（同じスプレッドシートの別のシート）が、行数一致で作れた
   * (2) 【2】の行数が書き換えの前後で同じで、消えた列は「Places の店名」だけ
   * (3) 書き換えで変わるのは「駅」「駅の候補」「要確認」「要確認の理由」の列だけで、駅が空になる飲食店の行が飲食店の行の 5% 以下
   * (4) テストと、新しい見出しでの変換の dry-run（mode all）が通る
   * (5) 再生成した resource_logs.html の差分が、駅の列と出典の表記だけ（行数は変わらない。種類ごとの件数と、想定外の差分を全部ログに書く）
   * (6) Claude の費用が $2 以下
* 書き換えの本番の実行の後に条件が外れた: バックアップから【2】を元に戻して止まる（2026-11-02 の run-weekly を今の cloudflare の変換で動かせる状態に戻すため）。元に戻せなかったら、その旨を最終報告の冒頭に書く
* 作るのに、Places・X の API の呼び出し・【3】への書き込み・新しい依存が要る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗に分ける。無関係なら原因を報告に書いて残りの手順を進めてよい。自分の変更で落ちると分かっているテストは直してよい
* dry-run・本番の手動実行が失敗で止まったときは、最終報告に「失敗通知が届くが対応不要」と書く（本番の書き換えの失敗で【2】を元に戻した場合を除く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。読み直しの回数と費用、照合のシートの名前と一致・不一致の件数、バックアップのシート名、書き換えの列ごとの件数、マージの後に予約の起動で初めて新しい変換が動く日時（2026-11-02 02:00 JST の run-weekly）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-LGN-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-LGN-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1029-LGN-07` のコミット・ログなし。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし（1行目に続けて書かれていた）
- 作業ブランチ: ローカル・リモートとも 359770dfe。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽だったので `git merge origin/cloudflare` で取り込んだ（衝突なし。取り込んだのは SEO-01 のログと saikyo/ の再生成）
- LGN-06 のログの状態の末尾に ` / 続き: CHAT-1029-LGN-07` を足した

### 手順1: 作り（49320fa93）

- `reread`（一度きり）: 【2】の判定が飲食店の行の本文（【1】の本文を投稿IDで引く）を claude-haiku-5-5 で読み直し、駅だけを返させる（`REREAD_PROMPT`・`REREAD_SCHEMA`、max_tokens 1000、effort low）。
  費用の上限 `REREAD_MAX_COST = 2.0`。見込みが上限を超えるなら呼ばずに止まる。呼ぶ途中で上限に届けば、それ以上呼ばず、照合のシートも書かない。
  照合は新しいシート「駅の照合_1029」に書く。列は 【2】の行・【3】の行・投稿ID・今の駅・読み直した駅・新しい駅・一致（一致／不一致／空）。【2】【3】には書かない
- `relist`: 本文から一覧の駅名を拾う処理（`body_stations`）をやめ、照合のシートの「読み直した駅」と、理由に書かれた駅（本文の駅・旧「ログ」の駅）で計算し直す。照合のシートが無ければ止まる
- `stations`（残す mode）: 今の「駅」（relist の後は国土数値情報の名前）と、理由に書かれた駅から計算し直す。駅の一覧を取り直したときに使う
- LGN-06 のレビューの指摘の直し:
  - 戻すときは、列の数をバックアップのシートの列数にそろえてから貼る（`column_count()`。列を消す要求が再試行で2回効いても、足りない列の数だけ足す）
  - `main()` は途中の例外でも出力を残す（`finally`）
  - 戻す処理の `except` に SystemExit を含めた
  - 理由の重複を消した
  - docstring・コメント・テスト名を直した
- 【3】の行番号は、【3】の見出しの「投稿ID」の位置で読む（【3】の見出しは【2】の旧い 20列で、投稿ID は11列目）
- 検査: reread の dry-run・照合のシートの中身・費用の上限で止まる・見込みが上限を超える、relist の dry-run・照合のシートが無い・書き換え・書く前に【2】が変わっていた・バックアップの不一致・駅が空が 5% を超える（dry-run・本番）・書けなかったら戻す・列が2つ消えても列数をそろえて戻す・書いた後が予定と違えば戻す・戻せなかったら報告する、を見本のシートで確かめた。`python3 -m unittest discover -s scripts/tests` → OK

### 手順1: reread の dry-run（run 40833270498、49320fa93、成功）

- 判定が飲食店の【2】の行 2,668行。Claude の呼び出し 2,668回、見込みの費用 約 $0.43（上限 $2.0）

## 報告

- 状態: 作業中
- ブランチ: work/1029-lgn-eki
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-eki/docs/logs/CHAT-1029-LGN-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-eki
- 確認用URL: なし
- マージ: 未
- issue: #367・#639
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0f7636bb）: https://github.com/retroeater/mj-logs/tree/main/guide/0f7636bb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f7636bb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74d7dc08.md
