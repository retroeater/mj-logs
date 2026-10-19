# CHAT-1019-OUK-22

- 着手日時: 2026-10-19
- 対象issue: なし（女流桜花の告知動画）
- ブランチ: work/1019-ouk19（CHAT-1019-OUK-21 の後半の続き）
- 着手時HEAD: 482714d60

## 指示

【Claude作成】Claude Code 向け指示：女流桜花の告知動画の順位変動の最終結果を、1位が映る位置で止める形に戻して撮り直す（判断待ちで止まる）
Chat-Ref: CHAT-1019-OUK-22
マージ: 判断待ちで止まる（平野さんが動画を見て確定してから、別の指示で制作のスクリプトと文書をマージする。動画はリポジトリに入れない）
貼る時機: いつでも（CHAT-1019-OUK-21 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk19 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1019-ouk19 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-OUK-21 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1019-OUK-22` を足す。
   **着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。**

## 目的・決定（2026-10-19、平野さん）
- OUK-21 の撮り直しでは、順位変動の最終結果で16位まで映すために表を送った結果、上端に貼り付く節のラベルの段の下に1位の行（古谷知美）が隠れた
- 決定: 最終結果は **1位が映る位置で止める**（OUK-19 の初版と同じ止め方。16位の行が画面の下で切れてよい）。OUK-21 で足した「止める前に表を送る 0.4 秒」はやめる
- ほかは OUK-21 の版のまま（字幕「期最高など歴代の記録」「昇降級ボーダーの攻防を再現」、PO の直しの入った本番の表、リーグの推移の早送り7倍、ランキングは「期最高」への1回の切り替え、質問と答えの全文、`scripts/promo_video/ouka/` に分けたまま）

### 前提（チャット側。実物に合わせて変えてよい）
- 送る 0.4 秒をやめて空く分は、最終結果で止める秒（範囲の字幕）を OUK-19 の 2.6 秒に戻す。60秒を超えるなら 2.4 秒のままにする
- 止めたコマで、1位の行が節のラベルの段に隠れず全部映ることを確かめる（`mustFit()` を1位の行に向ける）
- 質問欄の答えは OUK-19 で保存した答えを使い、本番へは送らない
- 本番 `https://ryoei.pro/ouka/` で撮る

## 手順
1. `scripts/promo_video/ouka/` を直し、本番で撮り直して動画を作る（1080×1920・30fps・mp4〈H.264・yuv420p、AAC〉・loudnorm -16 LUFS 前後・曲調 b・30〜60秒）。最初のコマ・字幕の位置（上端から 80〜210px、下端から10%以上）・白いままのコマが無いこと・最終結果で1位の行が映ることを確かめる
2. mp4 を `SendUserFile` で平野さんに送る。動画の長さ、場面ごとの秒（変わった所）、最終結果の画面に映る順位の範囲をログに書く。制作のスクリプトは work/1019-ouk19 に push する（マージしない）

## 止まる条件
- 本番の ouka/ が開けない
- cloudflare への push はしない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は判断待ち。「判断が必要なこと」に、動画の確定か直しか（平野さんが見て決める）と、新しく仮置きしたこと（あれば）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-22 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章

- Chat-Ref の重複なし。「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1019-ouk19 はリモートとローカルが同じ（482714d60）
- `git merge-base --is-ancestor origin/cloudflare HEAD` が偽だったので、指示のとおり `origin/cloudflare`（8ea27fb30）を merge で取り込もうとした。`docs/decisions/houou.md` の末尾が衝突した:
  - こちら（work/1019-ouk19）: 「## 2026-10-19（CHAT-1019-OUK-19）」の節（告知動画の決定）
  - cloudflare: 「## 2026-10-19（CHAT-1019-OUK-20）」「## 2026-10-19（CHAT-1019-OUK-21）」の節（PO の直しと動画の決定）
  - どちらも同じ位置への追記どうしで、両方を日付・番号の順（OUK-19 → OUK-20 → OUK-21）に並べれば両立する
- docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」の規則（生成物以外の衝突は、指示文が解き方を書いていなければ解かずに止まる）に当たるため、`git merge --abort` で取り込みを取り消して止まった（作業ツリーは取り込み前のまま。未コミットの変更は無かった）
- このため、CHAT-1019-OUK-21 のログ（cloudflare にある）の状態への ` / 続き: CHAT-1019-OUK-22` の追記と、撮り直しは行っていない

## 報告

- 状態: 完了（CHAT-1019-OUK-24 でマージ） / 続き: CHAT-1019-OUK-23
- ブランチ: work/1019-ouk19
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-OUK-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk19
- 確認用URL: なし
- マージ: 済み（CHAT-1019-OUK-24 でマージ。この指示では取り込みを取り消して中断した）
- issue: #591（CHAT-1019-OUK-24 でコメント）
- 判断が必要なこと: なし（2026-10-19 に平野さんが CHAT-1019-OUK-23 の動画で確定し、X に投稿した）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ff5acfce）: https://github.com/retroeater/mj-logs/tree/main/guide/ff5acfce

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff5acfce/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e223c8f.md
