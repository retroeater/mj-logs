# CHAT-1104-TOP-14

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top
- 着手時HEAD: eae05129b

## 指示

【Claude作成】Claude Code 向け指示：TOP-11〜13 を取り込み直して cloudflare へマージし、TOP-05 の判断待ちを閉じる（#101）
Chat-Ref: CHAT-1104-TOP-14
マージ: 承認済み（チャットで）。条件: (1) origin/cloudflare を取り込んだ後にテスト（`python3 -m unittest discover -s scripts/tests`）が通る (2) cloudflare との差分（`git diff --name-only origin/cloudflare...HEAD`）が TOP-11・12・13 の変更（TOP-13 のログ `## 経過` の差分の一覧のファイル）とログ・決定・docs だけで、ほかの新しい変更を含まない (3) 取り込んだ後に全ページを作業ブランチで生成し直し（`regenerate-page.yml` の作業ブランチでの手動実行、dry_run なし・all）、`git diff --name-only origin/cloudflare...HEAD` の生成物が houou/・ouka/・jwrc/・wrcr/・saikyo/・top/ の index.html とサイトマップ（`sitemap-*.xml` の lastmod の行だけ）に限られる。1つでも満たさなければマージせずに止まる
貼る時機: いつでも（TOP-13 と同じセッションの続きで可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-top を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・`navbar.js` の白地の対象パスの if の連なりに行を足すもの）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成されたページの衝突は取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1104-TOP-13 のログの `## 報告` の状態が「判断待ち」であることを確かめ、その状態の末尾に ` / 続き: CHAT-1104-TOP-14` を足す。

## 目的

TOP-13 は、マージの条件 (3)（比べる実行で6件が出ること）が比べの仕組み上満たせない書き方だったため止まった。平野さんの判断で、条件 (3) を「生成し直した後の cloudflare との差分の生成物が6件とサイトマップの lastmod だけ」に置き換え、cloudflare を取り込み直してマージする。あわせて、今朝の確かめで済んだ CHAT-1103-TOP-05 の判断待ちを閉じる。

### 決定（2026-11-04、平野さん。TOP 系列のチャットで）

- TOP-13 の条件 (3) は満たしたものとしてよい。取り込み直し → 条件 (1)(2) の確かめ直し → マージ、で進める（マージの可否はほかの問いと分けて聞いた）
- CHAT-1103-TOP-05 の判断待ち（新トップ top/ が毎朝の運行で作り直されるか）は、チャット側の確かめで済んだので閉じる

### 前提（チャット側。平野さんの決定ではない）

- 今朝の確かめ（チャット側）: 2026-11-04 04:00 JST の「毎朝の運行」（run 41699165492）は success。live / regenerate の対象に `top_page` が入り、ログは「top/index.html は変わりません」（02:43 JST の再生成で、すでに 11/4 の内容）。cloudflare の top/index.html の誕生日の欄は 11月4日（水）
- 取り込み後の生成し直しで、シート側のその日の変化による他ページの差分が出たときは、その差分は cloudflare の再生成でも出るもの。条件 (3) の判定では、作業ブランチを cloudflare のコードのまま（取り込んだ直後、生成し直す前）にした比べ（dry_run・all・compare_ref＝origin/cloudflare）でも出るファイルは除いてよい（除いたファイルと理由をログに書く）
- 本番での確かめに送る問いは1日の上限に数える。送るのは2問まで（UA に `mj-internal`）
- 使われなくなった質問欄の部品（`ASK_TEMPLATE`・`ASK_HEAD`・`assets/houou_ask.*` など）の片付けはこの指示ではしない（`workers/ask/test/week3.test.mjs` が `houou_ask.js` を読むことを含め、後の指示で扱う）

## 手順

1. CHAT-1103-TOP-05 のログの `## 報告` の状態の末尾に ` / 解消: 2026-11-04 毎朝の運行で top/ の作り直しを確認（CHAT-1104-TOP-14）` を足す（同じログの「未確認の項目」の該当行にも確認済みと書く）。origin/cloudflare を取り込み、全ページを生成し直し、マージの条件 (1)〜(3) を確かめる（確かめた一覧をログに書く）
2. 条件を満たせば `git push origin work/1104-top:cloudflare` でマージする（push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD`。偽なら1に戻って取り込み直す。2回目も追いつけなければ止まる）
3. 本番で確かめる（15分で反映されなければ「未確認の項目」に回して完了にする）: `https://ryoei.pro/houou/?v=<未使用の値>` に新しい帯とアイコンが出てページの中の質問欄が無いこと、PC 幅で開いた入力欄の左右の端が本文の面とそろうこと、アイコンを押して開いた欄で「現鳳凰位は？」を1問送り答えが出ること（`context: "houou"`）、`https://ryoei.pro/wayhome/?v=<…>` に新しい帯が出ること、`https://ryoei.pro/jpml_pros.html?v=<…>`・`https://ryoei.pro/title/?v=<…>`・`https://ryoei.pro/?v=<…>` が変わっていないこと。CHAT-1104-TOP-11・12 のログの「マージ」の行に、このマージのコミットを書き足す。#101 にマージと本番の確かめをコメントし、着手中を解除する

## 止まる条件

- TOP-13 が判断待ちでない、または work/1104-top がすでにマージ済み
- マージの条件のどれかを満たさない（満たさなかった条件と中身を書く）
- 上の扱いで解けない衝突が出た
- 古い表のページ・title/・`https://ryoei.pro/` の生成物や本番の内容が、この変更のせいで変わった（見つけたら直さずに報告する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dfabc859）: https://github.com/retroeater/mj-logs/tree/main/guide/dfabc859

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dfabc859/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dfabc859/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dfabc859/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dfabc859/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dfabc859/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dfabc859/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dfabc859.md
