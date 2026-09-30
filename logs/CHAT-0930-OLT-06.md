# CHAT-0930-OLT-06

- 着手日時: 2026-09-30
- 対象issue: #446（項目8）、#475
- ブランチ: work/0930-olt-06
- 着手時HEAD: b9b7e174

## 指示

【Claude作成】Claude Code 向け指示：vs 行で「漢字・かなに挟まれた s・v 1文字」も区切る（#446 項目8、#475 の「逢川恵夢s二階堂瑠美」ほか6件）。模擬で確かめてマージし、【2】に反映する Chat-Ref: CHAT-0930-OLT-06 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/0930-olt-06 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-olt-06 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-olt-06 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 マージ: 承認済み（チャットで、2026-09-30）。条件: 手順1 の模擬で、生成物の差分が下の6本の対局者の分かれ方（とそれに伴う表示）だけであること。それ以外の差分が出たら判断待ちで止める。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-OLT-04 のログの `## 報告` を読み、完了していなければ止まる。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、`scripts/lib/live_extract.py` とそのテストに触れているものがあれば止まる。

目的
YouTube の概要欄で「vs」の「v」や「s」が抜けた誤記（例: 逢川恵夢s二階堂瑠美）を【2】の規則で2人に分け、#475 の未登録の名前から消す。OLT-04 のログ「直し方の案」の案B。
決定（2026-09-30、平野さん）

* 案B で直す: vs 行の区切りに「漢字・かなに挟まれた `s`・`v` 1文字」を加える（#446 項目8 に足す）。
* 【2】が直った後、【3】手動補正の対局者の補正2本（`6Sem9jKnkVU`、`SGlbTPLSs7Q`）を消す（補正を減らす方針どおり）。
* 生成物の差分が6本の対局者の分かれ方だけなら、cloudflare へマージしてよい。

前提（チャット側。平野さんの決定ではない）

* 対象の6本（OLT-04 の表）: `ff8_G1P7dm0`（逢川恵夢s二階堂瑠美）、`6Sem9jKnkVU`・`SGlbTPLSs7Q`（覚野陽生v猿渡輝也）、`WeTjFR4EBtg`（葉山唯一s麻生知花）、`FBThlykRgWA`・`qjNFjwKtMQU`（佐々木寿人v阿久津翔太）。
* 規則の形の案は OLT-04 のとおり（`SPLIT_VS` に、漢字・かなに挟まれた `[vVsS]` 1文字を足す）。大文字を含めるか、全角の ｖ・ｓ を含めるかは実データで当たる件数を見て決めてよい（決めた理由を書く）。
* 【3】は平野さんの入力先なので、補正2本は平野さんがシートで消す（この指示ではシートの【3】に書き込まない）。消す前に、新しい【2】の対局者が今の補正と同じになることを確かめる。
* 【2】への反映は、マージ後に【2】を書き直すワークフロー（09-30 00:01 UTC に手動実行した apply の run 36648255283 と同じもの）を手動実行して行う見込み。手動実行の前に docs/notes/static-generation.md「ワークフローを手動実行するとき」を読む。

手順

1. 規則とテスト、模擬: `scripts/lib/live_extract.py` の区切りを直し、テストを足す（6パターンが2人に分かれること、区切らないはずの名前〈「漢字＋s・v＋漢字」ではないもの、英字を含む名前の例があれば〉が分かれないこと）。【1】の全動画で、直す前と後の【2】の対局者を比べ、変わる動画の一覧を書く（6本以外が変われば止まる）。変わった6本の新しい対局者を書き、`6Sem9jKnkVU` と `SGlbTPLSs7Q` について今の【3】の対局者の補正と並べる。新しい【2】と今の【3】で /live・title/ を作業コピーに生成し（シートに書かない模擬）、今の生成物との差分を種類に分けて書く。テスト・配信上限・CLAUDE.md の検証を通す。
2. マージと【2】への反映: 手順1 の差分がマージの条件を満たせば、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。続けて【2】を書き直すワークフローを cloudflare で手動実行し（待つ上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）、シートの【2】で6本の対局者と「理由」の列（未登録の名前が消えたこと）を確かめる。/live・title/ の再生成が走ったら、その差分が手順1 の模擬と同じかを書く（走らなければ、次にいつ走るかを書く）。
3. 平野さんへの依頼と記録: 【3】で平野さんが消すセル2つを、シートの行番号・列名・今の値で書く（行番号は読んだ時点のもの。消すのは【2】が直った後）。#446 に項目8 の追加としてコメントし、#475 に「次の毎朝の実行で『逢川恵夢s二階堂瑠美』『覚野陽生v猿渡輝也』が消える見込み」とコメントする。

止まる条件

* OLT-04 が完了していない。`live_extract.py` とそのテストに触れる未マージのブランチがある。
* 手順1 で6本以外の動画の対局者が変わる。6本のどれかが2人に分かれない。
* 手順1 の模擬の差分がマージの条件を満たさない（判断待ちで止める）。
* テスト・配信上限・検証が通らない。本番のビルドやワークフローが失敗した（戻さずに状態を書いて止まる）。
* cloudflare への push や、ワークフローの手動実行が権限の判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。
* 報告の「判断が必要なこと」に、平野さんが【3】で消すセル2つ（手順3）を書く。
* 作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-OLT-06.md を書き、最後の行に Chat-Ref: CHAT-0930-OLT-06 を書く。

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-0930-OLT-06` のコミットは無し。`work/0930-olt-06` はローカル・リモートとも無し → `git checkout -b work/0930-olt-06 origin/cloudflare`
- 「指示」欄の末尾は指示文の最後の行と一致。OLT-04 の `## 報告` は「状態: 完了」
- 未マージのブランチ（`work/0930-bng`・`work/0930-cal-450`・`work/0930-cal-dec`・`work/0930-cal-full`）はどれも `live_extract.py` とそのテストに触れていない

## 報告

- 状態: 作業中
- ブランチ: work/0930-olt-06
- ログ: https://github.com/retroeater/mj/blob/work/0930-olt-06/docs/logs/CHAT-0930-OLT-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-olt-06
- 確認用URL: なし
- マージ: 未
- issue: #446、#475
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b9b7e174）: https://github.com/retroeater/mj-logs/tree/main/guide/b9b7e174

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b9b7e174/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b9b7e174/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b9b7e174/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b9b7e174/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b9b7e174/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
