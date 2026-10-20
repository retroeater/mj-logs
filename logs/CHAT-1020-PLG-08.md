# CHAT-1020-PLG-08

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-plg
- 着手時HEAD: e18c2138a

## 指示

【Claude作成】Claude Code 向け指示：hook の層の分け方を文書にし、hookify の最初の規則（sitemap.xml の手編集に注意）を平野さんがコミットできる形で用意する
Chat-Ref: CHAT-1020-PLG-08
マージ: 判断待ちで止まる（`.claude/` の規則ファイルは平野さんが GitHub の画面でコミットし、PR〈Create a merge commit〉でマージする。docs/notes/skills.md「git の hook」）
貼る時機: いつでも（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
公式プラグインの試行（PLG 系列）の続き。hook の層の分け方（止める規則と注意を出す規則の置き場所）を決めたので docs/notes/skills.md と docs/decisions/ に書き、hookify の最初の規則（sitemap.xml を Edit・Write したら注意）を、平野さんが GitHub の画面でそのままコミットできる形でログに用意する。セッションは `.claude/` を変えない。

### 決定（2026-10-20、平野さん）
- hook の層の分け方: **止める**規則はリポジトリの hook（mj-git-guard と同じ Python・テスト付き）かテスト・CI に置く。**注意を出す**規則は hookify に置く（hookify はアカウント側のプラグインで、外すと規則ファイルが残っていても効かなくなるため、止める規則を任せない）
- hookify で最初に試す規則は 1 本: sitemap.xml を Edit・Write で書き換えたら注意を出す（warn。lastmod は `scripts/update_sitemap_lastmod.py --from-git` が導出する、CLAUDE.md「禁止事項」・#265）
- 1〜2 週間使って振り返る（予定はチャット側がカレンダー「ryoei.pro」の 2026-11-04 に作成済み）

### 前提（チャット側。平野さんの決定ではない）
- hookify の規則ファイルは `.claude/hookify.<名前>.local.md`（要確認。規則の書式は skill `hookify:writing-rules` に従う）。`.gitignore` が `*.local.md` を無視していないか確かめる（無視していると GitHub の画面で足しても、後で消えたように見えることがある。要確認）
- `.claude/` の変更はセッションから書けず、分類器が `[Self-Modification]` として拒否する（docs/notes/skills.md「git の hook」）。規則ファイルは作らず、内容をログに置く
- sitemap.xml を正しく書き換える経路はスクリプト（Bash）で、Edit・Write は使わない（要確認）。warn なので誤検知しても作業は止まらない
- 「知見 → 選んだ層 → 理由」の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| hook の層の分け方（止める規則と注意の規則） | ガイド文（docs/notes/skills.md の 1 節） | 規則を置く場所の方針で、機械では確かめられない。CLAUDE.md には足さない（容量） |
| sitemap.xml の lastmod を手で書き換えない | hookify の warn（既存の禁止事項の補助） | 止める規則にはしない試行。振り返りで、止める規則（リポジトリの hook かテスト）へ格上げするかを決める。CLAUDE.md「禁止事項」の行はそのまま残す（warn は強制ではないため） |

## 手順
1. 同じ論点（hook の層の分け方、hookify、sitemap の lastmod の手編集の検出）の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。無ければ skill `hookify:writing-rules` を読み、規則の書式（置き場所・ファイル名・event・条件・action〈warn〉・メッセージ）をログの `## 経過` に要約する。`.gitignore` の該当の有無と、sitemap.xml を書き換える正しい経路（スクリプト名・Bash か Edit か）を確かめて書く。
2. 規則ファイルの全文（ファイル名つき）をログの `## 経過` にコードブロックで置く。メッセージは日本語で、「lastmod は `python3 scripts/update_sitemap_lastmod.py --from-git` が導出する。手で直さない（CLAUDE.md 禁止事項、docs/notes/sitemap-lastmod.md）」の趣旨。可能なら、hookify の判定部分に規則ファイルとサンプルの入力（`{"tool_name":"Edit","tool_input":{"file_path":".../sitemap.xml",...}}` と、別のファイルへの Edit）を一時ディレクトリで渡し、warn が出る・出ないを確かめて書く（`.claude/` には置かない。確かめ方が無ければ「未確認」として書き、止まらない）。
3. docs/notes/skills.md に「hook の層の分け方」の節を足す（決定の 1 つ目の箇条の内容、hookify の規則の置き場所と一覧〈この 1 本〉、規則を足す・格上げするときの判断の目安）。追記先の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張する。docs/decisions/ に決定を追記する（CLAUDE.md「作業ログ」節のとおり）。ログの `## 報告` の「判断が必要なこと」に、**平野さんの手作業の手順**を書く: (a) 作業ブランチ work/<識別子> を開くリンク (b) GitHub の画面で「Add file → Create new file」、ファイル名、手順 2 の全文を貼る、「Commit directly to the work/<識別子> branch」を選ぶ（「Create a new branch」や cloudflare を選ばない） (c) PR を作り「Create a merge commit」でマージする。各リンクは直接開ける URL にする。

## 止まる条件
- 手順 1 で同じ論点の issue があるとき
- 手順 1 で hookify の規則の書式が前提と大きく違い（置き場所が `.claude/` でない、warn が無い等）、決定のとおりに作れないとき
- 手順 1 で sitemap.xml の正しい経路が Edit・Write を使っていると分かったとき（規則が正しい作業に毎回注意を出すため）
- 手順 3 で docs/notes/skills.md の記述と決定が矛盾し、判断が要るとき（同じ趣旨の記述があるだけなら止まらない）
- `.claude/` を変える必要が出たとき（変えずに止まる）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが docs/notes/skills.md の同じ節を変えている、または取り込みで衝突する。両方の変更が両立する衝突は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない。平野さんの PR でマージする）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-PLG-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-PLG-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/work/1020-plg/docs/logs/CHAT-1020-PLG-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e18c2138）: https://github.com/retroeater/mj-logs/tree/main/guide/e18c2138

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e18c2138/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e18c2138/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e18c2138/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e18c2138/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e18c2138/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e18c2138/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
