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

## 経過

- 0: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 着手前の確認: CHAT-1112-MCK-42 のコミットは無し（MCK は既存の系列）。docs と issue 本文のみで 0章ゲートは対象外。#304 は Open。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠け無し
- ブランチ: ローカル・リモートとも work/1112-mck-bing が無かったので `git checkout -b work/1112-mck-bing origin/cloudflare`（起点 6411ba6d5）
- 同じ論点の issue: 「Bing」「Bing 月次」で検索（クローズ済みを含む）。#486（Recommendations への対応）・#568（転送の棚卸し）・#126（closed、登録と IndexNow）などで、Bing の画面を毎月見る論点を扱う別の issue は無かった
- #304 の着手中コメント: 他セッションのものは MCK-38・MCK-41（どちらも完了済み）だけ
- 未マージの work/ ブランチで docs/handover.md を変えているのは origin/work/1101-prp-pub だけで、変えている箇所は 4章「現行サイトに作り込みすぎない」と 6章の表（3章「タスク管理」の行ではない）。止まらない
- 「460」: 「2026-11 実施」のコメント https://github.com/retroeater/mj/issues/304#issuecomment-6551825397 に「引用 460（09-16〜10-29。うち 10-01〜10-29 が 432）」とあり、前提と一致
- 手順1: 直す前の #304 本文の (13) の末尾から「### 運用」まで:

  ```
      - 2026-12 の回は切り替えの当日。解約の確認は 12/2 の【R#298】（OBS）が行うので、月次では Subscriptions の状態を読むだけにし、解約の確認・手続きはしない（この行は 2026-12 の回の後に消してよい）
    - 2026-10-21 に Workers Paid へ切り替えた（この issue のコメント CHAT-1021-WKP-01）

  ### 運用
  ```

  本文に Bing の項目は無く、(13) の次は (14)。直す前の docs/handover.md 3章「タスク管理」の行:

  ```
  - 月次の手作業（AI言及・Core Web Vitals・デバイス比率・robots.txt差分・Cloudflare の設定と記録の照合）は
    #304 に集約し、実施ごとにコメントを残す
  ```

- 手順2: #304 の本文に (14) を足した（文案のまま）。直した後の (14):

  ```
  - [ ] (14) Bing Webmaster Tools を見る（画面。チャット側が平野さんの Chrome で読む）
    - Search Performance（期間 3M）: 合計のクリック・表示・CTR、表示の多いページ上位10、表示が多いのにクリック0のクエリ・ページ
    - AI Performance（期間 3M）: 合計の引用数、grounding query の上位、引用されたページの上位。301 済みの旧 URL（`saikyo/<年度>.html`・`jpml_pros.html?name=` など）が引用・表示に残っているか（新しい URL へ移ったかを月ごとに見る。#568 の転送の削除の判断材料）
    - Recommendations: 重大度ごとの件数と種類。前月から増えた種類だけ #486 へ（短い description は 2026-10-31 の決定〈docs/decisions/seo-bing.md「CHAT-1031-SEO-13」〉で受け入れ済み。件数が大きく増えたときだけ見る）
    - 記録は (7) と同じく、その月の「YYYY-MM 実施」のコメントに値で書く（スクリーンショットは任意）
    - 2026-11-12 に月次の項目にした（2026-11 の回だけの追加は docs/decisions/operations.md「2026-10-12（CHAT-1012-ACF-04）」）
  ```

  ほかに変えたのは本文末尾の Chat-Ref の並びに `Chat-Ref: CHAT-1112-MCK-42` を足し、末尾の生成の注記を付けたことだけ（MCP の issue_write は全文を送るため、読んだ本文を写して足した。直した後の本文を REST で読み直し、項目が (1)〜(14) の14件・(14) が (13) と「### 運用」の間にあることを確かめた）。参照先の docs/decisions/operations.md「2026-10-12（CHAT-1012-ACF-04）」・docs/decisions/seo-bing.md「CHAT-1031-SEO-13」は実在を確かめた
- 手順の逸脱: #304 への着手中コメント（https://github.com/retroeater/mj/issues/304#issuecomment-6839096498 ）を、本文の編集の後に書いた（CLAUDE.md「issueの着手ルール」は先に書く）。コメントにその旨を書いた
- 手順2（handover）: 列挙が #304 の (1)〜(14) とずれていた（Search Console の (7)(8)(10)(12)・Synthetic monitoring・Billing などが無い）ので、案の後者の参照の形にした。直した後の行:

  ```
  - 月次の手作業（Search Console・Cloudflare・Bing Webmaster Tools の画面の確認など。項目は #304 の本文が正）は
    #304 に集約し、実施ごとにコメントを残す
  ```

  docs/handover.md の大きさ（作業ブランチの上、直した後）: 24,511 バイト（約 23.9KB。警告域 26KB の内）。「最終更新」の行は変えていない
- 手順3: 値の表のコメント https://github.com/retroeater/mj/issues/304#issuecomment-6839097845 （申告値で、セッションからは検証できないと明記）。docs/decisions/operations.md の末尾に「2026-11-12（CHAT-1112-MCK-42）」を足した（handover の直しと同じコミット 2ada1bf74）
- セルフレビュー: コード・生成物・ワークフロー・配信設定を変えていない（docs と issue 本文のみ）ので行わない

## 報告

- 状態: 完了
- ブランチ: work/1112-mck-bing
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1112-MCK-42.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1112-mck-bing
- 確認用URL: なし
- マージ: 済（0bb6f814e。cloudflare へ fast-forward）
- issue: #304
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e614708b）: https://github.com/retroeater/mj-logs/tree/main/guide/e614708b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e614708b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e614708b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e614708b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e614708b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e614708b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e614708b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6411ba6d.md
