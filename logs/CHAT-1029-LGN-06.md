# CHAT-1029-LGN-06

- 着手日時: 2026-10-29
- 対象issue: #367・#639
- ブランチ: work/1029-lgn-eki（LGN-05 から続けて使う）
- 着手時HEAD: f439133fa

## 指示

【Claude作成】Claude Code 向け指示：【2】の「Places の店名」の列を消し、駅をオープンデータで計算し直して、work/1029-lgn-eki をマージする
Chat-Ref: CHAT-1029-LGN-06
マージ: 承認済み（チャットで）。条件は下の「止まる条件」のマージの条件をすべて満たすこと（平野さんが 2026-10-29 に、この条件でマージしてよいとほかの問いと分けて答えた）
貼る時機: いつでも（CHAT-1029-LGN-05 が判断待ちで止まった後。LGN-05 と同じセッションに貼ってよい）。2026-11-02 02:00 JST の run-weekly より前に終える
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-lgn-eki への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1029-lgn-eki を続けて使う（LGN-05 の変換の直しがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1029-LGN-05 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1029-LGN-06` を足す。

## 目的
Google の規約に合わせる作り直し（LGN-04・05）の2本目。既存の【2】自動変換後から Google の値を除き、LGN-05 の変換の直しと一緒に cloudflare へ入れる。今の cloudflare の変換は、【2】の見出しが変わると止まる。LGN-05 の変換は、今の見出しのままだと止まる。このため、シートの書き換えとマージを続けて行い、2026-11-02 の run-weekly を新しい変換で動かす。

### 決定（2026-10-29、平野さん）
- LGN-04・05 の決定はそのまま（規約に全部合わせる、Places は place_id だけ〈案B'〉、「Places の店名」の列は消す、駅・都道府県はオープンデータ）
- オープンデータは国土数値情報（鉄道・行政区域、CC BY 4.0）。都道府県はオープンデータの駅の点と行政区域の面で決める（Google の座標は使わない）（LGN-05 で Code が選び、平野さんがこの形で進めると答えた）
- 【2】の書き換えとマージは今すぐ行う（2026-11-02 の run-weekly より前）
- マージは下の条件をすべて満たしたら行ってよい

### 前提（チャット側。平野さんの決定ではない）
- 書き換えは LGN-05 の申し送りのとおり、stations の mode（または新しい mode）を新しい規則（`station_cells()`）で計算し直す形にする。API（Places・Claude・X）は呼ばない
- 「駅の候補」はオープンデータの駅と距離で書き直す。「要確認」「要確認の理由」は新しい規則で計算し直す。ほかの列は変えない
- 【3】手動補正は書き換えない。この指示では、【3】の見出しと、空でない値の列ごとの件数を読むだけ（値の出どころが Google マップかは平野さんに聞くため。値そのものはログに書かない）
- 出典の表記は LGN-05 の文（「国土数値情報（鉄道データ・行政区域データ）（国土交通省）をもとに作成」と URL）。resource_logs.html にも入れる（駅を出すため）。log/ への表記は log/ の作り直し（次の指示以降）で入れる
- 2026-11-02 の run-weekly は、マージの後は新しい変換（Text Search は IDs Only、Nearby Search なし）で動く。#367 を閉じるのは LOG 系列のチャット（伝言は平野さんが渡す）
- 一度きりの書き換えに使う mode・関数は、使い終えたら同じ指示の中で消すか、残すなら理由を書く（技術的負債を残さない）

## 手順
1. 書き換えを dry-run で行う。`convert-x-posts.yml` を作業ブランチの ref で、書き換えの mode の dry-run で手動実行し、【2】の行数・消す列・変わる行の数を列ごとに・駅が空になる行の数と割合・要確認になる行の数を出させる。下の条件 (3) を満たさなければ、何も書かずに止まる。あわせて【3】の見出しと、空でない値の列ごとの件数を読む（読むだけ）
2. 書き換えを行う。先に【2】の全行を、同じスプレッドシートの新しいシート（名前は Code が決めてよい。例「【2】バックアップ_1029」）に写し、行数が一致することを確かめる（条件 (1)）。続けて書き換えの mode を本番で実行し（「Places の店名」の列を消す・駅・駅の候補・要確認・要確認の理由を書き直す）、読み直して条件 (2)(3) を確かめる。外れたら、バックアップから【2】を元に戻して止まる（戻したことと、戻した後の行数・見出しをログに書く）
3. 一度きりの書き換えの mode を消すか残すかを決めて直し、resource_logs.html に出典の表記を入れる。テスト（`python3 -m unittest discover -s scripts/tests`）と、新しい見出しでの変換の dry-run（mode all）を手動実行し（条件 (4)）、`regenerate-page.yml` を作業ブランチの ref で入力無しで実行して resource_logs.html を生成させる（条件 (5)）。docs/notes/self-review.md のとおり差分をレビューさせる。条件をすべて満たしたら cloudflare へマージし、#639 にマージの SHA と書き換えの結果（どのログのどの節かを指す）をコメントする

## 止まる条件
- マージの条件（1つでも外れたらマージしない）:
  - (1) 【2】の全行のバックアップ（同じスプレッドシートの別のシート）が、行数一致で作れた
  - (2) 【2】の行数が書き換えの前後で同じで、消えた列は「Places の店名」だけ
  - (3) 書き換えで変わるのは「駅」「駅の候補」「要確認」「要確認の理由」の列だけで、駅が空になる行が全体の 5% 以下
  - (4) テストと、新しい見出しでの変換の dry-run（mode all）が通る
  - (5) 再生成した resource_logs.html の差分が、駅の列と出典の表記だけ（行数は変わらない。種類ごとの件数と、想定外の差分を全部ログに書く）
- 書き換えの本番の実行の後に条件が外れた: バックアップから【2】を元に戻して止まる（2026-11-02 の run-weekly を今の cloudflare の変換で動かせる状態に戻すため）。元に戻せなかったら、その旨を最終報告の冒頭に書く
- 作るのに、Places・Claude・X の API の呼び出し・【3】への書き込み・新しい依存が要る
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗に分ける。無関係なら原因を報告に書いて残りの手順を進めてよい。自分の変更で落ちると分かっているテストは直してよい
- 書き換えの dry-run・本番の手動実行が失敗で止まったときは、最終報告に「失敗通知が届くが対応不要」と書く（本番の書き換えの失敗で【2】を元に戻した場合を除く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。【3】の見出しと列ごとの件数、バックアップのシート名、書き換えの列ごとの件数を書く。マージの後に予約の起動で初めて新しい変換が動く日時（2026-11-02 02:00 JST の run-weekly）を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-LGN-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-LGN-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1029-LGN-06` のコミット・ログなし。雛形の行の欠けなし
- 作業ブランチ: ローカル・リモートとも work/1029-lgn-eki が f439133fa で一致。`git merge-base --is-ancestor origin/cloudflare HEAD` は真（取り込み不要）。CLAUDE.md は `HEAD..origin/cloudflare` で変更なし
- LGN-05 のログの状態の末尾に ` / 続き: CHAT-1029-LGN-06` を足した

## 報告

- 状態: 作業中
- ブランチ: work/1029-lgn-eki
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-eki/docs/logs/CHAT-1029-LGN-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-eki
- 確認用URL: なし
- マージ: 未
- issue: #367・#639
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 74d7dc08）: https://github.com/retroeater/mj-logs/tree/main/guide/74d7dc08

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74d7dc08.md
