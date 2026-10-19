# CHAT-1019-WRC-20

- 着手日時: 2026-10-19
- 対象issue: なし
- ブランチ: work/1019-wrc20 → cloudflare へマージ（ドキュメントのみ）
- 着手時HEAD: 77431408e

## 指示

【Claude作成】Claude Code 向け指示：（申送り）WRC の振り返りの知見2件を、公開前の確かめ（new-page-checklist.md）と告知動画の確かめ（page-announcement.md）に1行ずつ足す
Chat-Ref: CHAT-1019-WRC-20
マージ: ドキュメントのみなので、完了報告のうえ cloudflare へ入れてよい（変えるのは docs/new-page-checklist.md・docs/notes/page-announcement.md・docs/logs だけ。コード・生成物・シートは変えない）
貼る時機: いつでも（CHAT-1019-WRC-19 は完了）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrc20 の作成・push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-wrc20 を origin/cloudflare から作る。取り込みの衝突は CLAUDE.md「ブランチ運用」のとおり（`docs/decisions/*.md`・`docs/logs/` の末尾への追記どうしは両方を残して解く。それ以外は止まる）。rebase・force push はしない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

2026-10-19 の WRC のチャットの振り返り（Fable 5.1 のレビュー済み）で出た知見のうち、手順で確かめられるもの2件を文書に残す。

### 決定（2026-10-19、平野さん）

- 振り返りの後に「申送り」として、知見を文書に残す（chat-routines.md「申送り」の手順）

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

知見と、書く層（chat-routines.md「申送り」の順: 計算的センサー → 推論的センサー → ガイド文）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| ① 大会のページ（houou/・ouka/・jwrc/ など）を公開する前に本番の質問欄で聞いたのが、ランキングの問い1問だけだった。トップの例の文の問いと「現タイトルホルダー（現鳳凰位・現女流桜花）は？」に答えられないまま公開し、平野さんが見つけた（CHAT-1019-WRC-15） | 推論的センサー: `docs/new-page-checklist.md` に、公開の確かめで Code が本番に聞く問いを1行足す | 答えは本番の Worker（Claude）を呼ばないと確かめられず、テスト（計算的センサー）にできない。公開の指示は new-page-checklist.md を読むので、確かめの手順として置くのがいちばん守られる |
| ② 告知動画で、字幕が指すもの（「通過ボーダーの攻防を再現」の通過ライン）が、止め方の決定（上位が映る位置で止める）のせいで画面に一度も映らなかった（CHAT-1019-WRC-14 → 16 で撮り直し） | 推論的センサー: `docs/notes/page-announcement.md`「告知動画の型」の確かめの項に、字幕が名指すものが映るコマがあることを確かめる1行を足す | 撮った後にコマを見て確かめる手順として書ける。字幕の文言は自由なので検査（計算的センサー）にはしにくい |

書かないもの（抽象的な心得なので、手順に落とせない）: 見本の選手をデータで選ぶこと、追記どうしの衝突の解き方（CLAUDE.md「ブランチ運用」と docs/instruction-template.md にすでにある。WRC-15 で止まったのはチャット側の指示文がそれより厳しく書いたため）

## 手順

1. 足す前に、2つの文書の今の該当の節を読み、同じ趣旨の行がすでに無いかを確かめる（あれば足さずにログに書く）。容量の検査（`check-assets.yml` と同じもの）を手元で回し、2つの文書の今のサイズと警告域・上限との差をログに書く
2. 規則と理由の一句だけを1行ずつ足す（事例は書かない。必要なら CHAT-1019-WRC-15・16 を参照する括弧書きだけ）。文の案:
   - new-page-checklist.md: 「質問欄のある大会のページは、公開の確かめで本番の質問欄に、トップの例の文の問いと『現タイトルホルダー（その大会の呼び方）は？』を聞き、答えが返ることを確かめる（答えの要旨をログに書く）」
   - page-announcement.md「告知動画の型」: 「字幕が名指すもの（線・選手・数字）は、その字幕が出ている間に画面に映るコマがあることを確かめる。止め方の決定で映らないときは字幕を変えるか、途中で一度見せる」
   足した後にもう一度容量の検査を回し、前後のサイズを書く。上限を超えるなら足さずに止まる
3. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージする

## 止まる条件

- 容量の検査で上限に入る
- 変更が上の3か所のほかに出る
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。足した2行の全文をログに引用する
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRC-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRC-20 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a91c3d04）: https://github.com/retroeater/mj-logs/tree/main/guide/a91c3d04

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
