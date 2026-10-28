# CHAT-1028-SKP-24

- 着手日時: 2026-10-28
- 対象issue: なし
- ブランチ: work/1028-skp-handoff
- 着手時HEAD: c6bd0e38f

## 指示

【Claude作成】Claude Code 向け指示：SKP 系列（最強戦のリニューアル）の振り返りとレビューの申送りを、資料に規則として書き残す（docs のみ）
Chat-Ref: CHAT-1028-SKP-24
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変わるのは docs/notes・docs/decisions・docs/logs だけ）
貼る時機: いつでも（新しいセッションでも、前の SKP のセッションの続きでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-skp-handoff の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-skp-handoff を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-skp-handoff origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
SKP 系列（CHAT-1023-SKP-01〜CHAT-1028-SKP-23、最強戦の個人成績・ランキング・質問欄・新トップと対戦成績の公開）の振り返りと、Fable 5.1 のレビューから、手順に落とせる4点を資料に規則として書き残す。抽象的な心得は書かない。

### 決定（2026-10-28、平野さん）
- SKP 系列の振り返りとレビューの申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案。下の4点）

### 前提（チャット側。平野さんの決定ではない）
- 4点と、チャット側が選んだ層（docs/notes/chat-routines.md「申送り」の、計算的センサー → 推論的センサー → ガイド文 の順で検討した結果）:

| # | 知見（振り返り・レビューから） | 選んだ層 | 理由 | 書く先（案） |
|---|---|---|---|---|
| 1 | ランキングの部門を名前の案だけで決め、後で定義の調べ（SKP-09）・上位の比較（SKP-15）をしてから3回変えた。9部門は houou/ の 3×3 の格子の形から来ていた | 推論的センサー（受け手が最初の仮置きの一覧に表を含める手順） | 部門の良し悪しは人が表を見て決めるので、テストにできない。自律開発の止まりを増やさず、最初の一覧に入れる形にする | docs/notes/league-pages-compare.md「新しい大会を足すとき」 |
| 2 | 質問欄の誤りの確かめで3往復した。Q5（選手の成績）の誤りは、部門名の変更（予選出場 → 出場）と答えの直しを同じ指示に束ねた SKP-10 の後に出た | 推論的センサー（固定の問いを決め、部門の名前・集まりを変えるたびに本番で全問通し直す手順） | 答えは本番の Claude の出力で揺れるのでテストで固定できない。固定の問いの表は既にあるので、通し直す時機と、束ねない規則を足す | docs/notes/ask-worker.md「変更の指示を作るときの前提: 本番で送る固定の問い」 |
| 3 | Mの乱の反映（SKP-19）で、出場者の過去の卓のページの「他の対局」のカードの差分（45ページ）を見込みに入れ忘れて止まった | ガイド文（見込みの種類の一覧に1行足す） | 差分の見込みはチャット側が指示文に書くもので、検査にすると毎回の反映で誤検知の調整が要る。一覧に足すのが軽い | docs/notes/saikyo-page-design.md（シートの更新を反映する節。無ければ1章） |
| 4 | 告知動画を2回撮り直した。v2 は、公開のときに直すと決めていた「最強位」のピルが公開前の動画に映った（SKP-02 の決定24 → SKP-12）。v3 は、撮り直す場面を「ランキングが映る場面3・4」と限ったため、場面2の「9部門」を見落とした（Code が見つけた） | 推論的センサー（撮る前・撮り直す前に確かめる手順） | 何が映るかは撮った後のコマでしか確かめられず、テストにできない。撮る前の確かめを手順に足す | docs/notes/page-announcement.md「告知動画の型」 |

- 書く中身（規則と理由の一句だけ。事例は docs/notes/handover-archive-2026.md の該当の節へ）:
  1. 新しい大会のランキングを作るときは、最初の仮置きの一覧に、部門ごとの「数えるもの・数えないもの（件数）・上位10人・ほかの部門との重なり」を含める。部門の数は格子の形から決めず、格子を部門の数に合わせる（理由: 名前の案だけで決めると、定義と重なりが後で分かって部門を変え直すことになる）
  2. 質問欄は大会ごとに固定の問いを決め、「選手の成績」の型を必ず含める。部門の名前・集まりを変える指示では、固定の問いを本番で全部送り直す。部門名の変更と答えの直しは、別の指示に分ける（理由: 答えは本番で揺れ、束ねると誤りの原因が分けられない）
  3. 最強戦のシートの更新を反映するときの見込みの差分に、「その対局の出場者が出た過去の卓のページの『他の対局』のカード」を含める（理由: 出場者の数だけ過去のページが変わる）
  4. 告知動画を撮る・撮り直すときは、場面を限らず「変えた文言・数が映る場面を全部探す」。公開前に撮るときは、「公開のときに直す」と決めた項目が映らないかを撮る前に確かめる（理由: 撮り直しの原因の2回とも、撮る前に分かることだった）
- 前回の振り返りの約束（GDC の C: ガイド文とコードの食い違いの例を見る）は、この系列では該当の例が無かった（チャット側の振り返り）。追加の手は打たない

## 手順
1. 4つの書く先の文書の今の内容（該当の節）を読み、同じ趣旨の記述があれば置き換え・拡張する（処理をログに書く）。追記先と矛盾していて、どちらが正か判断が要るときだけ止まる。容量の制限のある文書なら、残り容量（上限・警告域との差）を測ってログに書く
2. 上の4点を、規則と理由の一句だけで書く。事例（どの Chat-Ref で何が起きたか）は docs/notes/handover-archive-2026.md の該当の節へ移す。docs/notes/chat-side-operations.md・CLAUDE.md・handover.md には足さない（足す必要があると判断したら、理由を書いて止まる）
3. docs/decisions/operations.md に、上の「決定」と4点の書いた先を足す（直近の振り返り・申送りの節の形に合わせる）。`python3 -m unittest discover -s scripts/tests` と、文書の容量の検査（`check-assets.yml` と同じもの）を流す

## 止まる条件
- 追記先と矛盾していて、どちらが正か判断が要る（どの文書のどの節かを書いて止まる）
- 容量の制限のある文書が上限を超える（測った値を書いて止まる）
- 4点のどれかを chat-side-operations.md・CLAUDE.md・handover.md に足す必要があると判断した
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-SKP-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-SKP-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1028-skp-handoff
- ログ: docs/logs/CHAT-1028-SKP-24.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1242ae11）: https://github.com/retroeater/mj-logs/tree/main/guide/1242ae11

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c6bd0e38.md
