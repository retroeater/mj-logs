# CHAT-1104-SKN-06

- 着手日時: 2026-11-05
- 対象issue: 新しい issue（起票する）・#528（コメントのみ）
- ブランチ: work/1105-skn-layout
- 着手時HEAD: 28ff50d2a

## 指示

【Claude作成】Claude Code 向け指示：SKN-05 の続き（新サイトの形のページの統一を別の issue にして、SKN-05 の手順1から進める） Chat-Ref: CHAT-1104-SKN-06 マージ: 判断待ちで止まる 貼る時機: いつでも（SKN-05 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-skn-layout を続けて使う（SKN-05 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-SKN-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-SKN-06` を足して push する。

目的
CHAT-1104-SKN-05 の「判断が必要なこと」への回答。新サイトの形のページの配置の統一を #528 と別の issue にし、SKN-05 の作業を手順1から進める。
決定（2026-11-05、平野さん）

* 新サイトの形のページ（houou/ を基本にした最強戦・ouka/・jwrc/・wrcr/・title/・pro/・live/ など）の配置の統一と差異の洗い出しは、#528 と別の新しい issue にする（SKN-05 の報告の案のとおり）。#528 は現行サイトの旧い系統の論点として残す
* #528 の「本文の列」の行のそばに、「最強戦は 400px の中央寄せに決めた（2026-11-05）。新サイトの形のページは #<新しい issue> で扱う」とコメントで追記する（#528 の本文・状態・ラベルは変えない）
* SKN-05 の決定1〜3・「知見 → 選んだ層 → 理由」の表・前提・手順・止まる条件は、そのまま使う（SKN-05 のログの「指示」欄）。#528 は止まる理由にしない

前提（チャット側。平野さんの決定ではない）

* 新しい issue の題の案:「新サイトの形のページの配置・部品を houou/ の基本の形にそろえ、検査で守る」。本文は SKN-05 の目的と決定1〜3、範囲（新サイトの形のページ。旧い系統は #528）、差異の表は SKN-05 の続きのログに書くこと。ラベルは既存の issue の付け方にそろえる
* 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す

手順

1. 同じ論点の issue（#528 以外。クローズ済みも含む）が無いことを確かめ、新しい issue を起こし、#528 にコメントする。
2. SKN-05 の手順1〜3（差異の表 → 最強戦の7画面を 400px の中央寄せにそろえ、テストを足す → 再生成とプレビューの確かめ、docs の直し）を行う。差異の表・最強戦以外で直す候補の一覧を、新しい issue にコメントで書く（ログの節を引用する）。
3. 仮置き・決まっていない差異・卓のページの扱いを「判断が必要なこと」に並べて止まる。

止まる条件

* SKN-05 の止まる条件（#528 を除く）
* マージは判断待ち。cloudflare には入れない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。差異の表は `## 経過` に全件で書く。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1105-skn-layout
- ログ: https://github.com/retroeater/mj/blob/work/1105-skn-layout/docs/logs/CHAT-1104-SKN-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-skn-layout
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: 未記入
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 759e6347）: https://github.com/retroeater/mj-logs/tree/main/guide/759e6347

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/759e6347/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7864a244.md
