# CHAT-1030-SEO-09

- 着手日時: 2026-10-30
- 対象issue: なし
- ブランチ: work/1030-seo9
- 着手時HEAD: 5b6cb6cd2

## 指示

【Claude作成】Claude Code 向け指示：SEO 系列（CHAT-1029-SEO-01〜08）の振り返りとレビューの申送りを docs/decisions/operations.md に記録する
Chat-Ref: CHAT-1030-SEO-09
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions/operations.md だけ）
貼る時機: いつでも（新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1030-seo9〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1030-seo9 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1030-seo9 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1030-seo9 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
SEO 系列（CHAT-1029-SEO-01〜08、SEO/AIO の包括レビューとその直し）の振り返りと Fable 5.1 のレビューの申送りを、docs/decisions/operations.md に記録する。この申送りではガイド文書（CLAUDE.md・handover・chat-side-operations・chat-routines・cloud-sessions など）に規則を足さない。

### 決定（2026-10-30、平野さん）
- SEO 系列の振り返りとレビューの申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案）

### 前提（チャット側。平野さんの決定ではない）
- 記録する中身（チャット側の案。この文面を元に、docs/decisions/README.md の書き方に合わせて直してよい）:
  - 足したもの: 本番の全ページの同時取得で 429 が出た件（CHAT-1029-SEO-01 の①-7。docs の「Managed Challenge、30回/分」と応答が違う）を、月次の手作業 #304 の 11月の回の観点に足すよう、担当の MCK 系列のチャットへ伝言した（#304 への追記は MCK の指示で行う）
  - 足さなかったもの（既存の規則で足りる、または機械的に確かめられないため）: (1) マージの可否は推奨の答えと分けて単独で聞く（docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」にある。SEO-06 の手順3の承認を別の問いの答えから推した） (2) 見た目を変える指示は、チャット側が WebFetch に加えて Chrome でもプレビューを開いてからマージを聞く（chat-side-operations.md「読み方」にある。SEO-04 で試さなかった） (3) 日付付きの手作業を頼む返信の中でカレンダーに予定を作る（docs/notes/chat-routines.md「期日とカレンダー」にある） (4) 表示の文字列を変える推奨の前に、その文字列を使う場所を確かめる（chat-side-operations.md「書く前に実物で確かめる」の前提の照合で足りる。SEO-07→08 の往復） (5) 本番の全ページ取得の既定の間隔は、①-7 の原因が分かるまで決めない（docs の 30回/分 と検証で使った 0.5秒間隔〈120回/分〉が食い違うため）
  - 前回の振り返りの約束（GDC の C: ガイド文と既存のコードの食い違いの例が出るかを見る）: この系列では例が出た（CHAT-1029-SEO-01 の L-1〈`scripts/regenerate.py` の `OUTPUT_OVERRIDES` のコメント〉、CHAT-1029-SEO-02 の docs/notes/cloudflare.md・docs/handover.md 2章の canonical の記述）。どちらも `## 報告` の「判断が必要なこと」に挙がり、#560 へ移した。規則は効いている
- 追記先は docs/decisions/operations.md の末尾。追記先の今の末尾の節を読み、同じ日付・系列の節があれば足すのでなく並べる

