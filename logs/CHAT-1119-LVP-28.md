# CHAT-1119-LVP-28

- 着手日時: 2026-11-19
- 対象issue: #475
- ブランチ: work/1119-lvp
- 着手時HEAD: 13ebaa174

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ以外」の所属団体に「WRPM」を足し、条件つきで cloudflare へマージする
Chat-Ref: CHAT-1119-LVP-28
マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1119-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1119-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1119-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
世界選手権 第4回の予選に出た Gemma Sakamoto は「連盟プロ以外」に所属団体「WRPM」で入っているが、生成が所属団体を6つ（連盟・最高位戦・協会・RMU・連合・-）しか受け付けないため、その行を使わずに飛ばし、未登録の名前として残っている（#475）。そのため `live/wrc/4/q.html` が作られない。

### 決定（2026-11-19、平野さん）
- 所属団体に「WRPM」（World Riichi Professional Mahjong）を足す
- 足したら、すぐ再生成して本番に出す

### 前提（チャット側。平野さんの決定ではない）
- 受け付ける所属団体の一覧は `scripts/lib/names.py` の `AFFILIATIONS`（2026-11-19 の cloudflare）。ほかに所属団体の値で分けている所（`generate_saikyo_mens.py` の団体名の対応、#354 の略称の表示〈保留〉、pro/・mtsuku の表示など）があるかは要確認。表示に団体名を出す所では「WRPM」がそのまま出る見込み
- 「連盟プロ以外」で所属団体が「WRPM」の行は、2026-11-19 の時点で Gemma Sakamoto の1行（平野さんの CSV）

## 手順
1. #475 に着手中のコメントを書く。`AFFILIATIONS` に「WRPM」を足し、所属団体の値を使うほかの箇所を洗い出す（団体名の対応表が要る所があれば足す）。テストを足す（「WRPM」の行が使われる、6つ＋WRPM のほかは今までどおり飛ばす）
2. 全ページを再生成し、生成物の差分を書く（`live/wrc/4/q.html` が作られること、Gemma Sakamoto が出るページ）。テストを回す
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages pro_pages title_pages jpml_pros` で手動実行して、Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md（または所属団体の決定の置き場所）に足す

## 止まる条件
- 生成物の差分が、Gemma Sakamoto の出るページ（世界選手権 第4回の予選など）と、それに伴う一覧・件数のほかに出る（マージせずに止まる）
- テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
- 未マージの work/ ブランチが、`scripts/lib/names.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- #475 に他セッションの着手中コメントがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1119-LVP-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1119-LVP-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過
- Chat-Ref の確認: `CHAT-1119-LVP-28` のコミットは0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-19 18:17 JST
- 0. 「指示」欄の末尾は指示文の最後の行と一致。work/1119-lvp はリモート・ローカルとも無いので origin/cloudflare から作った
- #475 の着手中のコメントは2件（2026-10-14 の CHAT-1014-SNM-23、2026-11-15 の CHAT-1115-ACF-45）あるが、どちらも同じ日に完了のコメントがある。#475 に着手中のコメントを書いた
- 未マージの work/ ブランチで `scripts/lib/names.py` を変えているものは無い
- 所属団体の値を使う箇所の洗い出し（`AFFILIATIONS` は `lib/names.py` だけが使う）:
  - `generate_title_pages.py` の `Person.org`: 「-」・空以外の値を入れるが、表示には使っていない
  - `generate_ask_data.py`・workers/ask: 名前の解決にだけ使い、所属団体は出さない
  - `research_candidates.py`・`lib/unused_names.py`: 値をそのまま文に入れる（「WRPM」と出るだけ）
  - `generate_saikyo_mens.py` の `ORG_ABBREVIATIONS`・`check_saikyo_unregistered.py`・`lib/mtsuku.py`: 別のシート（正式名）の値で、関係しない
  - 表示（#354、保留）: 未実装。docs/notes/live-page-design.md「2-5」の対象の列挙（最高位戦・協会・RMU・連合）に WRPM を入れるかは #354 に着手するときの論点
  - 団体名の対応表を足す所は無い
