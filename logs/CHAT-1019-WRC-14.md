# CHAT-1019-WRC-14

- 着手日時: 2026-10-19
- 対象issue: #599（告知。関係: #594）
- ブランチ: work/1019-wrc14（マージしない。判断待ち）
- 着手時HEAD: d8b213689

## 指示

【Claude作成】Claude Code 向け指示：WRCリーグ（jwrc/）の告知動画の初版を作る（女流桜花の告知と同じ構成。判断待ちで止まる） Chat-Ref: CHAT-1019-WRC-14 マージ: 判断待ちで止まる（docs/notes/page-announcement.md「進め方」のとおり。平野さんが動画を見て確定してから、別の指示で制作のスクリプトと文書をマージする。動画はリポジトリに入れない） 貼る時機: いつでも（CHAT-1019-WRC-13 とは別のセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrc14 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-wrc14 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。rebase・force push はしない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-WRC-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-WRC-14` を足す。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。

目的
WRCリーグ（jwrc/）は 2026-10-19 に公開した（CHAT-1019-WRC-12）。X の告知に付ける動画の初版を作り、mp4 を平野さんに送る。
決定（2026-10-19、平野さん）

* 告知は鳳凰戦・女流桜花と同じく、docs/notes/page-announcement.md の型で行う（投稿文と告知動画）
* 構成は女流桜花の告知動画（`scripts/promo_video/ouka/`、59.6秒、CHAT-1019-OUK-23 で確定）と同じ流れを jwrc/ に置き換える（順位変動の場面を含む）
* 長さは30〜60秒（houou/・ouka/ と同じ）
* 見本の選手は石立岳大（通算1位・優勝2回）。写真・名前・成績はそのまま映す。現タイトルホルダーの紺野真太郎はシード枠からの優勝で、予選に出たのは第9期までのため見本にしない
* ランキングの場面は1回の切り替えで、WRC にしか無い部門（通過率など）に切り替える。順位変動の場面は第19期の表で、上位が映る位置で止める
* 順位の表を止める画面は上位が映る位置にする。下位・最下位の行だけを映す送り方はしない（2026-10-19 の ouka/ の決定。docs/notes/page-announcement.md「告知動画の型」）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 構成（仮置き。秒は ouka/ の版の配分を元に決める）:
   1. タイトル: 白地に「WRCリーグ」とひとことの説明（最初のコマからはっきり出す）
   2. 質問欄: 石立岳大が答えに出る質問をし、答えと選手のカードが出る（質問の形は ouka/ の告知に合わせる。例「通算成績の1位は？」。本番の Worker に聞くのは1〜数回にし、答えは保存して撮り直しに使う）
   3. 個人成績: カードから個人成績へ。期別（「通過」「敗退」のチップ）・通算・部門別の3つのタブを ouka/ と同じ見せ方で。名前の面の「最高到達」「出場 n期（通過n回）」が映るようにする
   4. ランキング: 上の決定のとおり1回の切り替え。切り替え先の部門（通過率・通過回数・連続通過のどれか）は、石立岳大が上位に映るものを選ぶ
   5. 順位変動: 期を選び、▶ に押す印を出してから押し、実際の速さ → 残りを早送り → 結果で止める（ouka/ と同じ撮り方。止める画面は上の決定のとおり上位が映る位置）。表は第19期。通過ラインがどう見えるかを確かめる
   6. 締め: 白地に `img/ogp.png` の「ryoei.pro」が1秒で現れ、その後は動かない
* 字幕: ouka/ の字幕を jwrc/ に置き換える（女流桜花の「昇降級ボーダーの攻防を再現」は、昇降級が無いので通過ボーダーの言い方にする、など）。対応している範囲・人数などの数字はその日の本番の値を使う
* 撮る所: 公開済みの本番 `https://ryoei.pro/jwrc/`。スマホの幅（iPhone 13、390×664・3倍）で、ouka/ と同じ撮り方
* 曲: 曲調 b（今までと同じ）
* 制作のスクリプト: `scripts/promo_video/ouka/` を元に `scripts/promo_video/jwrc/` に分ける（ouka/ のときに houou/ と分けたのと同じ。使い回すなら ouka/ の作り直しが壊れないことを確かめる）

手順

1. ouka/ の告知動画の制作のスクリプト・README と、docs/notes/page-announcement.md・ouka/ の告知の記録（CHAT-1019-OUK-19・21〜24 のログ、docs/notes/ouka-top.md の「告知動画」の節）を読む。上の構成で、秒・画面・字幕の表を作り、ログの `## 経過` に書く
2. 本番で撮り、動画を作る（1080×1920・30fps・mp4〈H.264・yuv420p、AAC〉・loudnorm -16 LUFS 前後）。最初のコマ・字幕の位置（上端から 80〜210px、下端から10%以上）・白いままのコマが無いこと・順位変動の結果の画面で1位の行が隠れず映ることを確かめる
3. mp4 を `SendUserFile` で平野さんに送る。動画の長さ、場面ごとの秒、使った質問と答えの要旨、字幕の全文、順位変動の結果の画面に映る順位の範囲をログに書く。制作のスクリプトは作業ブランチに push する（マージしない）

止まる条件

* 本番の jwrc/ が開けない・質問欄が答えない（答えないときは質問欄の場面を飛ばさず、止まって書く）
* ouka/ の制作のスクリプトを使い回して、ouka/ の動画の作り直しが壊れる（直しで消せない）
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は判断待ち。「判断が必要なこと」に、動画の確定か直しか（平野さんが見て決める）と、仮置きしたこと（各場面の秒、字幕の文言、質問の文、ランキングの部門）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRC-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRC-14 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 268fdb4e）: https://github.com/retroeater/mj-logs/tree/main/guide/268fdb4e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e223c8f.md
