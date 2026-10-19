# CHAT-1019-OUK-19

- 着手日時: 2026-10-19
- 対象issue: #591（公開後の告知）
- ブランチ: work/1019-ouk19（origin/cloudflare から作成）
- 着手時HEAD: 596809461

## 指示

【Claude作成】Claude Code 向け指示：女流桜花（ouka/）の告知動画の初版を作る（houou/ の告知と同じ構成＋順位変動） Chat-Ref: CHAT-1019-OUK-19 マージ: 判断待ちで止まる（docs/notes/page-announcement.md「進め方」のとおり。平野さんが動画を見て確定してから、別の指示で制作のスクリプトと文書をマージする。動画はリポジトリに入れない） 貼る時機: いつでも（CHAT-1019-OUK-18 とは別でも同じセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk19 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-ouk19 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。

目的
女流桜花（ouka/）は 2026-10-19 に公開した（CHAT-1019-OUK-16）。X の告知に付ける動画の初版を作り、mp4 を平野さんに送る。
決定（2026-10-19、平野さん）

* 告知は docs/notes/page-announcement.md の型で行う（投稿文と告知動画）
* 構成は houou/ の告知動画（v4・v5、`scripts/promo_video/houou/`、56.6秒）と同じ流れを ouka/ に置き換え、順位変動の場面も入れる（鳳凰戦は事前に順位変動の単独の告知があったので houou/ の告知では割愛したが、女流桜花は順位変動も初めての告知になるため）
* 見本の選手は清水香織（今の女流桜花）。選手の写真・名前・成績はそのまま映してよい（見本に選んだ選手として平野さんが指定）
* 長さは houou/ の告知と同じく30〜60秒の範囲（2026-10-15 の houou/ の決定に合わせる）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 構成（仮置き。秒は houou/ の v5 の配分を元に決める）:
   1. タイトル: 白地に「女流桜花」とひとことの説明（最初のコマからはっきり出す）
   2. 質問欄: 清水香織について質問し、答えと選手のカードが出る（houou/ の告知と同じ見せ方。質問は houou/ の告知の質問の形に合わせる。本番の Worker に聞くので1〜数回で済ませる）
   3. 個人成績のタブ: カードから個人成績へ。期別・通算・リーグのタブを houou/ の告知と同じ順に見せる（通算のグラフは全体が映るまでスクロール、など houou/ の確定の判断〈docs/decisions/houou.md の「houou/ の告知動画」の節〉に合わせる）
   4. ランキング: 部門の切り替え（houou/ の告知と同じく「期最高」で終わる形を基本にする。8部門）
   5. 順位変動: 期とリーグを選び、▶ に押す印を出してから押し、実際の速さ → 残りを3倍の早送り → 結果で止める（`scripts/promo_video/houou_race/` の撮り方・時計の進め方、`docs/notes/houou-race.md`「告知動画」）。表は第20期 A（清水香織が出ている表。出ていなければ、ページの既定の表）
   6. 締め: 白地に `img/ogp.png` の「ryoei.pro」が1秒で現れ、その後は動かない
* テロップ: houou/ の告知のテロップを ouka/ に置き換える。対応している範囲は「第1期〜第20期」。人数など数字はその日の本番の値を使う（houou/ の v4 で人数がずれた件があるため、撮った本番の値と合わせる）
* 撮る所: 公開済みの本番 `https://ryoei.pro/ouka/`。スマホの幅（iPhone 13、390×664・3倍）で高解像度のスクリーンショットの連番（page-announcement.md「告知動画の型」）
* 曲: 曲調 b（今までと同じ）
* 制作のスクリプト: `scripts/promo_video/houou/` を大会の設定で使い回すか、`scripts/promo_video/ouka/` に分けるかは、実物の作りから決める（使い回すときは houou/ の動画の作り直しが壊れないことを確かめる）
* 長さが60秒を超えるときは、ランキングの部門の数や個人成績のタブの見せ方を詰めて60秒以内にする（順位変動の場面は残す）

手順

1. houou/ の告知動画の制作のスクリプト・README・決定（docs/decisions/houou.md の「houou/ の告知動画」の節）と、順位変動の告知の撮り方を読む。上の構成で、秒・画面・テロップの表を作り、ログの `## 経過` に書く
2. 本番で撮り、動画を作る（1080×1920・30fps・mp4〈H.264・yuv420p、AAC〉・loudnorm -16 LUFS 前後）。最初のコマ・テロップの位置（上端から 80〜210px、下端から10%以上）・白いままのコマが無いことを確かめる
3. mp4 を `SendUserFile` で平野さんに送る。動画の長さ、場面ごとの秒、使った質問と答えの要旨、テロップの全文をログに書く。制作のスクリプトは作業ブランチに push する（マージしない）

止まる条件

* 本番の ouka/ が開けない・質問欄が答えない（答えないときは質問欄の場面を飛ばさず、止まって書く）
* houou/ の告知の制作のスクリプトを使い回して、houou/ の動画の作り直しが壊れる（直しで消せない）
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は判断待ち。「判断が必要なこと」に、動画の確定か直しか（平野さんが見て決める）、仮置きしたこと（各場面の秒、テロップの文言、質問の文、順位変動の表、使い回したか分けたか）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-19 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk19
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk19/docs/logs/CHAT-1019-OUK-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk19
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 59680946）: https://github.com/retroeater/mj-logs/tree/main/guide/59680946

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/59680946/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/59680946/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/59680946/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/59680946/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/59680946/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/59680946/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fbae687e.md
