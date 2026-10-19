# CHAT-1019-OUK-21

- 着手日時: 2026-10-19
- 対象issue: なし（女流桜花の順位変動の PO の直しのマージと告知動画）
- ブランチ: work/1019-ouk20（前半、CHAT-1019-OUK-20 の続き。このログもここに置く）・work/1019-ouk19（後半、CHAT-1019-OUK-19 の続き）
- 着手時HEAD: work/1019-ouk20 5dcbb7f40・work/1019-ouk19 65c676a62

## 指示

【Claude作成】Claude Code 向け指示：順位変動の PO の直し（work/1019-ouk20）を cloudflare にマージし、本番で女流桜花の告知動画を撮り直す（動画は判断待ちで止まる） Chat-Ref: CHAT-1019-OUK-21 マージ: 前半（work/1019-ouk20）は下の確かめが通れば cloudflare にマージする（2026-10-19、平野さんがプレビューで動作確認 OK・第1期 A も PO として扱うことを決めた）。後半（告知動画、work/1019-ouk19）はマージしない（平野さんが動画を見て確定してから、別の指示で制作のスクリプトと文書をマージする。動画はリポジトリに入れない） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk20・work/1019-ouk19 の push と、work/1019-ouk20 の cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。前半は未マージの work/1019-ouk20、後半は未マージの work/1019-ouk19 を続けて使う。それぞれ `git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-OUK-20 と CHAT-1019-OUK-19 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-OUK-21` を足す。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。

前半: work/1019-ouk20 のマージ
決定（2026-10-19、平野さん）

* OUK-20 のプレビューの動作確認は OK（第20期 A の「PO」と9〜16位の順位、第15期 A の赤司美奈子の「–」、鳳凰戦 第42期 A1 の前原雄大の「–」）
* 第1期 A（39人のうち20人が最後の節を打った表）も PO として扱う（今のまま）
* OUK-20 のほかの仮置き（広い幅でも「PO」、PO の節に入った時点で出なかった選手が下へ動く、判定を `Contest.race_po` とシートの節の値で決める、読み上げ「プレーオフの終わりへ」）は今のまま

手順

1. 取り込み（必要なら）の後、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、差分が OUK-20 の種類（`assets/league_race.js`・ouka/race/ の JSON・文書）だけで、houou/ の生成物が差分0であることを確かめる
2. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。Workers Builds の成否を書く
3. 本番の確かめ: ouka/race/ の第20期 A で節のラベルの最後が「PO」で、再生の最後に9〜16位が順位付きで残ること、houou/race/ の第42期 A1 が今のままであることを headless Chrome で確かめて書く

止まる条件（前半）

* テストが落ちて直せない。houou/ の生成物に差分が出て直しで消せない
* マージ後に本番の houou/race/ や ouka/race/ が壊れた（ページが出ない・JS のエラー）ときは、CLAUDE.md の戻し方の節のとおりに戻して止まる（後半には進まない）

後半: 告知動画の撮り直し（work/1019-ouk19）
前半のマージと本番の確かめが済んでから、本番 `https://ryoei.pro/ouka/` で撮り直す。作り方は CHAT-1019-OUK-19 のログと `scripts/promo_video/ouka/` のとおり（1080×1920・30fps・mp4〈H.264・yuv420p、AAC〉・loudnorm -16 LUFS 前後・曲調 b・30〜60秒）。
決定（2026-10-19、平野さんが OUK-19 の仮置きを確かめた）

* 順位変動の最終結果: 16位の行まで映るように、最後に止める前にページを少し上へ送ってから止める（ページのコードは変えず、撮り方だけ）。節の番号の段が少し上に隠れてよい
* 順位変動の表は第20期 A のまま。OUK-20 の直しにより、第8節のラベルが「PO」になり、9〜16位が順位付きで残る形がそのまま映る
* 字幕を2か所変える:
   * ランキングの場面: 「歴代の記録を8部門で」→「期最高など歴代の記録」
   * 順位変動の場面: 「節ごとの順位の変動を再生」→「昇降級ボーダーの攻防を再現」（鳳凰戦の順位変動の告知の字幕と同じ）
* 今のままにするもの: リーグの推移の早送り7倍、ランキングは「期最高」への1回の切り替え、質問「第20期の女流桜花は？」と答えは全文を映す、制作のスクリプトは `scripts/promo_video/ouka/` に分けたまま、ほかの場面・字幕・秒の配分

前提（チャット側。実物に合わせて変えてよい）

* 質問欄の答えは OUK-19 で本番に聞いた答え（2026-10-19 00:59:52Z）をそのまま使い、本番へは送らない（送るなら1回まで。送ったら回数と答えをログに書く）
* 範囲の字幕「第1期〜第20期・178名」は、撮ったときの本番のトップの説明文の値に合わせる（変わっていればその値）
* 撮り直しで60秒を超えたら、順位変動の場面は削らず、ほかの場面の止めを詰めて60秒以内にし、詰めた所を書く

手順

1. 上の変更を `scripts/promo_video/ouka/` に入れ、本番で撮り直して動画を作る。最初のコマ・字幕の位置（上端から 80〜210px、下端から10%以上）・白いままのコマが無いこと・最終結果の画面で16位の行が切れていないことを確かめる
2. mp4 を `SendUserFile` で平野さんに送る。動画の長さ、場面ごとの秒、字幕の全文をログに書く。制作のスクリプトは work/1019-ouk19 に push する（マージしない）

止まる条件（後半）

* 本番の ouka/ が開けない
* cloudflare への push はしない（後半）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は判断待ち（動画）。報告の最初の方に、前半のマージのコミットの SHA と本番の確かめの結果を書く。CHAT-1019-OUK-20 のログの状態を「完了（CHAT-1019-OUK-21 でマージ）」の趣旨にする。「判断が必要なこと」に、動画の確定か直しか（平野さんが見て決める）と、新しく仮置きしたこと（あれば）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-21 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk20（前半）・work/1019-ouk19（後半）
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk20/docs/logs/CHAT-1019-OUK-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk20
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7c82be63）: https://github.com/retroeater/mj-logs/tree/main/guide/7c82be63

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7c82be63.md
