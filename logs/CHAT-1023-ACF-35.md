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

### 着手前の確認

- 同じ Chat-Ref のコミット: なし。ACF は既存の識別子。実装を含まないため0章ゲートは対象外
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも欠けなし
- 0.（指示欄の末尾と指示文の最後の行の一致）: 一致
- ガイド文に規則を足す指示の「知見 → 選んだ層 → 理由」の表: あり（(a)・(b) の2行）
- 作業ブランチ: `origin/work/1023-acf`・ローカルとも cloudflare の祖先だったため `git merge --ff-only origin/cloudflare`（9623fbd44）。ログ先行 push 済み

### 手順1: 追記先の今の内容と残り容量

- docs/notes/chat-side-operations.md: 22,290 バイト。`check-assets.yml` の純増の警告域（`FLOOR=24576`。24,576 バイトを超えて、かつ純増のときに警告）まで 2,286 バイト。上限の警告域 26,624・上限 28,672 の手前
- (a) の追記先「書く前に実物で確かめる」の項「**平野さんの手作業（画面を見るだけ等）が安価で結論を左右するなら、記録・文書の指示より先に頼む**」は、括弧の無い1行だった。矛盾する記述は無い。近い趣旨の項「仮説を確かめるときは、当たり外れを一発で分ける確認を先に行い…」は別の規則なので、そのまま
- docs/instruction-template.md: 12,352 バイト（警告域 14,336・上限 16,384 の手前）。(b) の追記先はファイル冒頭の注意の箇条（「作業ブランチ」の行の文面〜「続きの指示…」）。禁止の理由・外れたときの候補を扱う記述は無く、矛盾も無い

### 手順2: 足したもの（6ed91516b）

- (a): chat-side-operations.md の上の項の末尾に「（外部のデータ〈X のアイコン・連盟サイトなど〉が『変わっていない』という報告で判断を出す前も同じ。今の正〈プロフィールのページ・API〉を見ていなければ、それを頼むか確かめる手順を先に指示する）」を足した
- (b): instruction-template.md の注意の「数を報告させるときは、名前の一覧も書かせる…」の次に1行を足した: 「**見込みを確かめる手順には、外れたときに次の案を選ぶために測る候補も書く。調べるだけの指示で外部 API の呼び出しなどを禁じるときは、禁じる理由（費用・書き込み・副作用）を書く。書けない禁止は入れない**（受け手は禁止の妥当性を判断できず、理由の無い禁止が一発で分かる確かめを塞ぐ）」。12,352 → 12,766 バイト
- 経緯: docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」の後に「2026-10-23 の申送り（ACF 系列、#352・#625）で足した規則の経緯と、移した記述」を足した（経緯を2行と、移した記述を1行）

### 手順2: 純増を避けるため移したもの

(a) で増える分より多く、chat-side-operations.md の場面限定の記述を移した。

- 「平野さんの判断とマージの許可」の「**告知の期間（…）に、告知に挙げるページ・機能を変える指示では、『マージ:』の行に『告知の期間中の変更を承認（<日付>、平野さん）』と書く。** 書いていなければ Code はマージせずに止まる」を移した
  - 移し先: docs/notes/page-announcement.md「進め方」の決定の「期間」の項。今まで「承認の書き方は docs/notes/chat-side-operations.md『平野さんの判断とマージの許可』」としていた所に、書き方の文面を入れた（Code の側の守り方は前からその節にある）
  - chat-side-operations.md「場面ごとに読む文書」の表に「告知の期間に、告知に挙げるページ・機能を変える → `docs/notes/page-announcement.md`『進め方』」の行を足した
- 同じ節の「**`.claude/` の hook・settings を変える指示は平野さんの手作業を前提に組み、『マージ:』は『判断待ちで止まる』**（Code は試験と文書まで。手順は上の表）」を消した
  - 同じ規則が docs/notes/skills.md「git の hook（mj-git-guard）」に全部ある（「チャット側の指示はこの手作業を前提に組み、『マージ:』は『判断待ちで止まる』にする〈`.claude/` の settings を変える指示も同じ〉」）。skills.md は変えていない
  - 場面の表の既存の行を「`.claude/` の hook・settings を変える（『マージ:』は判断待ちで止まる）」にした
- 結果: chat-side-operations.md 22,290 → 22,166 バイト（-124、純増なし）

### 手順3

- docs/decisions/operations.md に「2026-10-23（CHAT-1023-ACF-35）」の節を足した（足した2点・足さなかったもの〈閉じた issue の規則。Code の state の確認が働いたため〉）

### マージ

- 変わったのは docs/ 配下だけ（docs/decisions/operations.md・docs/instruction-template.md・docs/logs/CHAT-1023-ACF-35.md・docs/notes/chat-side-operations.md・docs/notes/handover-archive-2026.md・docs/notes/page-announcement.md）

## 報告

- 状態: 完了
- ブランチ: work/1023-acf
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1023-ACF-35.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-acf
- 確認用URL: なし
- マージ: 済（work/1023-acf を cloudflare へ fast-forward。docs のみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cc768b5f）: https://github.com/retroeater/mj-logs/tree/main/guide/cc768b5f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc768b5f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc768b5f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc768b5f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc768b5f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc768b5f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc768b5f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
