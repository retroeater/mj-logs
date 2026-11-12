# CHAT-1112-MCK-42

- 着手日時: 2026-11-12
- 対象issue: #304
- ブランチ: work/1112-mck-bing
- 着手時HEAD: 6411ba6d5

## 指示

【Claude作成】Claude Code 向け指示：#304 の毎月の手作業に (14) Bing Webmaster Tools を足し、docs/handover.md の列挙を合わせる
Chat-Ref: CHAT-1112-MCK-42
マージ: ドキュメントのみ（ログ・docs/handover.md・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（2026-12-01 の月次チェックより前に）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1112-mck-bing の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1112-mck-bing を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1112-mck-bing origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Bing Webmaster Tools の確認を、#304 の本文「毎月の手作業」の (14) にする。今は 2026-11 の回だけの追加（docs/decisions/operations.md「2026-10-12（CHAT-1012-ACF-04）」の BNG-01 の行）で、本文の項目にはなっていない。docs/handover.md 3章「タスク管理」の月次の手作業の列挙も合わせて直す。あわせて、2026-11-12 に読んだ値を 12月の回の比較の起点として #304 に残す。

### 決定（2026-11-12、平野さん。Bing レポート確認のチャットで依頼し、MCK のチャットへの伝言で受けた）
- #304 の本文「毎月の手作業」に、Bing Webmaster Tools の確認を1項目として足す。docs/handover.md 3章の「月次の手作業」の列挙も合わせて直す

### 前提（チャット側。平野さんの決定ではない）
- 足す項目の文案（伝言の案を MCK のチャットが整えたもの。実物の (1)〜(13) の書きぶりに合わせて変えてよい。(13) の後、「### 運用」の前に置く）:

```
- [ ] (14) Bing Webmaster Tools を見る（画面。チャット側が平野さんの Chrome で読む）
  - Search Performance（期間 3M）: 合計のクリック・表示・CTR、表示の多いページ上位10、表示が多いのにクリック0のクエリ・ページ
  - AI Performance（期間 3M）: 合計の引用数、grounding query の上位、引用されたページの上位。301 済みの旧 URL（`saikyo/<年度>.html`・`jpml_pros.html?name=` など）が引用・表示に残っているか（新しい URL へ移ったかを月ごとに見る。#568 の転送の削除の判断材料）
  - Recommendations: 重大度ごとの件数と種類。前月から増えた種類だけ #486 へ（短い description は 2026-10-31 の決定〈docs/decisions/seo-bing.md「CHAT-1031-SEO-13」〉で受け入れ済み。件数が大きく増えたときだけ見る）
  - 記録は (7) と同じく、その月の「YYYY-MM 実施」のコメントに値で書く（スクリーンショットは任意）
  - 2026-11-12 に月次の項目にした（2026-11 の回だけの追加は docs/decisions/operations.md「2026-10-12（CHAT-1012-ACF-04）」）
```

- docs/handover.md 3章「タスク管理」の列挙の直し方の案: 「（AI言及・Core Web Vitals・デバイス比率・robots.txt差分・Cloudflare の設定と記録の照合）」に「・Bing Webmaster Tools」を足す。列挙が今の #304 の (1)〜(13) とずれているなら（Search Console・Synthetic monitoring・Billing などが無い）、項目を並べ直さず「（Search Console・Cloudflare・Bing Webmaster Tools の画面の確認など。項目は #304 の本文が正）」のように参照の形にしてよい（同じ内容を2箇所に書かないため。どちらにしたかをログに書く）
- ガイド文の直しの「知見 → 選んだ層 → 理由」:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| Bing Webmaster Tools（特に AI Performance の引用）は Search Console で見えない AI 経由の露出を示す。301 済みの旧 URL に引用が残っている | #304 の本文（月次の手作業）と、handover 3章の列挙（参照） | 画面でしか読めず API・ワークフローで自動化できない（センサーにできない）。月次の手作業はすでに #304 に集約している |

- 2026-11-12 に Bing レポート確認のチャットが平野さんの Chrome で読んだ値（データは 2026-11-08 まで。チャット側の読み取りで、Code からは検証できない。「申告値」として書く）:

| 画面 | 値 |
|---|---|
| Search Performance 3M | クリック 167・表示 4.6K・CTR 3.62% |
| AI Performance 3M | 引用 722（2026-11 の回〈CHAT-1101-MCK-25〉で読んだ 9/16〜10/29 は 460）、引用ページ 31。AI の引用で `saikyo/2025.html` 87・`saikyo/2026.html` 60 など、301 済みの旧 URL に多く付いている |
| Recommendations | High 1（IndexNow の案内）・Moderate 55（短い description 54 ページ、被リンク）・Low 1（alt 無し 1 ページ）。h1 無しの指摘は消えていた |

- 「460」は 2026-11 の回の「2026-11 実施」のコメントにあるはず（要確認。食い違えば値を直さずログに両方を書く）

## 手順
1. 同じ論点の open issue（「Bing」「月次」で検索、クローズ済みも含めて確認）と、#304 の state・本文を確かめる。#304 が Open でない、本文に Bing の項目がすでにある（同じ趣旨なら置き換え・拡張してよく、処理をログに書く）、(13) の次の番号が (14) でない、を確かめ、別の issue が同じ論点を扱っていれば止まる。#304 の本文の (13) の末尾から「### 運用」までと、docs/handover.md 3章「タスク管理」の月次の手作業の行を、ログの `## 経過` に引用する
2. #304 の本文に (14) を足す（ほかの項目は変えない。直した後の (14) をログの `## 経過` に引用する）。docs/handover.md 3章の列挙を前提の案のとおり直す（直した後の行をログに引用する。handover の大きさを作業ブランチの上で測り、ログに書く）
3. #304 にコメントを1本書く: 「(14) を足した」ことと、前提の 2026-11-12 の値の表（12月の回の比較の起点。チャット側が読んだ申告値で、Code からは検証できないと明記）。末尾に Chat-Ref の行。決定節を docs/decisions/operations.md に足す（CLAUDE.md「作業ログ」節）

## 止まる条件
- #304 が Open でない、同じ論点を扱う別の open issue がある、#304 の本文の既存の行が今回の決定と矛盾する
- 他のセッションの着手中コメントが #304 にあり、その作業が本文の「毎月の手作業」を変えている
- 未マージの work/ ブランチが docs/handover.md 3章「タスク管理」の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- docs/handover.md が直した後に警告域（26KB）を超える
- 取り込みで docs/decisions/operations.md の末尾への追記どうしが衝突したら両方を残して解き、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1112-MCK-42.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1112-MCK-42 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 137c8327）: https://github.com/retroeater/mj-logs/tree/main/guide/137c8327

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/137c8327/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6411ba6d.md