- 実装（0e9bfd2f7）: `scripts/lib/names.py` の `AFFILIATIONS` に「WRPM」を足した。docs/notes/live-page-design.md「1-4」の所属団体の値の表と「6つ」→「7つ」を直した。テスト `scripts/tests/test_names.py`（WRPM の行が使われる、ほかの5つは今までどおり、空・前後の空白・小文字・未知の値・「プロ」にいない「連盟」は捨てる）。直す前のコードでは WRPM のテストが落ちる。テスト 2,256件 OK
- 全ページの再生成（dry_run、compare_ref origin/cloudflare、run 44495240989）: **差分なし**。`live/wrc/4/q.html` は作られない
- **原因（指示文の前提と違う）**: 一時の出力（a6ba1f867、run 44496335245。5d7e6894d で戻した）で【3】と【2】の第4回の行を読んだ:
  - 【3】の行4377〜4394（2026-11-17 の取り込みで追記された18行）は、掲載・ステージ・まとめ単位とも空欄。掲載は【2】の自動の値で決まる
  - 【2】の「掲載（自動）」: ベスト32・16・8・決勝の8行は Y、**予選の10行は N（理由「範囲外のステージ（予選）」）**。掲載の自動の規則（`lib/live_layer3.py` の `OUT_OF_RANGE_STAGES = ("予選",)`、2026-11-05 の決定「ステージが予選だけの行は N」）による
  - Gemma Sakamoto が出るのは予選3回戦（行4377 `1SzitLyEpuE`）だけ。この行は自動の値が N なので、WRPM を受け付けても `q.html` は作られない。今の本番の `live/wrc/4/` は `b32.html`・`b16.html`・`b8.html`・`f.html`（ベスト16 も、11/18 に20名が登録されて出ている）
  - #475 の未登録の名前は 2026-11-18 に 21名 → 1名（Gemma Sakamoto だけ）。WRPM を受け付けると、予選の10行で未登録の名前は0名になる
- **CHAT-1116-LVP-27 の見込みの訂正**: LVP-27 の試しは第4回の18行の掲載を Y と決めて生成したため、自動の規則の「予選は N」を通っていない。「18本とも限定 Y の動画なので Y」「名前を登録すれば予選10本と q.html が出る」は誤りで、予選の10本は自動では N になる
- 指示文の前提（WRPM で飛ばされているため q.html が作られない）と実物が違うので、CLAUDE.md「Chat-Ref」のとおりマージせずに止めた（WRPM の変更自体は生成物を変えない）
- 自己レビュー（新しい文脈のサブエージェント、1回。対象 0e9bfd2f7、観点: 所属団体の値を使うほかの箇所、テスト、文書）: 要修正なし。任意2件:

| 指摘 | 扱い |
|---|---|
| #354 の表示の対象（「2-5」280行、プロ団体4つ）に WRPM を入れるかが未決 | 直さない（#354 は保留。着手するときの論点として上に書いた） |
| 「1-4」の所属の確かめ方（4団体の選手一覧）が WRPM の人を拾えない | 直さない（WRPM の人をどう確かめるかは平野さんの入力の運用。判断が必要なことに書いた） |

  証跡: 所属団体を使う箇所をレビューが表にして確かめた（上の洗い出しと同じ結論）。テストは修正前のコードで WRPM の1本が落ち、修正後に全体 2,256件 OK

## 報告

- 状態: 判断待ち / 続き: CHAT-1120-LVP-29
- ブランチ: work/1119-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1119-lvp/docs/logs/CHAT-1119-LVP-28.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1119-lvp
- 確認用URL: なし（生成物の変更なし）
- マージ: 未（指示文の前提と実物が違うため止めた）
- issue: #475
- 判断が必要なこと:
  1. `live/wrc/4/q.html` が作られないのは WRPM のためではなく、掲載の自動の規則で第4回の予選の10行が N になるため（理由「範囲外のステージ（予選）」）。予選を載せるか: A 平野さんが【3】の第4回の予選10行（行4377〜4379・4381・4383・4387・4389・4392〜4394）の掲載に Y を入れる（ほかの大会の予選の扱いは今のまま）／B 自動の規則で、リーチ麻雀世界選手権の予選を範囲外から外す（コードの直し）／C 予選は載せない（今のまま）
  2. WRPM の変更（0e9bfd2f7、生成物は変わらない。#475 の未登録の名前 Gemma Sakamoto が消える）を、1 の答えを待たずにマージしてよいか。マージしてよければ、続きの指示でマージと `regenerate-page.yml` の手動実行を行う
  3. 「連盟プロ以外」で WRPM の人の所属をどう確かめるか（docs/notes/live-page-design.md「1-4」の確かめ方は4団体の選手一覧だけ）。#354（所属団体の表示、保留）の対象に WRPM を入れるかは #354 に着手するときに決める
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 13ebaa17）: https://github.com/retroeater/mj-logs/tree/main/guide/13ebaa17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13ebaa17.md
