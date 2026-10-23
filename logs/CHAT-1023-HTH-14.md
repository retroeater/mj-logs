# CHAT-1023-HTH-14

- 着手日時: 2026-10-23
- 対象issue: #372・#623（振り返りの対象。この指示で触るのはガイド文）
- ブランチ: work/1023-hth-retro
- 着手時HEAD: 0ee9af3f0

## 指示

【Claude作成】Claude Code 向け指示：HTH 系列（最強戦の対戦成績）の振り返り・レビューの申送り（ガイド文2か所）
Chat-Ref: CHAT-1023-HTH-14
マージ: ドキュメントのみ（docs/new-page-checklist.md・docs/notes/chat-side-operations.md・docs/decisions/operations.md・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1023-HTH-13 は完了済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-hth-retro の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1023-hth-retro を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-hth-retro origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
HTH 系列（CHAT-1022-HTH-01〜CHAT-1023-HTH-13、#372・#623）の振り返りと Fable 5.1 のレビューのうち、手順に落とせる2点をガイド文に残す。

### 決定（2026-10-23、平野さん）
- 申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案）

### 前提（チャット側。平野さんの決定ではない）
- 残す2点（知見 → 選んだ層 → 理由）:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| A: 画面を作る自律開発で、参考にしたい画面（他のサイト）が最初の版を作った後に出て、作り直しの指示が1本増えた（HTH-05。FIFA の対戦成績） | ガイド文（docs/new-page-checklist.md「作る段の確かめ」の grill の最初の問いに「参考にしたい画面・サイト」を足す） | 平野さんがどの画面を思い浮かべているかは、テスト・hook では拾えない。grill の最初の問いはこの節が正で、問いを1つ足すだけで済む |
| C: 直しが小さく、合否を機械的に確かめられる（件数・px・はみ出し0件・テストの成否）ときに、プレビューで止めず「条件を満たせばマージ」を先に承認してもらい、直しとマージを1本にした（HTH-10・HTH-13）。今の規則「プレビューを見て決める変更は判断待ち」と食い違っていた | ガイド文（docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」の「プレビューを見て決める変更は…」の項に例外を足す） | マージの可否はチャットで平野さんに聞く手順で、仕組みでは決められない。例外の条件（機械的に確かめられる合否を止まる条件に書く・平野さんがその条件でよいと単独の問いで答える）を書かないと、規則と食い違う運用が続く |

- 残さないもの（理由）:
  - 色・形の仮置きを文字で聞いて覆った（HTH-06 の金）: docs/notes/design.md「7. チャット側の決め方」に「見た目は文章で往復せず実物で見比べる」が既にある。守らなかった話で、規則は足さない
  - 指示文の `regenerate.py --check` の書き方が古かった（HTH-08〜13）: chat-routines.md は 2026-10-23 の PRV-10 で直っている。チャット側がガイドの版の変化（6a274c07 → c0589404）の後に読み直さなかった話で、既にある規則（chat-side-operations.md「ガイドの版が前に読んだ版と違えば…読み直す」）を守る話。Code はワークフローの `check` で代えていた
  - issue の一覧の取得が全件でないまま進めかけた（93件）: Code の着手前の同じ目的の issue の確かめ（CLAUDE.md「issueの着手ルール」）が既にある
- 追記の文面（案。今の内容に合わせて言い回しを直してよい）:
  - A: grill の最初の問いに「参考にしたい画面・サイトがあるか（あれば URL）」を加える
  - C: 「例外: 直しの合否を機械的に確かめられる（件数・寸法・はみ出しの件数・テストの成否など）ときは、その条件を止まる条件に書き、平野さんがその条件でマージしてよいとほかの問いと分けて答えたら『承認済み（チャットで）』としてよい。条件を1つでも満たさなければマージせずに止める」

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。語の例: 参考の画面、条件つきのマージ、プレビューを見ずにマージ）、あれば止まって報告する。追記先の2か所の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張する（処理を報告に書く）。追記先と矛盾して、どちらが正かの判断が要るときは止まる
2. A・C を追記する。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」のとおり、作業ブランチの上で docs/notes/chat-side-operations.md の容量を測り（追記の前後）、警告域（26KB）に入るなら足す前に止まって報告する
3. docs/decisions/operations.md に、この申送りの節（決定と、残す2点・残さない3点を各1行）を足す

## 止まる条件
- 同じ論点の issue がある
- 追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要る
- docs/notes/chat-side-operations.md が追記の後に警告域（26KB）に入る
- 未マージの work/ ブランチが追記先の同じ節を変えている、または取り込みで衝突する（`docs/decisions/*.md` の末尾の追記どうしの衝突は両方を残して解いてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-HTH-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-HTH-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c0589404）: https://github.com/retroeater/mj-logs/tree/main/guide/c0589404

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c0589404/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c0589404/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c0589404/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c0589404/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c0589404/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c0589404/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
