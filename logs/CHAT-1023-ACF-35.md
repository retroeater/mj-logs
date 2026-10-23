# CHAT-1023-ACF-35

- 着手日時: 2026-10-23
- 対象issue: なし
- ブランチ: work/1023-acf
- 着手時HEAD: 9623fbd44

## 指示

【Claude作成】Claude Code 向け指示：ACF 系列（CHAT-1023-ACF-29〜34）の振り返り・レビューの申送り（chat-side-operations.md と instruction-template.md に規則を2つ足す）
Chat-Ref: CHAT-1023-ACF-35
マージ: ドキュメントのみ（docs/notes・docs/instruction-template.md・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1023-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
ACF 系列（CHAT-1023-ACF-29〜34。「x」の表〈#625〉と、瀬戸麻衣さんの X 画像〈#352〉）の振り返りと、Fable 5.1 のレビューから、規則を2つ書き残す。

### 決定（2026-10-23、平野さん）
- 申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案）

### 前提（チャット側。平野さんの決定ではない）
振り返りで分かったことは次の3つ。
- ACF-32 で、Code は古い画像の last-modified を見て「画像は変わっていない」と結論した。実際には、瀬戸さんは前日にアイコンを変えていた
  - チャット側はその結論を見抜けず、事実の形で平野さんに伝えた
  - ACF-32 の指示は X API の呼び出しを禁じていた。禁じた理由は書いていなかった。瀬戸さん1人を引けば一発で分かった
  - 解決したのは、平野さんが新しい画像の URL を1つ貼ったこと
- ACF-33 では、見込み（クエリでキャッシュを通らない）が外れて止まった。次の案を選ぶ材料（大きさごとの HEAD）は、Code が自分の判断で測った。指示には書いていなかった
- ACF-29 の閉じた issue（#387）は、Code の着手前の state の確認（2026-10-23 の WKP-04 の申送り）で止まった。この仕組みは働いた。チャット側が既存の規則を守らなかっただけなので、規則は足さない

知見ごとの書き残し方は次の表のとおり。どちらも計算的センサー（テスト・hook・ワークフロー）にも、推論的センサー（Code の着手前の確認）にも落とせない。チャット側が指示文を書くときの判断だからで、ガイド文にする。

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| (a) 外部のデータ（X のアイコン・連盟サイトなど）が「変わっていない」という結論を受けて判断を出す前に、その値の今の正（プロフィールのページ・API）を見る。見ていなければ、それを平野さんに頼むか、確かめる手順を先に指示する | ガイド文（chat-side-operations.md「書く前に実物で確かめる」の既存の行「平野さんの手作業（画面を見るだけ等）が安価で結論を左右するなら、記録・文書の指示より先に頼む」に、例として1句足す） | チャット側が報告を受けるときの判断で、機械では確かめられない。新しい行にせず既存の行に寄せる（文書を増やさないため。Fable のレビューの意見） |
| (b) 見込みを確かめる手順では、外れたときに次の案を選ぶために測る候補も書く。調べるだけの指示で外部 API の呼び出しなどを禁じるときは、禁じる理由（費用・書き込み・副作用）を書く。書けない禁止は入れない | ガイド文（docs/instruction-template.md の注意に1行） | 指示文の書き方の注意で、受け手の Code は禁止の妥当性を判断できない |

## 手順
1. 追記先の今の内容と残り容量を測り、ログに書く。対象は次の2つ。
   - docs/notes/chat-side-operations.md: バイト数、`check-assets.yml` の純増の警告域（24,576 バイト）との差
   - docs/instruction-template.md: 該当の節
   同じ趣旨の記述がすでにあれば、足さずに既存の記述を拡張してよい（処理を報告に書く）。既存の記述と矛盾して、どちらが正かの判断が要るときだけ止まる。
2. 上の表の (a) を chat-side-operations.md の既存の行に1句で、(b) を instruction-template.md の注意に1行で足す。規則と理由の一句だけを書き、事例（ACF-32・33 の経緯）は書かない。経緯は、このログと docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」に1〜2行で置く。
   chat-side-operations.md が純増したら、同じバイト数以上を、同じ文書の場面限定の記述から、場面ごとの文書（docs/notes/ 配下）へ移す（文書の冒頭の規則のとおり。移したものと移し先をログに書く）。
3. docs/decisions/operations.md に、この申送りの節を足す（直近の「2026-10-23（CHAT-1023-WKP-04）」などと同じ形）。書く項目は次のとおり。
   - 足した2点
   - 足さなかったもの（issue の state の規則。理由: Code の確認が働いた）

## 止まる条件
- 手順1で、(a)・(b) と矛盾する既存の記述があり、どちらが正かの判断が要る
- 手順2で、純増を移しきれない（移せる場面限定の記述が見つからない）
- origin/cloudflare との差分に、`docs/` 以外のファイルが入っている

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-ACF-35.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-ACF-35 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b4b933ba）: https://github.com/retroeater/mj-logs/tree/main/guide/b4b933ba

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
