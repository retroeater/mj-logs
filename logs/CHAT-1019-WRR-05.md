# CHAT-1019-WRR-05

- 着手日時: 2026-10-19
- 対象issue: #366・#611
- ブランチ: work/WRR
- 着手時HEAD: 739add250

## 指示

【Claude作成】Claude Code 向け指示：（WRR-04 の続き）最高到達の回数を「等」のときだけにし、work/WRR（wrcr/ を未公開のまま・ランキングの下限・予選の行が無い決勝進出者）を cloudflare へマージする Chat-Ref: CHAT-1019-WRR-05 マージ: 承認済み（チャットで、2026-10-19。平野さんが WRR-04 までのプレビューを確かめ「他は問題なし、マージしてよい」）。条件: 下の「止まる条件」に当たらないこと 貼る時機: いつでも（CHAT-1019-WRR-04 は判断待ち）。WRR-04 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/WRR の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/WRR を続けて使う（WRR-04 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・別々の大会の設定）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は、取り込んだ後に生成し直して解く。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-WRR-04 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら末尾に `/ 続き: CHAT-1019-WRR-05` を足す。

目的
WRR-04 のプレビューへの平野さんの回答（最高到達の書き方）を入れ、work/WRR を cloudflare へマージする。wrcr/ は未公開（noindex・メニュー未掲載）のまま入れる（公開は #611）。
決定（2026-10-19、平野さん）

* 最高到達の回数は、期を4期以上並べて「等」を付けるときだけ書く（WRC-09〈2026-10-18〉の「回数を添える」を置き換える）
   * 「最高到達 2位 1回（7期）」→「最高到達 2位（7期）」
   * 「最高到達 2位 2回（7期・6期）」→「最高到達 2位（7期・6期）」
   * 「最高到達 2位 3回（7期・6期・5期）」→「最高到達 2位（7期・6期・5期）」
   * 「最高到達 2位 4回（7期・6期・5期 等）」→ そのまま「最高到達 2位 4回（7期・6期・5期 等）」
* WRR-04 の残りの仮置き（検索の候補の右端「7期 2位」、決勝の順位が空の行は対象外、wrcr/ の質問欄の「現タイトルホルダー」の一文）は確定
* 他は問題なし。マージしてよい

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 最高到達の書き方は jwrc/・wrcr/ で共有の部品（`assets/league.js` の `passText()` など、`best_reach()` の周り）と、質問欄の選手のカード（`assets/houou_ask.js`）にある見込み（要確認）。同じ書き方を使う所は全部そろえる。houou/・ouka/ の「最高到達 A2」の形（リーグの段）は回数を出していないので変わらない見込み。変わる所があれば報告する
* マージで本番に入るもの（WRR-04 の報告）: ouka/ のランキングの下限で3件が外れる、jwrc/ に予選の行が無い決勝進出者4人（ダンプ大橋・伊藤優孝・勝又健志・佐々木寿人）が載り在籍者の表記が 320 → 324名、houou/ は変化なし、wrcr/ が未公開で入る、Worker `mj-ask` に `contest: "wrcr"` と `data-wrcr.json` が入る、`check-assets.yml` の許可に `wrcr`
* jwrc/ の質問欄の「現タイトルホルダー」の一文（CHAT-1019-WRC-15、cloudflare に入った）は WRR-04 で取り込み済み。WRC のチャットからの依頼: 取り込みで Worker の同じ関数・同じテストのファイルが衝突したら jwrc/・wrcr/ の両方の分を残して解き、両方の「現タイトルホルダーは？」が決勝の型（champions）に振り分けられることをテストで確かめる

手順

1. 直す: 最高到達の回数を「等」のときだけにする（名前の面・質問欄の選手のカード・ほかに同じ書き方の所）。テストを境界（1・2・3・4期）で足す。`python3 scripts/regenerate.py all` で生成し直し、houou/・ouka/ の生成物に、この直しでの差分が無いことを確かめる（WRR-04 までの差分〈ouka/ の下限の3件〉はそのまま）
2. マージの前の確かめ: origin/cloudflare を取り込み直し、生成し直す。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通り、jwrc/・wrcr/ の両方の「現タイトルホルダーは？」が champions に振り分けられることを確かめる。cloudflare との差分が、WRR-02〜05 の変更（wrcr/ 一式・共通の部品・ouka/ の下限の3件・jwrc/ の4人と在籍者の表記・Worker・`check-assets.yml`・文書とログ）だけであることを確かめる。作業ブランチの check（`check-assets.yml` を含む）が success であること
3. マージ: `git push origin work/WRR:cloudflare`（CLAUDE.md「ブランチ運用」のとおり push 直前に再 fetch して祖先を確かめる）。Workers Builds の mj・mj-ask が success になるのを待つ（上限15分）。本番で確かめる: wrcr/ のトップ・`?name=白鳥翔`（「最高到達 2位（7期）」）・ランキングの節浮き率が noindex のまま開けること、navbar・サイトマップ・`llms.txt` に wrcr/ が無いこと、jwrc/ の `?name=伊藤優孝` が開けること、ouka/ のランキングの期浮き率に二階堂瑠美・王政芳が無いこと。本番の質問欄に、wrcr/ から「現タイトルホルダーは？」（小川尚哉が答えに入る）と「7期の予選1位は？」、jwrc/ から「現タイトルホルダーは？」（答えが WRC リーグの優勝者のまま）を送る（計3問）。WRC-R の答えに WRC リーグの内容が混ざらないことを確かめる。docs/decisions/houou.md に WRR-05 の決定を足す。#366 と #611 に、マージしたこと（SHA）と公開の前の状態をコメントする

止まる条件

* 手順1で、houou/・ouka/ の生成物に最高到達の直しによる差分が出て、直しで消せない
* 手順2で、テストが通らない、cloudflare との差分に WRR-02〜05 で説明できないものがある、作業ブランチの check が失敗する
* 取り込みで、両方を残して解けない衝突が出る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* 手順3の本番の確かめで合わない所があれば、マージは戻さずに、合わない所を書いて「判断が必要なこと」に回す（止まらずに残りの確かめを進める）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、本番の確かめで合わなかった所（無ければ「なし」）、質問欄の3問の答えの要旨、公開（#611）の前に要ることを書く
* マージは冒頭の「マージ:」の行のとおり。最終報告に本番の確認用の URL（wrcr/ のトップ・`?name=白鳥翔`・jwrc/ の `?name=伊藤優孝`）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRR-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRR-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: 同じ Chat-Ref のコミットは 0 件
- 作業ブランチ: work/WRR はローカル・リモートとも 739add250 で一致（未マージ）。続けて使う
- 0. 「指示」欄の末尾は指示文の最後の行と一致。WRR-04 の `## 報告` の状態は「判断待ち」→ 末尾に「 / 続き: CHAT-1019-WRR-05」を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/WRR
- ログ: https://github.com/retroeater/mj/blob/work/WRR/docs/logs/CHAT-1019-WRR-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/WRR
- 確認用URL: なし
- マージ: 未
- issue: #366・#611
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b209d841）: https://github.com/retroeater/mj-logs/tree/main/guide/b209d841

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b209d841/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
