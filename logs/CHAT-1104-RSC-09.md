# CHAT-1104-RSC-09

- 着手日時: 2026-11-04
- 対象issue: #408・#630
- ブランチ: work/1104-rsc
- 着手時HEAD: 71efe5a3e

## 指示

【Claude作成】Claude Code 向け指示：タイトル戦の対局日（#408）の API の調べものを区切って記録し、最強戦の成績記事からの転記案（D）の設計案を出す Chat-Ref: CHAT-1104-RSC-09 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1104-RSC-08 と同じセッションでよい） 作業ブランチ: クラウドセッションで実行する。work/1104-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-RSC-08 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-RSC-09` を足す。

目的
タイトル戦の対局日（C、#408）の API の調べものをここで区切って記録する。あわせて、最後の種別の最強戦の転記案（D）の設計案を、記事の実物を読んで出す（コード・ワークフロー・シートは変えない）。
決定（2026-11-04、平野さん。RSC-08 の判断待ち 1〜4 への回答）

* 1: 照合済みのうち観戦記の見出しの日付の12期（候補タブの備考に「※」）は、平野さんが出典を開いて本文の対局日を確かめる。プロンプトの直し・調べ直しはしない
* 2・4: 見つからなかった 34期と、2009年以前の期は、外の資料待ちに戻す（2026-10-12 の #408 のコメントの「急がない」）。C の API の調べものはここで区切る
* 3: 未照合の 33期は、平野さんが候補タブで出典を開いて「採用」「却下」を書く。照合（大会名の別名・「M月D、D日」の形）は足さない
* 次は最強戦の転記案（D）に進む。まず記事の作りを調べて設計案を出す（この指示）

決定（2026-10-29、平野さん。D について、RSC-01 の指示文の「決定」のとおり）

* D は毎朝動かし、新しい成績記事があるときだけ API を呼ぶ（読んだ記事は記録して二度呼ばない）。結果は候補タブに書き、本体（「最強戦」）に入れるのは平野さん

前提（チャット側。平野さんの決定ではない）

* 資料のサイト: 今の公式（mahjong-saikyosen.com、RSS `?feed=rss2` がある〈RSC-02〉）・キンマweb（kinmaweb.jp、カテゴリ「麻雀最強戦」）・ABEMA TIMES（times.abema.tv）。使うのは順位などの事実と出典 URL だけ（2026-10-29 の決定）。公式サイトの利用は #469 の許可の内
* RSC-06 で、キンマweb の予選の結果記事は通過の2人しか書かず、3位・4位は観戦記の後ろのページにしかないことが分かっている。記事の作りによっては、転記案も「通過・敗退だけ」「1位・2位だけ」になる
* 平野さんの「最強戦」シートの転記は、対局がほとんど日曜・記事は月曜で、更新は月〜火が多い（docs/decisions/saikyo.md）。対局は年に15週ほど
* 名前を照合に使うので、`scripts/check_unused_names.py` の `NAME_RESOLVERS` を範囲に含めるかを設計案で書く（RSC-07 の判断待ち3）

手順

1. 前提を確かめる: #408・#630 が Open で、他のセッションの着手中のコメントが無いこと。#408 に、上の C の決定（区切り、平野さんが確かめるもの〈※の12期・未照合の33期〉、外の資料待ちに戻すもの）を記録するコメントを書く（本文・状態・ラベルは変えない）。D 用の issue があるか、クローズ済みを含めて検索し、無ければ「判断が必要なこと」に起票の案を書く（この指示では起票しない）
2. 記事の作りを調べる（Claude API・Actions は呼ばない。理由: 設計が決まる前に費用をかけないため）
   * 3つのサイトで、最近の最強戦の対局（2026年度）の成績記事を1〜2本ずつ読み、載っている項目（対局日・対局名・卓・4人の名前・順位・点数・通過/敗退）と書き方を、例を引用して表にする
   * 新しい記事の見分け方（RSS・一覧のページ・URL の形）と、robots.txt・利用の条件を書く
   * 今の「最強戦」シートの列と、記事から埋められる列・埋められない列の対応を表にする
3. 設計案を書く（ログの `## 経過`）: 起動（毎朝のどこに入れるか）・新しい記事の見分け方と記録・プロンプトと照合（名前・卓・結果の通過/敗退とのずれ）・候補タブの列（今の列で足りるか）・費用の見込み（1記事・月）・止まる条件。API を使わずにプログラムで読める記事の形なら、その案も書く。平野さんの判断が要るものを「判断が必要なこと」に、選択肢・メリット・デメリット・推奨つきで書く

止まる条件

* #408 が Closed、または他のセッションの着手中のコメントがある（その手順だけ外して進める）
* 3つのサイトをどれもセッションから読めない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RSC-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RSC-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1104-rsc/docs/logs/CHAT-1104-RSC-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-rsc
- 確認用URL: なし
- マージ: 未
- issue: #408・#630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8140cebc）: https://github.com/retroeater/mj-logs/tree/main/guide/8140cebc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8140cebc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8140cebc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8140cebc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8140cebc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8140cebc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8140cebc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
