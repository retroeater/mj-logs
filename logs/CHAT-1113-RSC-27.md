# CHAT-1113-RSC-27

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: 959b5c98d

## 指示

【Claude作成】Claude Code 向け指示：「最強戦」のブックの共有をし直したので、RSC-26 の手順1の dry-run からやり直し、2019〜2021年度の旧公式の45卓を書いて片付ける
Chat-Ref: CHAT-1113-RSC-27
マージ: 承認済み（チャットで。CHAT-1113-RSC-26 の「マージと本実行の条件」をすべて満たすときだけ。書き込みの手段を戻すマージ、書き込みの本実行、片付け〈一度きりの手段を外す変更〉のマージ。条件は RSC-26 と同じで、平野さんの承認〈2026-11-13〉もそのまま）
貼る時機: 平野さんが「最強戦」のブックを live-channel-writer に編集者でもう一度共有した後（RSC-26 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1113-rsc を続けて使う（RSC-26 で戻した書き込みの手段と拾い方の変更〈コミット 407e3f9a0〉をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-26 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-27` を足す。「貼る時機」の前提（共有）は手順1の dry-run で確かめる。

目的
CHAT-1113-RSC-26 は、「最強戦」のブックの共有が外れていた（Drive API の 404）ので、dry-run の後で止まった。平野さんが共有をし直したので、RSC-26 の手順1の dry-run からやり直し、手順2・3（マージ・本実行・再生成・片付け）まで進める。
決定（2026-11-13、平野さん）

* CHAT-1113-RSC-26 の「決定」のとおり（45行を採用、未照合の1行の正の名前はシートの「齋藤敬輔」、Claude Code が候補タブの「状態」と「最強戦」シートの F列〈空のセルだけ〉を書く）
* 「マージと本実行の条件」も RSC-26 のとおり（作業ブランチの dry-run で採用 45行・書くセル＋同じ値＝180・違う 0・引けない卓 0、書く先は「状態」と F列の空のセルだけ、cloudflare の dry-run が同じ数と同じ全件の表、読み直しで違い 0、念のための dry-run で書くセル 0、片付けの後は RSC-24 の片付けの後と同じ状態）

前提（チャット側。平野さんの決定ではない）

* RSC-26 の作業ブランチでの dry-run（run 43419175939）の数は、採用 45行・書くセル 180・同じ値 0・違う 0・引けない卓 0（RSC-26 のログの「2.」）。やり直しの dry-run はこの数と全件の表に照らす
* 平野さんが共有をし直したかは、手順1の dry-run で確かめる（404 のままなら止まる）

手順

1. 作業ブランチで research-candidates.yml を `source=adopt`・dry-run で手動実行し、数と全件の表を RSC-26 の dry-run と突き合わせる（違えば全件の違いを書いて止まる）
2. RSC-26 の手順2（cloudflare へマージ → cloudflare で dry-run → 本実行〈読み直しを含む〉→ 念のための dry-run → 再生成 → 公開のページを数卓で確かめる）。本番の反映を15分待って古いままなら、「未確認の項目」に回して先へ進む
3. RSC-26 の手順3（片付け・試験・マージ、「順位の空いた人がいる卓」の数の前後〈336卓から〉、#630 へのコメントと本文の「今の件数」の直し、docs/decisions への記録、「判断が必要なこと」に平野さんの手作業「『最強戦』のブックの live-channel-writer の共有を外す」）

止まる条件

* RSC-26 の「止まる条件」のとおり（条件の外れ、ブックが読めない・書けない、#630 の他のセッションの着手中、未マージのブランチとの重なり、無関係な失敗の扱い、一時的な接続の失敗は1回再実行、閉じるキーワードの直後の `#番号`、push の拒否）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1113-rsc/docs/logs/CHAT-1113-RSC-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-rsc
- 確認用URL: なし
- マージ: 未
- issue: #630
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a24c8b2b）: https://github.com/retroeater/mj-logs/tree/main/guide/a24c8b2b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a24c8b2b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a24c8b2b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a24c8b2b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a24c8b2b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a24c8b2b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a24c8b2b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/de3fa135.md
