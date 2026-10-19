# CHAT-1019-WRR-07

- 着手日時: 2026-10-19
- 対象issue: なし（#611 の告知。#611 はクローズ済み）
- ブランチ: work/1019-wrr07
- 着手時HEAD: de6f0f672

## 指示

【Claude作成】Claude Code 向け指示：WRC-Rリーグ（wrcr/）の告知動画の初版を作る（WRCリーグの告知と同じ構成。判断待ちで止まる） Chat-Ref: CHAT-1019-WRR-07 マージ: 判断待ちで止まる（docs/notes/page-announcement.md「進め方」のとおり。平野さんが動画を見て確定してから、別の指示で制作のスクリプトと文書をマージする。動画はリポジトリに入れない） 貼る時機: CHAT-1019-WRR-06（wrcr/ の公開）が cloudflare に入った後（0章で確かめる）。WRR-06 とは別のセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrr07 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-wrr07 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-wrr07 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。`git log origin/cloudflare --grep="CHAT-1019-WRR-06" --oneline` が空なら止まる（wrcr/ がまだ公開されていない）。 着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。

目的
WRC-Rリーグ（wrcr/）を公開した（CHAT-1019-WRR-06）。X の告知に付ける動画の初版を作り、mp4 を平野さんに送る。
決定（2026-10-19、平野さん）

* 告知は鳳凰戦・女流桜花・WRC リーグと同じく、docs/notes/page-announcement.md の型で行う（投稿文と告知動画）。WRC-R のページは新しく作ったもの（「リニューアル」でなく「公開」）
* 構成は WRC リーグの告知動画（`scripts/promo_video/jwrc/`、44.3秒、CHAT-1019-WRC-16 で確定）と同じ流れを wrcr/ に置き換える。長さは30〜60秒
* 見本の選手は現タイトルホルダーの小川尚哉（第1〜7期の予選すべてに出ている）。写真・名前・成績はそのまま映してよい
* 質問欄の質問は「現タイトルホルダーは？」（答えに小川尚哉が出て、選手のカードから個人成績へつなぐ）
* ランキングの場面は1回の切り替えで、「通過率」に切り替える（WRC リーグの告知は通過回数だったので変える）
* 順位変動は第7期。jwrc/ と同じく、早送りの途中で一度通過ラインまで送って見せ、最終結果は1位が映る位置で止める（下位・最下位の行だけを映す送り方はしない）
* 曲は今までと同じ（曲調 b）
* 投稿の日時は未定（動画を確定してから決める）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 構成（仮置き。秒は jwrc/ の版の配分を元に決める）: 1 タイトル（白地に「WRC-Rリーグ」とひとことの説明。最初のコマからはっきり出す）→ 2 質問欄（「現タイトルホルダーは？」→答え→選手のカード。本番の Worker に聞くのは1〜数回にし、答えは保存して撮り直しに使う）→ 3 個人成績（カードから。期別〈「通過」「敗退」のチップ〉・通算・部門別の3つのタブを jwrc/ と同じ見せ方で。名前の面の「最高到達」「出場 n期（通過n回）」が映るようにする）→ 4 ランキング（通過率へ1回切り替え）→ 5 順位変動（第7期。期を選び、▶ に押す印を出してから押し、実際の速さ → 早送りの途中で一度通過ラインまで送る → 結果で1位が映る位置で止める）→ 6 締め（白地に `img/ogp.png` の「ryoei.pro」が1秒で現れ、その後は動かない）
* 字幕: jwrc/ の字幕を wrcr/ に置き換える。対応している範囲・人数などの数字はその日の本番の値（第1〜7期）。字幕が名指すもの（通過ラインなど）は、その字幕が出ている間に画面に映るコマがあることを撮った後に確かめる（docs/notes/page-announcement.md「告知動画の型」）
* 小川尚哉が通過率のランキングに載っているか（下限・規定の5期以上）は確かめていない（要確認）。載っていなくてもランキングの場面は通過率のまま撮り、上位が映る位置で見せる
* 撮る所: 公開済みの本番 `https://ryoei.pro/wrcr/`。スマホの幅（iPhone 13、390×664・3倍）で、jwrc/ と同じ撮り方
* 制作のスクリプト: `scripts/promo_video/jwrc/` を元に `scripts/promo_video/wrcr/` に分ける（jwrc/ のときに ouka/ と分けたのと同じ。使い回すなら jwrc/ の作り直しが壊れないことを確かめる）

手順

1. jwrc/ の告知動画の制作のスクリプト・README と、docs/notes/page-announcement.md・jwrc/ の告知の記録（CHAT-1019-WRC-14〜16 のログ、docs/notes/jwrc-top.md の「告知動画」の節）を読む。上の構成で、秒・画面・字幕の表を作り、ログの `## 経過` に書く
2. 本番で撮り、動画を作る（1080×1920・30fps・mp4〈H.264・yuv420p、AAC〉・loudnorm -16 LUFS 前後）。最初のコマ・字幕の位置（上端から 80〜210px、下端から10%以上）・白いままのコマが無いこと・順位変動で通過ラインが映るコマがあること・結果の画面で1位の行が隠れず映ることを確かめる
3. mp4 を `SendUserFile` で平野さんに送る。動画の長さ、場面ごとの秒、質問の答えの要旨、字幕の全文、順位変動の結果の画面に映る順位の範囲をログに書く。制作のスクリプトは作業ブランチに push する（マージしない）

止まる条件

* 0章の確かめに当たる
* 本番の wrcr/ が開けない・質問欄が答えない（答えないときは質問欄の場面を飛ばさず、止まって書く）
* jwrc/ の制作のスクリプトを使い回して、jwrc/ の動画の作り直しが壊れる（直しで消せない）
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は判断待ち。「判断が必要なこと」に、動画の確定か直しか（平野さんが見て決める）と、仮置きしたこと（各場面の秒、字幕の文言、ランキングで映る範囲）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRR-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRR-07 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- 識別子確認: 同じ Chat-Ref のコミットは 0 件。`git log origin/cloudflare --grep="CHAT-1019-WRR-06"` は空でない（e6b5408a2・6bb0f617c）
- 作業ブランチ: work/1019-wrr07 はローカル・リモートとも無し → `git checkout -b work/1019-wrr07 origin/cloudflare`（de6f0f672）
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1019-wrr07
- ログ: https://github.com/retroeater/mj/blob/work/1019-wrr07/docs/logs/CHAT-1019-WRR-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-wrr07
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 405e702e）: https://github.com/retroeater/mj-logs/tree/main/guide/405e702e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/405e702e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0a1e266.md