## 手順
1. docs/decisions/operations.md の末尾の節と docs/decisions/README.md を読み、上の前提の中身を `## 2026-10-30（CHAT-1030-SEO-09）` の節として足す（「作業中の回答: なし」を含め、README.md の形に従う）
2. 追記の後の docs/decisions/operations.md の行数と、上限のある文書なら残り容量をログに書く（上限が無ければ「上限なし」と書く）
3. ログを仕上げて `git push origin work/1030-seo9:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- docs/decisions/operations.md に、この指示と同じ系列（SEO）の申送りの節がもうある（重ねずに止まる）
- 前提に挙げた既存の規則（(1)〜(4) の参照先の節）が見つからない、または中身が前提と食い違う（その項目を報告に書いて止まる）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1030-SEO-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1030-SEO-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-30）

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: なし（識別子 SEO は CHAT-1029-SEO-01〜08 と同じ系列の続き）。CLAUDE.md は起動時から変わっていない。work/1030-seo9 はローカル・リモートとも無く `git checkout -b work/1030-seo9 origin/cloudflare`（着手時 HEAD 5b6cb6cd2）
- docs/decisions/operations.md（1,247行）に SEO の系列の申送りの節は無い（`grep -n SEO` が0件。末尾の節は「2026-10-29（CHAT-1029-LGN-08）」）

### 前提の (1)〜(4) の参照先の照合

| 項目 | 参照先 | 照合 |
|---|---|---|
| (1) マージの可否は推奨の答えと分けて単独で聞く | docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」（92行〜） | 一致: 「**マージの可否は、ほかの問いと分けて単独で聞く。別の問いの答え（「進む」など）から承認を推さない**」 |
| (2) 見た目を変える指示は、チャット側が WebFetch に加えて Chrome でもプレビューを開いてからマージを聞く | chat-side-operations.md「読み方」（7行〜） | **食い違い**（下） |
| (3) 日付付きの手作業を頼む返信の中でカレンダーに予定を作る | docs/notes/chat-routines.md「期日とカレンダー」（113行〜） | 一致: 「**issue に期日を書かせたら・平野さんに日付つきの手作業を頼むときは、同時に（その返信の中で）カレンダー「ryoei.pro」に予定を作る**」 |
| (4) 表示の文字列を変える推奨の前に、その文字列を使う場所を確かめる | chat-side-operations.md「書く前に実物で確かめる」（82行〜） | 一致（前提の照合で足りる、の読みと合う）: 「**平野さんに判断を求める前提（「A を直せば B も不要になる」等）…は、先に Claude Code に照合させるか「未確認」と明記する**」・「事実は実物で確かめたものだけを断定する」 |

(2) の食い違い: 「読み方」の表の「本番の見え方」の行は「チャット側から本番を読む手段は WebFetch と Chrome（`curl` は通らない）。**「届かない・読めない」と書く前に両方を試す**」で、**本番（ryoei.pro）を読む手段の話**。見た目を変える指示で、マージを聞く前にプレビューを Chrome でも開く、という規則はこの節に無い。プレビューについては同じ文書の「確認用 URL」（41行〜。チャット側がブランチ名の別名 URL を `?v=<HEAD の SHA>` 付きで開き、ログの「確認用URL」の印を確かめる。Chrome の指定は無い）と、「平野さんの判断とマージの許可」の「プレビューを見て決める変更は「判断待ちで止まる」。「承認済み」はプレビューの確認が済んだことをチャットで確かめてから書く」がある。どれも「WebFetch に加えて Chrome でも開く」とは書いていない

→ 止まる条件「前提に挙げた既存の規則（(1)〜(4) の参照先の節）が見つからない、または中身が前提と食い違う」に当たるので、docs/decisions/operations.md には追記せずに止めた。マージもしない（CLAUDE.md「ブランチ運用」の「止まる条件に当たったときはマージせずに報告する」）

## 報告

- 状態: 判断待ち
- ブランチ: work/1030-seo9
- ログ: https://github.com/retroeater/mj/blob/work/1030-seo9/docs/logs/CHAT-1030-SEO-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1030-seo9
- 確認用URL: なし
- マージ: 未（止まる条件に当たったため。変えたのはこのログだけ）
- issue: なし
- 判断が必要なこと:
  - 前提の (2)「見た目を変える指示は、チャット側が WebFetch に加えて Chrome でもプレビューを開いてからマージを聞く（chat-side-operations.md「読み方」にある）」が、参照先と食い違う。「読み方」にあるのは「本番を読む手段は WebFetch と Chrome。届かない・読めないと書く前に両方を試す」（本番の話）で、プレビューは「確認用 URL」の節（別名 URL を開いて印を確かめる、Chrome の指定なし）と「平野さんの判断とマージの許可」（プレビューの確認が済んだことを確かめてから「承認済み」）にある。(2) を「足さなかったもの（既存の規則で足りる）」に入れたまま記録するか、(2) の言い方を参照先に合わせて直すか、(2) を「足したもの」の候補に移すか
  - (1)・(3)・(4) は参照先と一致した（経過の表）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d0635eab）: https://github.com/retroeater/mj-logs/tree/main/guide/d0635eab

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b6cb6cd.md
