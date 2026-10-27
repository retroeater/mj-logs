# CHAT-1027-SKP-19

- 着手日時: 2026-10-27
- 対象issue: なし
- ブランチ: work/1027-skp-data
- 着手時HEAD: 954de2fa3

## 指示

【Claude作成】Claude Code 向け指示：「最強戦」シートに入れた Mの乱（2026年10月25日）の結果を saikyo/ と質問欄に反映する（検査 → 差分の見込み → cloudflare で手動実行）
Chat-Ref: CHAT-1027-SKP-19
マージ: 承認済み（チャットで）。対象は手順3の cloudflare での `regenerate-saikyo.yml` と `regenerate-page.yml`（ask_data_saikyo）の手動実行（Actions のコミットが本番反映になる）。作業ブランチの変更はログと docs/decisions だけなので、完了報告のうえ cloudflare へ入れてよい。告知の期間中だが、シートの値の反映で機能は変えない
貼る時機: いつでも（CHAT-1027-SKP-17 のセッションに続けて貼ってよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1027-skp-data の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1027-skp-data を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1027-skp-data origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
平野さんが「最強戦」シートに Mの乱（2026年10月25日）の結果を入れた（2026-10-27 の申告）。毎朝の自動実行を待たずに saikyo/（年度・対局・卓・個人成績・ランキング・対戦成績・新トップ）と質問欄のデータに反映する。続けて告知動画を撮り直す（CHAT-1027-SKP-18、この指示の完了の後に貼る）ので、反映を先に済ませる。

### 決定（2026-10-27、平野さん）
- Mの乱（2026年10月25日）の結果をシートに入れたので、反映する
- 検査で止まる理由が無く、差分が見込みの範囲なら、cloudflare で手動実行して本番へ反映してよい（CHAT-1017-SKG-01・CHAT-1019-SKG-02 と同じ扱い）

### 前提（チャット側。平野さんの決定ではない）
- 再生成の経路: `regenerate-saikyo.yml`（saikyo_pages）を cloudflare で手動実行する。入力の値は CHAT-1017-SKG-01 のログの `## 経過` と今の既定値を確かめて書く。質問欄のデータ（`workers/ask/data-saikyo.json`、ask_data_saikyo）は毎朝の対象ではない（CHAT-1023-SKP-03 のログ）ので、`regenerate-page.yml` を target_page=ask_data_saikyo で手動実行する（`workers/` の変更で Worker `mj-ask` が作り直される）
- 見込みの差分: Mの乱の対局・卓のページと 2026年度のページ、出た選手の個人成績・ランキングの値・対戦成績のデータ、新トップ（埋め込みデータ）、sitemap*.xml、`workers/ask/data-saikyo.json`。選手写真の URL だけの差は生成環境の揺らぎ（handover.md「データの流れ」）
- Mの乱の卓別の ABEMA の動画は、まだ出ていない見込み（ライブ枠のままで正しい）。--check の「2026 の卓の動画URLが放送枠（slots）」の警告は想定どおり
- Mの乱の結果で、告知動画に映る値（桑田憲汰の個人成績・ランキングの上位・対戦成績の桑田憲汰と近藤誠一）が変わるかを、手順2で書く（SKP-18 の撮り直しの材料）

## 手順
1. `regenerate-page.yml` の check（saikyo_pages）で「最強戦」シートを読み、行数と、Mの乱の行（対局日・卓・選手・順位・結果）を全件 `## 経過` に書く。結果（G列）が空の卓・一部だけ空の卓があれば書く。止まる理由・警告をすべて書き、止まる理由が1件でもあれば止まる
2. 作業ブランチで生成し（saikyo_pages と ask_data_saikyo）、差分を種類ごとに数えて書く（(a) Mの乱の対局・卓と 2026年度のページ、(b) 個人成績・ランキング・対戦成績・新トップのデータ、(c) 写真の URL だけの差、(d) sitemap*.xml、(e) `workers/ask/data-saikyo.json`、(f) それ以外）。ランキング6部門の上位10人で変わった行を前と後で書く。告知動画に映る値（上の前提の最後）が変わったかを書く。生成物はコミットせず戻す（docs/notes/branch-operations.md「未コミットの変更を戻すとき」）
3. (f) が無ければ、`regenerate-saikyo.yml` と `regenerate-page.yml`（target_page=ask_data_saikyo）を cloudflare で手動実行する（docs/notes/github-actions.md「ワークフローを手動実行するとき」を先に読む）。run 番号・結論、入ったコミットの SHA と `--stat`、Workers Builds（mj・mj-ask）の結論を書く。本番で Mの乱の対局のページと 2026年度のページを `?v=<未使用の値>` を付けて取得し、結果（通過・敗退）が出ていることを確かめる

## 止まる条件
- 手順1で止まる理由がある、または Mの乱の行が無い・結果が全卓空（平野さんの入力が反映されていない）
- 手順2で (f) がある（内訳を書いて止まる）
- 手動実行が失敗した（今回のデータによる失敗と、無関係な失敗を分けて書く）。このセッションから手動実行できないときは、平野さんが GitHub の Actions の画面から実行できるよう、ワークフロー名・ブランチ・入力の値を「判断が必要なこと」に書いて止まる
- 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`scripts/generate_ask_data_saikyo.py`・`.github/workflows/regenerate-saikyo.yml` の同じ関数・同じ行を変えている（別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1027-SKP-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1027-SKP-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1027-skp-data
- ログ: docs/logs/CHAT-1027-SKP-19.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f4f690aa）: https://github.com/retroeater/mj-logs/tree/main/guide/f4f690aa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f4f690aa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/954de2fa.md
