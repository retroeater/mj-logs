# CHAT-1104-TOP-19

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top4
- 着手時HEAD: ada6990de

## 指示

【Claude作成】Claude Code 向け指示：新トップ top/ に「お気に入りの予定」の面を作る（お気に入りの選手の放送対局・誕生日などを一番上に、#101）
Chat-Ref: CHAT-1104-TOP-19
マージ: 判断待ちで止まる（チャット側がプレビューを見て、仮置きを平野さんと決めてから、マージを分けて聞く）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top4 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。origin/cloudflare を起点に work/1104-top4 を新しく作る（work/1104-top〜top3 はマージ済みで使わない）。リモートにすでにあれば止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

新トップ top/（未公開・noindex）の一番上の差し込み口（`data-top-slot="favorites"`）に、端末の「お気に入り」（pro/ の個別ページの ★、localStorage `mj:favorites`）に入れた選手の今後の予定と誕生日を出す面を作る。見た人が「自分の推しの次の出番」をすぐ分かるようにする。自律開発で、下の仮置きで作り、プレビューで決める。

### 決定（2026-11-03〜04、平野さん。TOP 系列のチャットで）

- 呼び名は「お気に入り」（「フォロー」ではない）。上限なし（2026-11-03）
- 面の並び: ロゴ → お気に入りの予定 → 誕生日 → 新着の番組（2026-11-03〜04。お気に入りの面が一番上）
- 2026-11-04: お気に入りに入れた選手の放送対局の予定・誕生日などを優先して出す面を作る。標題・並び・表示する中身はチャット側の提案で自律開発する（下の仮置き）
- 端末ごとの絞り込みなので、お気に入りの中身を URL のクエリに載せない（pro/ と同じ、2026-11-03）

### 前提（チャット側。平野さんの決定ではない。実物で確かめてから進める）

データ（チャット側が cloudflare ada6990de で見た。要確認）:
- `mj:favorites` は `[{"slug","name"}]`（`assets/pro.js` の `FAV_KEY`。書くのは pro/ の個別ページ）
- `/pro/plans.json` は `{"plans":[{slug,name,date,kind,role,title,href}]}`、全在籍者の今後の予定を日付の近い順（`generate_pro_pages.plans_json()`）。date は日本時間の `YYYY-MM-DD` か `YYYY-MM-DDTHH:MM`。kind は「放送」「最強戦」「道場部」。今は 112 行・84 人・11/04〜11/30（約4週間）。過ぎた行は読む側で隠す（`assets/pro.js` の `hidePastPlans()` と同じ基準）。pro/ の生成は毎朝の運行で作り直される
- 誕生日は `plans.json` に無い。top/ の生成（`lib/birthdays.py` × 名簿、`birthday_rows()`）は今日の前後の数日分だけを焼いている。お気に入りの誕生日のために、top/ の生成で在籍者の `{slug: "MM-DD"}` の小さな JSON（例: `top/birthdays.json`）を書き出す見込み（誕生日は今も誕生日の面と pro/ に出ている公開の情報。年は書かない）。`plans.json`・`generate_pro_pages.py`・`assets/pro.js` は PRP 系列の担当なので TOP は変えない
- pro/（#641）はまだ未公開。本番で ★ を押せるのは pro/ の公開の後。top/ も未公開なので、作って入れておいてよい

仮置き（プレビューで平野さんが決める。ログに一覧で書く）:
- 標題: 「お気に入りの予定」（★ の呼び名に合わせる。ほかの案は「推しの予定」「推し選手の予定」。定数1つで差し替えられるようにする）
- 中身と並び: 1行1件で日付の近い順に混ぜて並べる。行は「日付（今日・明日はその語）＋時刻、選手名、種類の印（放送・最強戦・道場部・誕生日）、題名」。誕生日の行は「〇〇さんの誕生日」。放送は `href` へのリンク、選手名は pro/ の個別ページへのリンク
- 範囲: 予定は `plans.json` にあるもの全部（今日以降）。誕生日は今日から30日以内
- 件数: 最初は5件、「もっと見る」で残りを全部（新着の番組・誕生日の面の「もっと見る」と同じ形と同じ部品）
- 今日の行は目立たせる（地の色を少し変えるか「今日」の印。誕生日の面の今日の扱いに合わせる）
- お気に入りが0人のとき: 面は出し、1行の案内「選手のページの ☆ で『お気に入り』に入れると、ここに放送対局や誕生日が出ます」と連盟プロ（`/pro/`）へのリンク
- お気に入りはいるが、範囲内に予定も誕生日も無いとき: 「お気に入りの選手の予定はまだありません」と、お気に入りの選手名のチップ（個別ページへのリンク）
- localStorage が使えない・`plans.json` が読めないとき: 面を出さない（ページの他の面は変えない）
- 面の見た目は誕生日・新着の番組の面と同じ部品（見出し・行・「もっと見る」）を使う。新しい外部ドメインは使わない
- 選手名は `mj:favorites` の name ではなく、`plans.json`・誕生日の JSON 側の現在名を出す（改名に追従）。どちらにも無い slug は名前のチップだけ

## 手順

1. 実物で上のデータの形を確かめ、`## 経過` に書く。未マージの work/ ブランチで、変えるファイル（`scripts/generate_top_page.py`・`assets/top.js`・`assets/top.css`・`style.css`・`scripts/tests/test_top_page.py`）の同じ行・関数を変えているものを一覧にする。#101 に着手中コメント
2. 面を作る（生成で差し込み口に面の枠と誕生日の JSON、JS で中身を描く）。テスト（範囲・並び・今日と明日の語・0人・予定なし・過ぎた行を隠す・30日の境目・件数と「もっと見る」・改名・リンクがルート相対か `plans.json` の href だけ）と `docs/notes/top-page.md`・`docs/decisions/top.md`（上の決定）を直す。`.assetsignore` と公開対象（新しい JSON）を確かめる
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（dry_run・all・compare_ref＝origin/cloudflare。差は top/ だけ）・画面の確かめ（360px・400px・PC 幅、明暗。お気に入り0人・3人〈予定のある人とない人、誕生日が近い人を混ぜる〉・予定なしの3つの状態を、localStorage に値を入れて撮る）。ブランチ名の別名 URL でプレビューできる状態にし、チャット側がプレビューで localStorage に入れる値の例（実在の slug を3人分）をログに書いて止まる

## 止まる条件

- `mj:favorites`・`plans.json` の形が上と違う（違いを書いて止まる）
- PRP 系列の担当のファイル（`generate_pro_pages.py`・`assets/pro.js`・pro/ 配下）を変える必要が出た
- top/ 以外の生成物に差分が出た
- 新しい依存・外部ドメインが要る
- #101 に他セッションの着手中コメントがある

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ada6990d）: https://github.com/retroeater/mj-logs/tree/main/guide/ada6990d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
