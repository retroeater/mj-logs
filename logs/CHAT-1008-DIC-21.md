# CHAT-1008-DIC-21

- 着手日時: 2026-10-10
- 対象issue: #533
- ブランチ: work/1008-dic
- 着手時HEAD: 8b81b485

## 指示

【Claude作成】Claude Code 向け指示：DIC-20 の続き。重なりは節単位で判断して、申送り A・B を行う Chat-Ref: CHAT-1008-DIC-21 マージ: 生成物を変えない検査の追加とドキュメントだけなので、完了報告のうえ cloudflare へ入れてよい（生成物が変わるときは止まる） 貼る時機: いつでも（CHAT-1008-DIC-20 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-20 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-20 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
DIC-19 は work/1008-hou（`static-generation.md`）、DIC-20 は work/1010-rev-routines（`chat-side-operations.md`）との重なりで止まった。どちらも追記先の節とは別の行で、止まる条件をファイル単位で書いたチャット側の書き方の問題だった。重なりは追記先の節単位で判断することにして、DIC-19 の手順2・3（申送り A・B）を行う。
決定（2026-10-10、平野さん）

* 振り返りの申送り A（辞書シートの検査だけを行う手段）と B（#533 への論点の追加）を行う（DIC-19 で記録済み）

前提（チャット側。平野さんの決定ではない）

* 重なりは追記先の節単位で判断する（DIC-20 の報告の案 (i)）。規則の1行は `docs/notes/chat-side-operations.md` の「平野さんがシート（タブ）を用意した・直したと言ったとき」の項に書く（work/1010-rev-routines が変えるのは「申送り」の項で、別の行。`chat-routines.md` は振り返り等の手順の置き場で、シートの規則の置き場ではないため）
* 使い方の1行は、DIC-19 の報告の案 (iii) のとおり、`docs/notes/static-generation.md` の「メンテナンス用スクリプトの詳細」（または同じ文書の、生成スクリプトの使い方を書く節）に書く。「ページの一覧」は変えない。その節も未マージの work/ ブランチが変えていれば止まる
* そのほかは DIC-19 の前提のとおり（検査の中身は今のものを使い二重に書かない、止まる理由はできれば1回で全部出す、生成物と `regenerate.py` は変えない、`docs/notes/chat-side-operations.md` には規則だけを足す、#533 へのコメントの文面）。呼び方は `python3 scripts/generate_resource_dictionary.py --check` を基本にし、実物に合わせて変えたら報告する

手順

1. 確かめる: CHAT-1008-DIC-20 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-21` を足す。未マージの work/ ブランチが `scripts/generate_resource_dictionary.py`、`docs/notes/static-generation.md` の追記先の節、`docs/notes/chat-side-operations.md` の追記先の項を変えていないか、もう一度確かめる（同じファイルの別の節・項の変更は止まる理由にしない。重なりの内容はログに書く）。#533 が Open であることを確かめる。
2. 作る: DIC-19 の手順2のとおり（検査の手段・テスト・今の「辞書」タブでの検査の結果と語数・2か所の追記〈追記先の今の内容を読んでから。容量は CLAUDE.md の更新ルールのとおり測る〉・全ページの再生成で生成物が変わらないことの確かめ・#533 へのコメント）。
3. 報告する: マージの行のとおり、完了を報告して cloudflare へ入れる。

止まる条件

* CHAT-1008-DIC-20 の状態が「判断待ち」でない
* #533 が Closed
* 未マージの work/ ブランチが `scripts/generate_resource_dictionary.py`、または2つの文書の追記先の節・項そのものを変えている
* 検査の手段を足すと生成物が変わる、または今の「辞書」タブで検査が止まる（止まる行と理由を報告する）
* 追記先が容量の上限を超える
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（検査の手段の呼び方を書く）
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #533
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 57963e3e）: https://github.com/retroeater/mj-logs/tree/main/guide/57963e3e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
