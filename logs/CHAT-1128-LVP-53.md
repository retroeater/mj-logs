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

- 着手前の確認: 同じ Chat-Ref のコミット無し・0章（指示欄の末尾が指示文の最後の行と一致）。雛形の行の欠け無し。#362 は open で、最後の着手中コメントはこのセッションのもの（終わっている）。他セッションが live/ を変えている途中ではない
- 着手中コメント: #362（本文・状態・ラベルは変えていない）

### 起点の日
- live/ の最初のコミットは **2026-09-17**（6dd86df98「放送対局ページ(live/)の生成スクリプトを足す」、同じ日に 49884fa8c で live/ を生成）。docs/decisions/live.md の最も古い節は 2026-10-02（前提のとおり）だが、決定の記録より前に生成は始まっていた
- 前提（2026-10-02）とのずれは15日で、1か月未満なので止まる条件には当たらない。起点は 2026-09-17 とした

### 手順1: 起点以降の live/ 以外の変更（件数）
**止まる条件「手順1の変更が80件を超える」に当たったので、手順2（観点ごとの突き合わせ）には入っていない。** 1件ずつの表は数百行になるので、範囲を決めてもらうための集計を書く。

docs/decisions/ の 2026-09-17 以降の節（live.md を除く。1節に複数の決定を含む）:

| 分野（ファイル） | 節の数 | live/ に関係しうる観点 |
|---|---:|---|
| operations | 346 | 運用（ほぼ関係しない） |
| houou | 181 | (1)(2)(3)(5)(6) 鳳凰戦の新しいページ（新サイトの基本の形の元） |
| automation | 139 | 運用・ワークフロー（ほぼ関係しない） |
| saikyo | 128 | (1)(2)(3)(5)(6) 最強戦 |
| title | 58 | (1)(2)(4)(6) タイトル戦（live/ と同じ大会・期を扱う） |
| pros | 57 | (1)(4)(6) 連盟プロ pro/（live/ の役を読む） |
| seo-bing | 46 | (4) |
| site-review | 42 | (1)〜(4) |
| design-renewal | 38 | (2)(3) |
| cloudflare | 38 | (7) |
| security | 36 | (7) |
| features | 34 | (1)(5)（log/ の決定もここ） |
| wayhome | 33 | (1)(6) 帰り道（live/ の個別ページのヒーローの元） |
| broadcast-calendar | 29 | (1) 公開カレンダー |
| top | 22 | (1)(2)(4) 新トップ |
| mtsuku | 22 | (1)(6) |
| ryoei | 17 | (2) |
| study | 15 | (1)(6) 女流勉強会（メンバー限定の札を共有） |
| page-release | 11 | (4) 公開の手順 |
| exam | 10 | (1)(6) |
| dojo-guest | 7 | 関係しない |
| aio | 4 | (4) AIO |
| publishing | 2 | 関係しない |
| 合計 | 1,315 | |

git の 2026-09-17 以降のコミット（生成物の再生成〈`chore: regenerate`・`via GitHub Actions`〉を除く。ディレクトリと生成スクリプト）:

| ページ・部品 | コミット | 期間 |
|---|---:|---|
| saikyo/ | 90 | 09-17〜11-28 |
| log/ | 35 | 10-29〜11-26 |
| houou/ | 109 | 10-05〜11-26 |
| ouka/ | 16 | 10-06〜11-26 |
| jwrc/ | 4 | 10-17〜11-25 |
| wrcr/ | 3 | 10-19〜11-25 |
| title/ | 91 | 09-17〜11-28 |
| pro/ | 29 | 10-30〜11-27 |
| top/ | 16 | 11-03〜11-27 |
| r/ | 68 | 09-21〜11-07 |
| wayhome/ | 24 | 09-22〜11-24 |
| mtsuku/ | 13 | 10-14〜11-24 |
| study/ | 7 | 10-16〜10-29 |
| exam/ | 6 | 10-19〜11-04 |
| books/ | 12 | 09-21〜10-18 |
| dic/ | 2 | 10-06〜10-16 |
| style.css | 227 | |
| assets/ | 208 | |
| navbar.js | 43 | |
| _headers | 6 | |
| scripts/lib/page.py | 16 | |
| workers/ask/（AI コンシェルジュ） | 118 | |

- 共通の部品（style.css・navbar.js・assets/・_headers・page.py）だけでも、件名の重複を除いて 348 件。決定のうち共通の規則に当たる6分野（design-renewal・site-review・seo-bing・aio・security・page-release）だけでも 177 節
- 比べる相手の候補との違い: 候補に無いページに books/（書籍）・dic/（用語）・wrcr/（WRC-R）がある。log/ の決定は独立のファイルが無く、features.md などに入っている

### 範囲の案（チャット側への材料。決めるのは平野さん）
- 案1（Code のおすすめ）: 履歴は洗わず、**今の文書と今のページ**で突き合わせる。相手は saikyo/・log/・title/・houou/ の今の形と、共通の規則の今の正（docs/notes/design.md・docs/new-page-checklist.md・seo-bing.md・aio.md・security.md の現行の決定）。観点 (1)〜(7) はそのまま。変更の履歴（いつ入ったか）は指摘ごとに必要なときだけ引く。件数が範囲に依らず収まり、告知の前に見る意味（今ずれているか）に合う
- 案2: 履歴で見るが、共通の部品（style.css・navbar.js・_headers・page.py）と共通の規則の6分野に絞る（それでも数百件）
- 案3: 期間を絞る（例: live/ を今の白地の形にした 2026-10-17〈CHAT-1017-LVP-06〉以降、または /live を公開した日以降）

## 報告

- 状態: 判断待ち
- ブランチ: work/1128-lvp-rev
- ログ: https://github.com/retroeater/mj/blob/work/1128-lvp-rev/docs/logs/CHAT-1128-LVP-53.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1128-lvp-rev
- 確認用URL: なし（調査だけ）
- マージ: 未（止まる条件に当たり手順2の前で止めた。ログは作業ブランチにある。マージの指示は「ドキュメントのみ」なので、続きの指示で一緒に入れる）
- issue: #362（着手中のコメントだけ）
- 判断が必要なこと:
  - レビューの範囲: 起点（live/ の最初のコミット 2026-09-17）以降の live/ 以外の変更は、決定の節だけで 1,315（運用を除いても約830）、生成物でないコミットだけで 584 件あり、止まる条件「80件を超える」に当たった。`## 経過` の「範囲の案」から選ぶ（Code のおすすめは案1: 履歴でなく、今の文書と今のページ〈saikyo/・log/・title/・houou/ と共通の規則〉で突き合わせる）
  - 起点の日: 前提の 2026-10-02 ではなく、live/ の最初のコミットの 2026-09-17 にした（ずれは1か月未満）
- 未確認の項目: なし
- エラー: なし

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
