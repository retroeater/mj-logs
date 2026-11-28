# CHAT-1128-LVP-53

- 着手日時: 2026-11-28
- 対象issue: #362
- ブランチ: work/1128-lvp-rev
- 着手時HEAD: 4f43e7bd9

## 指示

【Claude作成】Claude Code 向け指示：/live の告知の前に、live/ の着手以降の他ページの変更・改善との平仄と、機能を援用できるかを包括的にレビューする（調査だけ）
Chat-Ref: CHAT-1128-LVP-53
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は状態と公開ログの URL の2行）。平野さんは、この指示のための作業ブランチ work/1128-lvp-rev の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1128-lvp-rev を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1128-lvp-rev origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
/live（live/）は告知（#362）を残すだけになった。live/ に取りかかってから後に、ほかのページ（最強戦 saikyo/、ログ log/ など）で入った変更・改善と live/ が揃っているか（平仄）、ほかのページの機能を live/ に援用できるかを、告知の前に包括的に洗い出す。**この指示ではコード・生成物・シート・issue を変えない**（読むだけ。#362 への着手中のコメントだけ書く。本文・状態・ラベルは変えない）。直すかどうか・どれを告知の前にやるかは、結果を見て平野さんが決める。

### 決定（2026-11-28、平野さん）
- 告知の前に、live/ の着手以降の「最強戦」「ログ」などの変更・改善との平仄と、機能を援用できるかを包括的にレビューする。観点は UI・デザイン・アクセシビリティ・SEO/AIO・AI コンシェルジュなど

### 前提（チャット側。平野さんの決定ではない）
- live/ の着手: docs/decisions/live.md の最も古い日付は 2026-10-02（チャット側が mj-logs の guide/d1f79d91 で見た）。起点はこの日とするが、#362 の経緯と git の履歴で live/ の最初のコミットを確かめ、違えばその日を使う（要確認）
- 比べる相手の候補（チャット側の控えから。漏れ・誤りがあれば実物に合わせる。要確認）: 最強戦 saikyo/（個人成績・ランキング・AI コンシェルジュ・新トップ）、ログ log/（2026-11-04 公開）、鳳凰戦 houou/、女流桜花 ouka/、WRC jwrc/、タイトル戦 title/（新サイトの基本の形へのレイアウトの直し）、連盟プロ pro/、トップ top/・r/、番組（wayhome/・mtsuku/・study/・exam/）、サイト共通（共通ナビ・共有ボタン・CSP〈`_headers`〉・OGP・sitemap・canonical）。決定は docs/decisions/ の design-renewal.md・site-review.md・seo-bing.md・aio.md・saikyo.md・houou.md・title.md・top.md・security.md・page-release.md など、仕様は docs/notes/ の各ページの文書と design.md
- 観点（平野さんが挙げたもの＋チャット側の案）: (1) UI（部品・並び・検索・絞り込み・空の状態・ナビ・共有ボタン）(2) デザイン（新サイトの基本の形〈面・強調色・文字の大きさ〉、design.md との一致）(3) アクセシビリティ（見出しの階層・ランドマーク・代替テキスト・コントラスト比・キーボード操作・フォーカス・`visually-hidden` の使い方）(4) SEO/AIO（title・h1・description・OGP・構造化データ・canonical・sitemap・Search for Agents など AIO の決定）(5) AI コンシェルジュ（saikyo/・houou/ などの質問欄〈`workers/ask/`〉を live/ に置けるか、置くなら何を答えられるか）(6) 共通の部品・スクリプトの援用（同じ機能を live/ が別に持っていないか）(7) チャット側の案: 性能（ページの大きさ・画像）・セキュリティ（CSP・外部の読み込み）。(7) は (1)〜(6) より軽く見てよい
- 本番（ryoei.pro）を読むのは数ページまで。まとめて要求を送るときは UA に `mj-internal` を含める（CLAUDE.md「判断・作業の原則」）。headless Chrome での確かめは、今ある道具だけを使う（新しい依存〈npm・pip のパッケージ〉は足さない）
- 観点ごとにサブエージェントを分けて調べてよい。指摘はチャット側が読み比べられるように、1件ずつ証跡（ファイルと行、または本番の URL と見えたもの）を付ける

## 手順
1. 起点の日（前提の1つめ）以降に、live/ 以外のページに入った変更・改善を、docs/decisions/ の見出し・git の履歴（ページのディレクトリと共通の部品・CSS・`_headers`）から洗い出し、表（日付・ページ・変更・Chat-Ref か SHA・live/ に関係しうる観点）にしてログの `## 経過` に書く。比べる相手の候補に漏れ・誤りがあれば表に書く
2. 観点 (1)〜(7) ごとに、live/（トップ・ライブ・動画・決勝の段、大会・期・節のページ、個別のページ）と手順1の変更を突き合わせ、指摘を表にする。列は「番号・観点・live/ の今（証跡）・相手のページの今（証跡）・ずれか援用の案か・手間の見込み（小・中・大）・告知への影響（告知で挙げる機能に触れるか）」。揃っている点も、確かめた観点ごとに1行で書く（何を見て問題が無かったか）。指摘は全件を表に書き、「略」にしない
3. 指摘を、チャット側の案として「告知の前にやるとよい」「告知の後でよい」「採らない」に分け、理由を1行ずつ書く（平野さんの決定ではないことを明記する）。平野さんに決めてもらうことを「判断が必要なこと」に挙げる

## 止まる条件
- 他セッションの着手中コメントが #362 にあり、live/ のファイルを変えている途中である（その作業が終わるまで比べられないため。終わっていれば止まらない）
- 起点の日が前提（2026-10-02）と1か月以上ずれる（範囲が大きく変わるため、表を書いて止まる）
- 手順1の変更が80件を超える（観点の手順2に入る前に、表を書いて止まる。範囲を平野さんに決めてもらう）
- 読むだけで確かめられず、変える操作（コミット・シートの書き込み・ワークフローの apply）が要る観点が出た（その観点は「未確認の項目」に回して進め、全部がそうなら止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1128-LVP-53.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f43e7bd）: https://github.com/retroeater/mj-logs/tree/main/guide/4f43e7bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f18a318e.md
