# CHAT-1104-RSC-10

- 着手日時: 2026-11-04
- 対象issue: （D の issue を起票する）
- ブランチ: work/1104-rsc
- 着手時HEAD: d08074979

## 指示

【Claude作成】Claude Code 向け指示：最強戦の転記案（D）を起票し、キンマweb のテキスト速報をプログラムで読んで候補タブに書く種別を足し、毎朝の run-daily から呼ぶ Chat-Ref: CHAT-1104-RSC-10 マージ: 承認済み（チャットで）。止まる条件を1つでも満たしたらマージせずに止まる 貼る時機: いつでも（CHAT-1104-RSC-09 と同じセッションでよい） 作業ブランチ: クラウドセッションで実行する。work/1104-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-RSC-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-RSC-10` を足す。

目的
CHAT-1104-RSC-09 の設計案と「判断が必要なこと」1〜7 への回答に沿って、最強戦の成績記事からの転記案（D）を実装し、毎朝動かす。
決定（2026-11-04、平野さん。RSC-09 の判断待ち 1〜7 への回答）

* 1: D はプログラムで読む（Claude API を使わない、費用 0）。2026-10-29 の決定「新しい成績記事があるときだけ API を呼ぶ」を「API を使わずにプログラムで読む」に置き換える
* 2: 資料はキンマweb だけ（テキスト速報と結果記事）。ABEMA TIMES は足さない。キンマweb も最強戦の主催者（竹書房）の運営なので、最強戦の公式サイトを資料に使う許可（#469）の内とみなす
* 3: 毎朝の run-daily から research-candidates を `workflow_call` で呼ぶ（2026-10-29 の決定「毎朝動かす」のとおり。research-candidates の「手動実行だけ」の扱いを変える）
* 4: 決勝の卓（シートに行が無い）は、候補の行（4人と順位・結果）を見て平野さんが「最強戦」に4行を足す。「シートに足す行」の形の出力は作らない
* 5: D の issue を、この指示の最初に起票する（題「最強戦: 成績記事から順位・結果の転記案を候補タブに書く（調べもの D）」。ラベル・本文は RSC-09 の案のとおり、実在のラベルに合わせてよい）
* 6: 記事の名前は「別名」で直さない。`scripts/check_unused_names.py` の `NAME_RESOLVERS` は範囲に含めない（旧名で書かれた記事は未照合に倒れる）
* 7: まず通常の対局（予選2卓＋決勝）の形だけを実装する。全日本プロ選手権・アマチュア最強位決定戦・ファイナルなど形の違う対局は「見つからない」「未照合」で書き、実際の速報の形を見てから足す
* この指示のマージと、マージの後の試し（手動実行で直近の 2026年度の記事を読んで R_候補 に書く）を承認する。本体の「最強戦」は変えない

前提（チャット側。平野さんの決定ではない）

* Sheets API の1分あたりの読み取り上限で再生成がページを飛ばす件（#662、別のチャットの ACF 系列で計測中）があるので、毎朝の D は、新しい記事が無い朝はシートを読まずに終える（新着の判定は RSS・一覧のページだけで行い、読んだ記事の記録は候補タブではなく読み取りの少ない形にしてよい。方法は実物に合わせて選び、ログに書く）。新しい記事がある朝だけ「最強戦」と R_候補 を読む
* run-daily の中の置き場所は、ほかの本の失敗に巻き込まれない並行のジョブ（`sync-dojo-calendar` などと同じ形）を案とする。D が失敗しても run-daily が failure になる（#506 の朝の知らせに出る）点は、ほかの本と同じ扱いでよい
* `docs/notes/github-actions.md`「ワークフローの一覧」の表（`research-candidates.yml`・`run-daily.yml` の行）と `scripts/tests/test_workflows_table.py` を同じコミットで直す。新しいジョブにも permissions を明記する
* 同点の卓は、点数だけでは順位が決まらないので「未照合」にする（RSC-09 の未確認の項目）
* 読み取りは robots.txt と RSC-09 で確かめた条件を守り、User-Agent は RSC-05 と同じ形にする

手順

1. 前提を確かめる: 同じ論点の issue を、クローズ済みを含めて検索する（RSC-09 では新しい200件だけを見た。題・本文の語で検索する）。無ければ D の issue を起票し、着手中のコメントを書く。未マージの work/ ブランチと、ほかのチャットの実行中の指示が `.github/workflows/run-daily.yml`・`research-candidates.yml`・調べもののスクリプトを変えていないか確かめる（`git branch -r --no-merged origin/cloudflare` と、各ブランチの差分）
2. 実装する: キンマweb の新着の判定、テキスト速報の「試合結果」の行の読み取り、シートの卓との照合（4人の完全一致、姓だけの照合は 2026-11-04 の決定のとおり）、候補タブへの追記、research-candidates への D と `workflow_call`、run-daily への1ジョブ。テスト（速報の HTML の一部を固定の資料にしてよい）、docs（仕組みの文書・github-actions.md の表・決定）。自己レビュー（docs/notes/self-review.md）の後にマージする
3. マージの後、cloudflare から research-candidates を手動実行し（`kind` D、まず `dry_run`、次に本実行）、直近の 2026年度の対局の記事を読んで R_候補 に書く。読んだ記事・卓ごとの表（卓・候補の順位・確度・出典 URL）を全件ログに書き、D の issue に要約をコメントする。翌朝の run-daily で D が動いたかを確かめる手順を「判断が必要なこと」か「未確認の項目」に書く（この指示の中で翌朝まで待たない）

止まる条件

* 同じ論点の open issue に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチ・ほかのチャットの実行中の指示が `run-daily.yml` の同じジョブ・同じ行を変えている、または取り込みで衝突する（`research-candidates.yml`・調べもののスクリプトも同じ。別の箇所の変更では止まらず、ログに書く）
* 差分が次の範囲を超える: 調べもののスクリプト・テスト、`research-candidates.yml`、`run-daily.yml` への1ジョブ、`docs/notes/github-actions.md` の表と `scripts/tests/test_workflows_table.py`、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
* テストが通らない（今回の変更による失敗は直してよい。無関係な失敗は原因を書いて進めてよい）
* キンマweb が読めない（HTTP 200 以外・robots.txt が禁じている）
* 新しい記事が無い朝にシートを読む作りになってしまう（#662 への配慮が守れない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RSC-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RSC-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1104-rsc/docs/logs/CHAT-1104-RSC-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-rsc
- 確認用URL: なし
- マージ: 未
- issue: 作業中
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 05cad68f）: https://github.com/retroeater/mj-logs/tree/main/guide/05cad68f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/05cad68f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/05cad68f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/05cad68f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/05cad68f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/05cad68f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/05cad68f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
