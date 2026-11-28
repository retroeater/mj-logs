# CHAT-1128-CFX-05

- 着手日時: 2026-11-28
- 対象issue: #304
- ブランチ: work/1128-cfx
- 着手時HEAD: f18a318eb

## 指示

【Claude作成】Claude Code 向け指示：Zero Trust Free（Teams Free Base）を ryoei.net のために有効にしたことを docs に記録し、#304 の (9) に確認の行を足す
Chat-Ref: CHAT-1128-CFX-05
マージ: ドキュメントのみの変更（docs/decisions/・docs/notes/・docs/logs/）なので、完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は状態と公開ログの URL の2行）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1128-cfx を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1128-cfx origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Cloudflare の Billing → Subscriptions に Zero Trust の契約が載り、2026-11-19 の記録（Available の7製品はどれも有効にしない）と合わなくなったので、記録を今の状態に直す。あわせて月次チェック（#304）に、ryoei.pro とプレビューに Access が掛かっていないかを見る行を足す。コードは変えない。

### 決定（2026-11-28、平野さん）
- Zero Trust（Billing → Subscriptions の「Teams Free Base」）は、平野さんが意図して有効にした。使うのは ryoei.net だけ（OBS のチャットで平野さんが答えた。OBS のチャットから CFX のチャットへの伝言による）
- 月次チェック（#304）に、Access のアプリケーションが ryoei.pro・`*.workers.dev` を対象にしていないかを見る行を足す（CFX のチャットで「足す」）

### 前提（チャット側。平野さんの決定ではない）
- 画面の事実（2026-11-28 の平野さんのスクリーンショットを OBS のチャットが見た。申告値。CFX のチャットは画面を見ていない）: Billing → Subscriptions に Zero Trust の「Teams Free Base」（Renews on Dec 9, 2026）が載っている。2026-11-02 の画面には無かった。Available の一覧から Zero Trust Free は消えている。有効にした日は分からない（2026-11-19 の CFX の時点では Available にあった）。Observability の行はまだ無い（解約は 12/2 の予定で OBS のチャットが扱う。この指示では触らない）
- 2026-11-19 の決定（docs/decisions/cloudflare.md の CHAT-1119-CFX-01 の節「Available の7製品は、今はどれも有効にしない」）のうち Zero Trust Free の分を、上の決定で置き換える。ほかの6製品はそのまま
- Zero Trust はアカウント単位の契約で、同じアカウントに ryoei.pro・ryoei.net・selflessness.net のゾーンがある。「使うのは ryoei.net だけ」から、ryoei.pro と mj のプレビュー（`*.workers.dev`）には Access のアプリケーションを掛けない、と書く（チャット側の読み。2026-11-19 に見送った理由〈クラウドセッションの curl とチャット側の WebFetch がプレビューを開けなくなり、確認の手順が止まる〉は今も当たる。ryoei.pro に Access を掛けるのは SDP の管理画面や非公開のページを作るときに改めて決める）
- ryoei.net で Zero Trust を何に使うか・その設定は、このリポジトリには書かない（2026-11-02・11-15 の決定: ryoei.net・selflessness.net の設定は claude.ai のプロジェクト「セルフレスネス合同会社の経営」の文書「ドメイン・Cloudflare設定台帳」が正）。docs には台帳を指す一文だけを書く
- #304 は 2026-11-28 に open。本文の (9) は「Cloudflare ダッシュボードの主な設定…を docs/notes/cloudflare.md の記録と照らし合わせる」で、下に Security Insights・監査ログ・Search for Agents の行がある（チャット側が REST で読んだ）。「Zero Trust」を含む issue は検索で0件
- Access のアプリケーションの一覧を見る画面の名前・場所は確かめていない（要確認）。公式の文書で確かめられたら行にその名前を書き、確かめられなければ「Zero Trust のダッシュボードの Access のアプリケーションの一覧」と書く（推測でメニューの経路を書かない）

## 手順
1. 同じ論点の issue（Zero Trust・Access。クローズ済みとコメントの決定まで）を検索し、追記先の今の内容（docs/decisions/cloudflare.md の末尾と CHAT-1119-CFX-01 の節、docs/notes/cloudflare.md「Billing の Available の製品（2026-11-19）」と ryoei.net・selflessness.net に触れる節）を読む
2. 記録する:
   - docs/decisions/cloudflare.md に「## 2026-11-28（CHAT-1128-CFX-05）」の節を足し、上の決定2点と「作業中の回答: なし」を書く。CHAT-1119-CFX-01 の節の該当の行には、置き換えの印（今までの節の書き方に合わせて「→ 置き換え: 2026-11-28（CHAT-1128-CFX-05）。Zero Trust Free の分」）を足す
   - docs/notes/cloudflare.md「Billing の Available の製品」の前置きの文と Zero Trust Free の行を今の状態に直す（契約は有効・ryoei.net のため・ryoei.pro と `*.workers.dev` には Access を掛けない・その理由・台帳を指す一文・画面の事実は申告値）。Zero Trust が今は無いと読める記述がほかにあれば、docs 全体（`docs/logs/`・`docs/gsc/` などの記録を除く。CLAUDE.md・docs/handover.md を含む）を「Zero Trust」で検索して直す
3. #304 の本文の (9) の下に、次の趣旨の行を足す: 「Zero Trust（2026-11 に ryoei.net のために有効。docs/decisions/cloudflare.md の 2026-11-28）の Access のアプリケーションが、ryoei.pro・`*.workers.dev`（mj のプレビュー）を対象にしていないか。対象になっていたら外す（プレビューを curl・WebFetch で開けなくなり確認の手順が止まる）」。本文のほかの行・状態・ラベルは変えない。足した行を `## 経過` に引用し、#304 にコメントで `Chat-Ref: CHAT-1128-CFX-05` と足した行を書く

## 止まる条件
- 追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい。処理を報告に書く）
- 同じ論点の open issue がある（クローズ済みに決定があれば、それと食い違わないように書き、食い違うなら止まる）
- #304 が Closed、または他セッションの着手中コメントがある・(9) を別のセッションが書き換えている最中
- 未マージの work/ ブランチが docs/decisions/cloudflare.md・docs/notes/cloudflare.md の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。両方の変更が両立する衝突〈追記どうし・隣り合う行〉は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1128-CFX-05.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d9066093）: https://github.com/retroeater/mj-logs/tree/main/guide/d9066093

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f18a318e.md
