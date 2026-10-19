# CHAT-1019-WRC-16

- 着手日時: 2026-10-19
- 対象issue: なし（関係: #599）
- ブランチ: work/1019-wrc14（マージしない。判断待ち）
- 着手時HEAD: d910b92cb

## 指示

【Claude作成】Claude Code 向け指示：（WRC-15 の後半の続き）work/1019-wrc14 に cloudflare を取り込み（docs/decisions/houou.md の衝突は両方を残して解く）、告知動画の順位変動の場面と字幕を直して撮り直す（判断待ちで止まる） Chat-Ref: CHAT-1019-WRC-16 マージ: 判断待ちで止まる（平野さんが動画を見て確定してから、別の指示で制作のスクリプトと文書をマージする。動画はリポジトリに入れない） 貼る時機: いつでも（CHAT-1019-WRC-15 は中断。前半はマージ済み）。WRC-15 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 起点のブランチから work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrc14 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-wrc14 を続けて使う（このログもここに置く）。origin/cloudflare を merge で取り込む（rebase・force push はしない）。`docs/decisions/houou.md` の衝突は、どちらも末尾への追記なので、両方の節を残し、日付・番号の順に並べて解く（WRC-15 の報告の案のとおり、WRC-14 の節を AIC-07 の節の前に置く形でよい）。解いた後の該当箇所の見出しの並びをログに書く。ほかのファイルが衝突したら、両方の追記・別々の大会の設定として両立するものだけ両方を残して解き、それ以外は解かずに止まる。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。取り込みの後に、CHAT-1019-WRC-14 と CHAT-1019-WRC-15 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-WRC-16` を足す（WRC-15 のログは cloudflare にあるので、取り込んだ作業ブランチ側で足す）。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。

目的
WRC-15 の後半（告知動画の撮り直し）を行う。前半（jwrc/ の質問欄の「現タイトルホルダーは？」と例の文）は WRC-15 で本番に入った（9b965c143・5c2333f99）。トップの質問欄の例の文が動画に映るので、本番の今の版で撮る。
決定（2026-10-19、平野さん。WRC-15 から変わらない）

* 動画の質問は「通算成績の1位は？」のまま（WRC-14 で保存した答えを使い、本番へは送らない）
* 順位変動の場面は、次の2つを両方入れる:
   * 字幕を「通過ボーダーの攻防を再現」から、ボーダーが映らなくても合う文に変える（案「各節の結果で順位が入れ替わる」）
   * 再生の途中で一度、表を通過ラインまで送ってラインを見せ、最後は1位が映る位置に戻して止める（最終結果は上位が映る位置で止める）
* ほかは WRC-14 の版のまま

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 通過ラインまで送る動き（WRC-15 の仮置きのまま）: 実際の速さの1節の後、早送りの途中で表を通過ライン（第19期は29人目の下）が映る位置まで送り、1〜1.5秒見せてから上へ戻し、最終結果で1位が映る位置で止める。通過ラインを見せる間だけ別の字幕（例「通過ライン」）を出すかは、見え方で決めて仮置きする。長さは60秒以内
* 範囲の字幕の人数（WRC-14 は「320名」）は、撮る日の本番のトップの説明文から読む（WRR の直しが先に本番に入っていれば 321名になる）

手順

1. 取り込み: 上の解き方で origin/cloudflare を取り込み、`python3 -m unittest discover -s scripts/tests` を回す。work/1019-wrc14 の cloudflare との差分が `scripts/promo_video/jwrc/`・ログ・文書だけであることを確かめる
2. 撮り直し: `scripts/promo_video/jwrc/` を直し、本番 `https://ryoei.pro/jwrc/` で撮り直して動画を作る（1080×1920・30fps・mp4〈H.264・yuv420p、AAC〉・loudnorm -16 LUFS 前後・曲調 b・30〜60秒）。最初のコマ・字幕の位置（上端から 80〜210px、下端から10%以上）・白いままのコマが無いこと・通過ラインが映るコマがあること・最終結果で1位の行が隠れず映ること・トップの例の文が「例：現タイトルホルダーは？」で映ることを確かめる
3. mp4 を `SendUserFile` で平野さんに送る。動画の長さ、場面ごとの秒（変わった所）、字幕の全文、通過ラインが映る時刻と画面の順位の範囲、最終結果の画面の順位の範囲をログに書く。制作のスクリプトは work/1019-wrc14 に push する（マージしない）

止まる条件

* 取り込みで、上の解き方で解けない衝突が出る
* 本番の jwrc/ が開けない
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は判断待ち。「判断が必要なこと」に、動画の確定か直しかと、新しく仮置きしたこと（字幕の文言、通過ラインを見せる秒と動き、通過ラインの間の字幕）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRC-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRC-16 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9b965c14）: https://github.com/retroeater/mj-logs/tree/main/guide/9b965c14

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b965c14/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
