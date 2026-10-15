# CHAT-1015-HOU-48

- 着手日時: 2026-10-15
- 対象issue: #518・#540・#542
- ブランチ: work/1015-hou5
- 着手時HEAD: 95d46b4d9

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦 houou/ の告知動画（約55秒・縦長）の初版を、未公開の本番で撮って作る
Chat-Ref: CHAT-1015-HOU-48
マージ: 判断待ちで止まる（平野さんが動画を見て、直すか確定かを決めるため。docs/notes/page-announcement.md「進め方」3）
貼る時機: いつでも（新しいセッションは任意。HOU のセッションの続きでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou5 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-hou5 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

## 目的

鳳凰戦の新ページ群 houou/（#518、公開は #540）を X で告知するための動画の初版を作り、mp4 を平野さんに送る。告知の型は docs/notes/page-announcement.md（順位変動の告知が元）。

### 決定（2026-10-15、平野さん）

- 告知動画を作る。操作で見せる分量が多いので、長さは30〜60秒程度にする（型の「15〜30秒」をこの動画では置き換える）
- 公開を待たず、今の未公開の本番（noindex のまま）で撮る（2026-10-14 の「告知動画は公開の後に別の指示で、本番の最終形で撮る」を置き換える）
- 見本にする選手は白鳥翔
- 質問欄（データ・コンシェルジュ #542）を動画に映す
- 曲は今までと同じ（曲調 b、タイトル戦・順位変動と同じ曲）

### 前提（チャット側。平野さんの決定ではない）

- 形・最初のコマ・テロップの置き場・実画面の撮り方・締めの画面は、docs/notes/page-announcement.md「告知動画の型」のとおり（縦長 1080×1920・30fps・白地に黒字・-16 LUFS 前後・iPhone 13 の幅〈390×664・3倍〉の高解像度の連番・846×1440 の枠）。時間で動く部分の撮り方は docs/notes/houou-race.md「告知動画」の「撮り方」と `scripts/promo_video/houou_race/` を参考にする（`scripts/promo_video/lib/` へまとめるかは受け手が決め、ログに書く。houou_race/ と title/ は変えない）
- 構成の案（チャット側の叩き台。実物に合わせて秒・順序・テロップを変えてよい。変えたら表でログに書く）:

| 場面 | 秒（案） | 画面 | テロップ（案） |
|---|---|---|---|
| 1 タイトル | 0〜2.5 | 白地に「鳳凰戦」「個人成績・ランキング・順位変動」（最初のコマからはっきり出す） | なし |
| 2 トップ | 2.5〜5.5 | houou/ のトップ。質問欄と3項目に順に枠を出す | 鳳凰戦のデータをひとつに |
| 3 質問欄 | 5.5〜13 | 質問を入れて送る → 答えが出る（答えの中のリンクが見える所まで） | 質問すると、データから答える |
| 4 選手を開く | 13〜16 | 個人成績へ進み、白鳥翔を開く（開き方は実物の部品に合わせる） | 選手ごとの成績 |
| 5 期別 | 16〜21 | 名前の面と期の行を送る | 全期の成績を一覧で |
| 6 通算 | 21〜25 | 通算のグラフ | 通算得点の推移 |
| 7 リーグ | 25〜37 | 名前の面の ▶ に押す印を出してから押す → 写真からアイコンが落ちる → 登る（前半は実際の速さ、後半は早送り） | リーグの昇降級を再生 |
| 8 部門別 | 37〜42 | 9部門の値と順位 | ランキング9部門の順位 |
| 9 ランキング | 42〜49 | 部門の行を押す → ランキングで白鳥翔の行が色付きで出る → 部門のチップを切り替える | 歴代の記録を9部門で |
| 10 範囲 | 49〜52 | ランキングの表で止める | 対応している範囲（期と人数は撮る日のデータで確かめて書く） |
| 11 締め | 52〜55.5 | 白地に `img/ogp.png` の「ryoei.pro」が1秒で現れ、その後は動かない | なし |

