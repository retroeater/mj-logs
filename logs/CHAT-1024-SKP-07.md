# CHAT-1024-SKP-07

- 着手日時: 2026-10-24
- 対象issue: #627・#623
- ブランチ: work/1024-skp-vid
- 着手時HEAD: 54aefe249

## 指示

【Claude作成】Claude Code 向け指示：最強戦リニューアルの本番の質問欄を試し、告知動画の初版を未公開の本番から作って送る（#627・#623）
Chat-Ref: CHAT-1024-SKP-07
マージ: 判断待ちで止まる（制作のスクリプトは作業ブランチに置き、動画の確定までマージしない。docs/notes/page-announcement.md「進め方」）
貼る時機: CHAT-1024-SKP-06 の完了の後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-skp-vid の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1024-skp-vid を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-skp-vid origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1024-SKP-06 のログの `## 報告` が「完了」で、マージ済みであることを確かめ、そうでなければ何もせずに止まる。この指示に着手した時点から、最強戦の告知の期間に入る（docs/notes/page-announcement.md「進め方」の決定、2026-10-20）。

## 目的
最強戦のリニューアル（新トップ・年度別・個人成績・ランキング・質問欄〈#627〉と対戦成績〈#623〉）を、告知動画を付けて公開と同時に告知する。この指示では、(1) 本番の質問欄で最強戦の問いを試し（CHAT-1023-SKP-02 の決定27「本番の試しはマージの後・公開の前に別の指示で」）、(2) 告知動画の初版を未公開の本番から撮って送る。公開はしない（動画の確定の後の別の指示）。

### 決定（2026-10-24、平野さん）
- 公開は、告知動画を作ってから、告知（X の投稿）と同時に行う
- 動画の構成は、チャットで示した案のとおり（約55秒・白地・縦長・操作デモ）:

| 場面 | 秒 | 画面 | テロップ |
|---|---|---|---|
| 1 タイトル | 0〜2.5 | 白地に「最強戦」「リニューアル」 | なし |
| 2 質問欄 | 2.5〜12 | 新トップの質問欄に「2025年度の最強位は？」と入力 → 答えと選手のカード | AIに言葉で質問（β版） |
| 3 個人成績 | 12〜24 | カードを押して個人成績へ。名前の下の「N回出場（…最強位）」→ 年度別タブ → 部門別タブ | 選手ごとの全出場と結果 |
| 4 ランキング | 24〜33 | ランキング「最強位」→「通過率」に切り替え（上位が映る位置で止める） | ランキング9部門 |
| 5 対戦成績 | 33〜45 | 左右に2人 → 勝敗の横棒 → 対局の記録 | 出場者同士の対戦成績 |
| 6 年度別 | 45〜51 | 歴代最強位 → 年度のページ | 2011年度〜の全対局・全卓 |
| 7 締め | 51〜55 | 白地に「ryoei.pro」 | なし |

- 見本の選手は桑田憲汰（人が映ってよい）。対戦成績は左に桑田憲汰、右に桑田憲汰と同卓の回数が最も多い相手
- 曲はこれまでと同じ（曲調 b、軽快・120 BPM）
- 投稿文はチャットで示した案（文は平野さんが直して投稿する。この指示では扱わない）

### 前提（チャット側。平野さんの決定ではない）
- 撮るのは未公開の本番: 新トップは仮の URL `/saikyo/next/`、ほかは `/saikyo/players/`・`/saikyo/ranking/<部門>/`・`/saikyo/vs/`・`/saikyo/years/`（いずれも noindex。要確認）。houou/ の告知（HOU-48）と同じく、公開を待たずに撮る。画面のパンくず・URL の表示に「next」が映らないようにする
- 質問欄の答えの文は毎回変わりうる。撮るときに得た答えが、手順1で確かめた事実と食い違えば撮り直す
- 対戦成績の右の相手は、桑田憲汰と同卓の最も多い人をデータから選ぶ（同数なら新しく同卓した人。名前と回数をログに書く）。個人成績の「対戦成績を見る ›」は公開まで出ないので映らない
- 作り方は docs/notes/page-announcement.md「告知動画の型」と、最も近い実例 `scripts/promo_video/wrcr/`・`scripts/promo_video/saikyo/`（README.md）に従う

## 手順
1. 本番の質問欄（`/api/ask`、`contest: "saikyo"`）に、次の5問を1回ずつ送る: 「2025年度の最強位は？」「最強位をいちばん多く取ったのは？」「通過率がいちばん高いのは？」「予選出場がいちばん多いのは？」「瀬戸熊直樹の最強戦の成績は？」。それぞれの答えの全文・出典・選手のカードを `## 経過` に引用し、ランキングのページ・個人成績の値（SKP-06 の後）と突き合わせる（同率の答え〈予選出場の1位は複数人の見込み〉も、ページの順位と食い違わないか）。食い違いがあれば、撮らずに止まる。docs/notes/ask-worker.md「本番で送る固定の問い」の表に最強戦の5問と見込みの答えを足す
2. 上の構成で動画を撮って作る。曲・形式・テロップの置き方は「告知動画の型」のとおり。止める位置・映る選手は、撮った後にコマで確かめる（テロップが名指すものが映るか、ランキングは上位が映るか、白いままのコマが無いか）。長さ・各場面の秒の実際を表にして書く
3. mp4 を `SendUserFile` で送り、制作のスクリプトを作業ブランチの `scripts/promo_video/saikyo_renewal/`（README.md つき）に置いて push する。判断待ちで止まる（動画の直し・確定は次の指示）

## 止まる条件
- 手順1の答えが、ページの値と食い違う（どの問いの・どこが・なぜかを書いて止まる）
- 撮る画面が SKP-06 の決定どおりでない（部門名・並び・名前の下の行）
- 本番の質問欄が答えない、または1日の上限に当たった（件数を書いて止まる）
- 未マージの work/ ブランチが `scripts/promo_video/` の同じファイルを変えている、または取り込みで衝突する

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-SKP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-SKP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1024-skp-vid
- ログ: docs/logs/CHAT-1024-SKP-07.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d53626ec）: https://github.com/retroeater/mj-logs/tree/main/guide/d53626ec

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c8aed737.md
