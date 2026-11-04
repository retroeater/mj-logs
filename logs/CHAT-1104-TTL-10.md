# CHAT-1104-TTL-10

- 着手日時: 2026-11-04
- 対象issue: #107・#296（コメントのみ）
- ブランチ: work/1104-ttl
- 着手時HEAD: 341706455

## 指示

【Claude作成】Claude Code 向け指示：TTL 系列（title/ のレイアウトのそろえ）の振り返りと Fable 5.1 のレビューの申送り
Chat-Ref: CHAT-1104-TTL-10
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1104-TTL-08 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなく、TTL-08 のログだけの未マージのブランチなら続けて使ってよい
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1104-TTL-09 は送る前に差し替えたため欠番。
TTL 系列（2026-11-04、CHAT-1104-TTL-01〜08）の振り返りと Fable 5.1 のレビューのうち、機械的に確かめられる手順・事実に落とせるものだけを文書と issue に残す（docs/notes/chat-routines.md「申送り」）。変更は docs と issue のコメント・起票だけ（`docs/decisions/` を含む）。

### 決定（2026-11-04、平野さん）
- 申送りを行う（中身は下の前提のチャット側の案）

### 前提（チャット側。平野さんの決定ではない）

振り返りの要点（docs/decisions/operations.md に1節として書く。形は直近の CHAT-1104-PIL-03・CHAT-1103-PLG-04 の節に合わせる）:
- 指示8本のうち、TTL-01 が着手前に止まった（未マージの `work/1101-prp-pub` との重なり。止まる条件は設計どおり働いた）。TTL-03 は TTL-02 のプレビューのビルドの失敗で出し直しの1本だった。TTL-08 は写真が空のままで足した1本
- TTL-03 の前提「ログの push でプレビューのビルドが付く」は外れた（非本番の watch paths が `docs/**` を除外している。TTL-03 のログ）。CHAT-1104-PIL-03 の約束 (b)「止まる条件に使う仕組みを書く前に確かめる」を守れなかった
- 公開の後に3点が覆った（大会ページの期の範囲・「成績」の節〈TTL-06〉、大会ページ・期ページの検索欄〈TTL-07〉）。原因は、チャット側が TTL-01 の前提で写し元（最強戦・houou/）に無い部品（下のページの検索欄）を足し、#618 の「成績」の節を写し元にあるか問わずに運んだこと。加えて、仮置き15件を1つの問いにまとめて決定にした（preferences の一問一答の規則、docs/notes/design.md「7. チャット側の決め方」の「規則から決めた値も見本を見せる」に反した）
- 期ページで › の無い行の順位が右端に寄る件は、Code の確かめもチャット側も見落とした（行の状態〈› の有り無し〉ごとに位置を比べる観点が無かった）。TTL-07 で全期ページを測って 0px にした
- チャット側のプレビューの確かめは PC 幅・暗い配色だけだった（ブラウザのリサイズが効いておらず、それに気づかなかった）。見ていない幅・配色をマージを伺う問いに書かなかった
- 「松本ちづる」の SNS ID を足した後の再生成（TTL-07）で写真が空のままだった。写真は SNS ブックの更新（`update-sns-book.yml`）が X API で取るため、ID を足した日の反映には更新の手動実行が要る。チャット側は仕組み（docs/notes/sns-book.md）を読む前に平野さんに手作業の確かめを頼んだ
- 前回の約束: CHAT-1104-PIL-03 の (b) は守れなかった（上の TTL-03）。PIL-03 の (a)、CHAT-1103-PLG-04 の (a)・(b) は当たる場面が無い（新しいセッションは全指示で「任意」）
- 約束（次の振り返りで確かめる）: (a) 自律開発の最初の指示に、未マージのブランチとの重なりが見つかったときの既定の進め方（docs/instruction-template.md の「重なる対象だけ外して残りを進めてよい」）を書いたか (b) 見た目の確かめで、チャット側が見ていない幅・配色をマージを伺う問いに書いたか (c) 画面に出る物が増える・減る仮置きを、見本と一緒に1件ずつ伺ったか

知見 → 選んだ層 → 理由:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 非本番のプレビューは docs だけの push では起動しない | ガイド文（docs/notes/cloudflare.md の work/ ブランチのプレビューの節に1行。既にあれば足さない） | 設定の事実で、テスト・hook では確かめられない。止まる条件を書くときにチャット側・Code が読む場所 |
| SNS の ID を足した日の写真の反映には `update-sns-book.yml` の `update` の手動実行が要る（その後の再生成は自動） | ガイド文（docs/notes/sns-book.md の「手動実行」の節に1行） | 手順の事実。生成の警告にする案は、ID があり画像が空の行は毎日の更新の前に必ず出るため、警告が常に出て意味を持たない |
| 一覧の行で状態（リンク・› の有り無し、写真の有り無し）が混ざるときは、状態ごとに右端などの位置を測って比べる | 推論的センサー（docs/notes/design.md の「部品の状態ごとの高さ」の行に、位置の比べも含める形で拡張） | Code が見た目を変える前に読む表で、確かめの手順として受け手が実行できる。全ページの測りは TTL-07 のスクリプトのやり方が使える |

