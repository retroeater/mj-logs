# CHAT-1113-RSC-26

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: c3ef367b3

## 指示

【Claude作成】Claude Code 向け指示：最強戦の過去の順位（B）の残り、2019〜2021年度の旧公式の45卓を、一度きりの手段を戻して「最強戦」シートの F列と候補タブへ書き、書き終えたら外す
Chat-Ref: CHAT-1113-RSC-26
マージ: 承認済み（チャットで。下の「マージと本実行の条件」をすべて満たすときだけ。書き込みの手段を戻すマージ、書き込みの本実行、片付け〈一度きりの手段を外す変更〉のマージ）
貼る時機: いつでも（「最強戦」のブックが live-channel-writer に共有されたままかは手順1の dry-run で確かめる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1113-RSC-24 で書いたのは 2013〜2018年度の旧公式の64行（採用61・却下3）だけで、2019〜2021年度の旧公式の45行（CHAT-1104-RSC-06 で書いた行）は候補タブの「状態」も「最強戦」シートの順位も空のまま残っている。RSC-24 と同じ道筋で、この45卓を書く。
決定（2026-11-13、平野さん）

* 2019〜2021年度の旧公式の45行（照合済み44・未照合1）を、RSC-24 と同じ方法（「最強戦」のブックの live-channel-writer への一時の共有で、Claude Code が候補タブの「状態」と「最強戦」シートの F列〈空のセルだけ〉を書く）で書く。2026-10-29 の決定「本体のタブに入れるのは平野さん」の例外（RSC-23・24 と同じ）
* 未照合の1行（2021-09-04 男子プロ最強新世代 決勝。旧公式は「斎藤敬輔」、シートは「齋藤敬輔」）も「採用」にする。正の名前はシートの「齋藤敬輔」。順位は候補のとおり（1位 岡崎涼太・2位 尻無濱航・3位 増田隆一・4位 齋藤敬輔）。シートの名前は変えない
* 下の「マージと本実行の条件」をすべて満たせば、マージ・本実行・片付けのマージまで進めてよい（条件の答えは平野さんがチャットで、ほかの問いと分けて出した）

前提（チャット側。平野さんの決定ではない）

* 候補タブの今の状態（平野さんが 2026-11-13 20:26 に送った候補タブの PDF〈出典で jimdoweb に絞ったもの〉をチャット側が読んだ）: 種別 B・モデル「旧公式」の行のうち、2013〜2018年度の64行は状態が「採用」61・「却下」3。2019〜2021年度の旧公式の行は状態が全部空で、照合済み44・未照合1（2021-09-04 男子プロ最強新世代 決勝）。この数は手順1の dry-run で確かめる（要確認）
* 「最強戦」シートのこの45卓の順位は空と見込む（チャット側が mj の生成物 `saikyo/2021/20210314-s2.html` などを読み、「優勝」「敗退」は出ているが 1〜4位の順位が無いことを見た。全卓は見ていない）。既に値のあるセルは「同じ値」か「値があって違う」に数えられる
* 一度きりの手段は、RSC-23 のコミット 78bcfe28c で足し、RSC-24 の片付けのコミット 7452e5afc（78bcfe28c の revert）で外した（CHAT-1113-RSC-24 のログの「4. 片付け」）。戻すのは 7452e5afc の revert でよい。対象の行の拾い方は今「種別 B・モデル『旧公式』・調べた日時が 2026-11-13」なので、「種別 B・モデル『旧公式』・状態が空・対象キーの年度が 2019〜2021」に変える（却下・採用済みの行は変えない）。未照合の1行は、名前を決定のとおりシートの「齋藤敬輔」で照らす（方法は実物に合わせてよい。ログに書く）
* 「最強戦」のブックの共有を平野さんが外していれば、dry-run で Drive API が 404 になる（RSC-23 と同じ）。そのときは止まり、平野さんに共有をもう一度頼む
* 順位を書くと、マージ後・毎朝の再生成で、2019〜2021年度の最強戦のページ（卓のページ・個人成績・ランキング）が変わる。鍵の列は変えないので ABEMA の表示は欠けない見込み（RSC-24 と同じ）

マージと本実行の条件（すべて満たすときだけ進む。1つでも外れたらマージ・本実行をせず止まり、数と全件の表をログに書く）

* 作業ブランチでの dry-run: 採用にする行が 45行、「最強戦」の書くセル＋同じ値のセルの合計が 180（45卓×4人）、値があって違うセル 0、引けない卓 0
* 書く先は、候補タブの「状態」の列と「最強戦」シートの F列の空のセルだけ（ほかの列・行・タブを変えない）
* cloudflare でのマージ後の dry-run が、作業ブランチでの dry-run と同じ数・同じ全件の表
* 本実行の後の読み直し: 順位の違うセル 0・状態の違う行 0。念のための dry-run で書くセル 0
* 片付けの後、RSC-24 の片付けの後と同じ状態（戻したファイルが 78bcfe28c^ とバイト単位で同じ、scripts/・.github/ に残った参照 0）。試験（全体）が通る

手順

1. 前提を確かめる: #630 の状態とコメント（他のセッションの着手中のコメントがあれば止まる）。未マージの work/ ブランチとほかのチャットの実行中の指示が、戻すファイル（78bcfe28c が変えたファイル）を変えていないか（`git branch -r --no-merged origin/cloudflare` と各ブランチの差分）。7452e5afc を revert して拾い方を変え、試験を足して通す。作業ブランチで research-candidates.yml を `source=adopt`・dry-run で手動実行し、上の条件の数と全件の表（候補タブの行・「最強戦」の行・名前・書く順位）をログに書く
2. 条件を満たせば cloudflare へマージし、cloudflare で dry-run → 本実行（読み直しを含む）→ 念のための dry-run。regenerate-saikyo.yml（または通常の再生成）で反映し、公開のページを数卓（2021-03-14 男子プロ因縁の血闘 決勝・2020-12-12 ファイナル 1st Stage ベスト16 A卓・2021-09-04 男子プロ最強新世代 決勝など）で確かめて URL をログに書く。本番の反映を15分待って古いままなら、「未確認の項目」に回して先へ進む
3. 片付け: 戻した一度きりの手段をもう一度外す（この指示の revert を戻す形でよい）。試験（全体）を通してマージする。B の dry-run で「順位の空いた人がいる卓」の数を出し、前の数（RSC-24 のログの 336卓）と比べてログに書く。#630 に結果（書いた卓の数・残りの卓の数）をコメントし、本文の「今の件数」の節を直す。docs/decisions に記録する（RSC-23・24 の例外の続きとして）。「判断が必要なこと」に、平野さんの手作業「『最強戦』のブックの live-channel-writer の共有を外す」を書く

止まる条件

* 上の「マージと本実行の条件」のどれかが外れる
* 「最強戦」のブックが読めない・書けない（Drive API の 404 など。共有が外れている）
* #630 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチ・ほかのチャットの実行中の指示が、戻すファイルの同じ行・関数を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない
* マージ後の check-run・手動実行の失敗は、今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい
* コミットの件名・本文に、閉じるキーワード（close・fix・resolve とその活用形）の直後の `#番号` が入る（push の前に範囲のコミットを確かめる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-26.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-26 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-26.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c3ef367b）: https://github.com/retroeater/mj-logs/tree/main/guide/c3ef367b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/de3fa135.md