- 質問欄の質問の案: 「通算得点の歴代1位は？」（答えにランキング `houou/ranking/total/` へのリンクが出る見込み、要確認）。答えが速く・短く・正しく出るものを受け手が選んでよい（選んだ質問と答えの全文をログに書く）。答えの数字・選手名はランキングのページと照らし、食い違えば使わない。本番の質問欄に送る回数は5回までにし、送った回数と時刻をログに書く（10/20 の試行の集計から除けるように）。答えが出るまでの待ちは動画では詰めてよい（詰めたことをログに書く）
- 人が映ること: 白鳥翔の写真・名前・成績、ランキングと質問欄の答えに出るほかの選手の名前は、そのまま映す（タイトル戦・順位変動の告知と同じ扱いの案）
- 公開前に見た目が変わったら撮り直す前提（撮り直しは別の指示）。制作のスクリプトは作業ブランチに置き、確定するまでマージしない。動画・連番・素材はコミットしない
- 本番は未公開なので、開く URL には `?v=<未使用の値>` を付けて最新を読ませる。noindex のページを開くだけで、公開に関わる変更（noindex・navbar・サイトマップ・`llms.txt`）はしない

## 手順

1. 着手の確かめ: #518・#540・#542 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に `scripts/promo_video/` の同じファイルの変更があるかを確かめ、ログに書く。本番の houou/ の各ページが cloudflare の最新の版（HOU-45 以降）で配られていることを確かめる。
2. 撮影と制作: 構成の案のとおりに撮って作る（`scripts/promo_video/houou/` などの新しいフォルダ。作り直しの手順をそのフォルダの `README.md` に書く）。リーグの再生は時計を撮る側で1フレームずつ進め、アイコンが落ち始め・着地・寄った後・終わりで画面の中にあることを座標でログに書く（docs/notes/houou-top.md「確かめ方（再生）」）。白いままのコマ・写真の読み込み前のコマが無いことを確かめる。
3. 確かめと送付: 出来た mp4 の長さ・解像度・fps・ラウドネス（測った値）と、場面ごとの秒とテロップの表をログに書く。最初のコマと各場面の1コマずつを PNG にして見て、テロップが見せたい所を隠していないこと・下の端から10%以上離れていることを確かめる。mp4 を `SendUserFile` で平野さんに送る（ファイル名は `houou-promo-v1.mp4`）。投稿文は書かない（公開の後にチャット側が出す）。

## 止まる条件

- #518・#540・#542 に他のセッションの着手中のコメントがある
- 未マージの work/ ブランチが `scripts/promo_video/` の同じファイルを変えている、または取り込みで衝突する（別のファイルの変更では止まらず、ログに書く）
- 本番の houou/ が cloudflare の最新の版で配られていない
- 質問欄が5回までで使える答えを返さない（エラー・上限・答えの誤り）。このときは場面3を抜いた版を作り、抜いた理由を報告に書いて送る（止まらずに進めてよい）
- ページのコード・生成物・本番の設定を変えないと撮れない（変えずに止まり、何が要るかを報告する）
- 出来た動画が60秒を超える（構成を詰めて60秒以内に収める。収まらなければ、詰め方の案を報告に書いて止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、動画を確定するか直すか（見る物は送った mp4）を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-HOU-48.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-HOU-48 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-15 着手。HOU-47 と同じセッション（再起動の後）で続けた。`CHAT-1015-HOU-48` のコミットは無し。`work/1015-hou5` はローカル・リモートとも無かったため `origin/cloudflare` から作った

- 0. 指示欄の末尾は指示文の最後の行と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 1. 着手の確かめ: #518・#540・#542 に他のセッションの着手中は無し（最新はどれも自分の着手中の解除・報告）。#540 に着手中を書いた。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に `scripts/promo_video/` の変更は無し。本番（`?v=<未使用の値>`）の `houou/index.html`・`houou/players/index.html`・`houou/ranking/total/index.html`・`assets/houou.js`・`style.css` は origin/cloudflare（95d46b4d9。houou/ の最後の変更は HOU-45 の e54f18808）とバイト一致
- 質問欄: `/api/ask/status` は `{"status":"open","remaining":10,"max":200}`。送った回数 1回目: 2026-10-15 09:28:09 UTC（18:28 JST）、curl で「通算得点の歴代1位は？」。答えの全文（約5秒）: `{"status":"answered","lead":"","answer":"通算得点の歴代1位はHIRO柴田で、通算得点は2074.4です。","notes":[],"asof":["鳳凰戦リーグ 第43期前期終了時点"],"sources":[{"label":"鳳凰戦 ランキング（通算得点）","url":"/houou/ranking/total/"}],"remaining":9}`。ランキング（通算得点）の1位 HIRO柴田 +2074.4 と一致。動画ではこの答えをそのまま使い、撮る側で `/api/ask` への POST にこの本文を返す（本番へ送る回数を増やさないため。撮るたびに送らない）
## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 95d46b4d）: https://github.com/retroeater/mj-logs/tree/main/guide/95d46b4d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