残りの後始末（issue）:
- `scripts/promo_video/title/capture.mjs` が、もう無い `.mj-filterbar`・`#title_select` を見ている（TTL-02・04 のログ）。title/ の告知動画を撮り直すときに当たる。同じ論点の issue（クローズ済みを含む）を検索し、無ければ起票する（題の案「title/ の告知動画の撮影スクリプトが今のページの部品を見ていない」、ラベルは付けずに報告に書く）
- #107・#296 の「最強戦・title/ の Material 寄りの見た目を面の形に寄せる」（docs/decisions/design-renewal.md の 2026-10-12・14 の節）のうち title/ の分は TTL-05〜07 で済んだ。2つの issue の今の状態と本文・コメントを読み、残りとして書かれていれば「title/ の分は CHAT-1104-TTL-05〜07 で済んだ」をコメントで足す（本文・状態・ラベルは変えない）
- 「TTL を先にマージし、`work/1101-prp-pub` の側で重なりを解く」の決定が docs/decisions/title.md（TTL-04 の節）と docs/decisions/pros.md（CHAT-1104-PRP-10）の両方にあるかを確かめ、両方にあれば title.md の行を「→ docs/decisions/pros.md の CHAT-1104-PRP-10 が正」と参照に直す（docs/decisions/README.md の書き方に従う。食い違っていれば直さず報告）

## 手順
1. 追記先（docs/decisions/operations.md の末尾、docs/notes/cloudflare.md・sns-book.md・design.md の該当の節）の今の内容と、容量制限のある文書なら上限・警告域との差を測る。同じ趣旨の記述があれば足さずに置き換え・拡張する（処理を報告に書く）。追記先と矛盾していて、どちらが正か判断が要るときは止まる
2. 上の前提の振り返りの節と、表の3件を書く（規則と理由の一句だけ。事例は書かない）
3. 残りの後始末の3件を行う

## 止まる条件
- 追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要る
- 起票の前の検索で、同じ論点の issue（クローズ済みを含む）が見つかった（起票せず報告）
- 変更が docs・`docs/decisions/` と issue 以外に及ぶ

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（追記先ごとの容量の前後、起票した issue の番号、コメントを足した issue を含める）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep 'CHAT-1104-TTL-10'` は0件（TTL-09 も0件で欠番どおり）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし。ガイド文に規則を足す指示で、「知見 → 選んだ層 → 理由」の表がある
- 作業ブランチ: `origin/work/1104-ttl` はマージ済み、ローカルも cloudflare の祖先だったので `git merge --ff-only origin/cloudflare` で進めた。手順0: 指示欄の末尾の行は指示文の最後の行と一致。ログを先に push（bab90e126）
- 手順1（追記先と容量）: 4つとも容量の上限・警告域の対象（CLAUDE.md・handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）ではない。前後のバイト数:
  - docs/decisions/operations.md 196,092 → 199,559
  - docs/notes/cloudflare.md 87,803 → 88,117
  - docs/notes/sns-book.md 14,095 → 14,389
  - docs/notes/design.md 34,188 → 34,414
  - 同じ趣旨の記述: cloudflare.md には「`docs/**` のみのコミットには Workers Builds の check-run が付かない」「判定は push 全体のファイル」（本番・非本番の両方の話として、ビルドのチェックの節）が既にあった。work/ ブランチのプレビューの節には無かったので、規則の1行と既存の項目への参照だけを足した（事実を二重に書かない）。sns-book.md・design.md には同じ趣旨の記述が無く、sns-book.md は「手動実行」の節に1行、design.md は「部品の状態ごとの高さ」の行を拡張（状態の混ざる一覧の行の位置の比べ、適用範囲に「状態の混ざる一覧の行」）。矛盾は無かった
- 手順2: docs/decisions/operations.md の末尾に「2026-11-04（CHAT-1104-TTL-10）」の節（CHAT-1104-PIL-03 と同じ形）。表の3件は上のとおり（979d2cf02）
- 手順3（後始末）:
  - 告知動画の撮影スクリプト: `gh api search/issues` で「capture.mjs」「告知動画」「promo_video」「撮影スクリプト」「mj-filterbar title_select」をクローズ済みを含めて検索。title/ の capture.mjs を扱う issue は無かった（#637 は saikyo/ の capture.mjs、#540 は houou_race の capture.mjs の言及）。#667「title/ の告知動画の撮影スクリプトが今のページの部品を見ていない」を起票（ラベルなし）
  - #107（closed）は本文に「残り3件…は親の #296 にコメントで書いた」とあるだけで、残りは #296 の 2026-10-14 のコメントの「2. 最強戦（saikyo/）・title/ の Material 寄りの見た目…を面の形に寄せる」。#296（open）に「title/ の分は CHAT-1104-TTL-05〜07 で済んだ」をコメントした。#107 にはコメントしていない（クローズ済みで、残りを #296 へ移したと書いてあるため）。本文・状態・ラベルは変えていない
  - 決定の重複: docs/decisions/title.md（TTL-04 の節）には「TTL を先にマージし、`work/1101-prp-pub` の側で重なりを解く（CHAT-1104-PRP-10 で記録される決定）」がある。cloudflare の docs/decisions/pros.md には CHAT-1104-PRP-10 の節が無く、未マージの `work/1101-prp-pub` の pros.md（412行目〜）にだけある（中身は食い違っていない）。「両方にあれば参照に直す」に当たらないので title.md は直していない

## 報告

- 状態: 完了
- ブランチ: work/1104-ttl（docs のみ）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-TTL-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: なし
- マージ: 済（docs のみ）
- issue: #667（起票）、#296（コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4faa95fb）: https://github.com/retroeater/mj-logs/tree/main/guide/4faa95fb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4faa95fb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4faa95fb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4faa95fb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4faa95fb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4faa95fb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4faa95fb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
