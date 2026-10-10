# CHAT-1008-DIC-22

- 着手日時: 2026-10-10
- 対象issue: #533
- ブランチ: work/1008-dic
- 着手時HEAD: 40eea4b1

## 指示

【Claude作成】Claude Code 向け指示：申送り（雛形の止まる条件の欄に重なりの書き方を入れる・#533 に検査だけを回す手段の論点を足す） Chat-Ref: CHAT-1008-DIC-22 マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1008-DIC-21 は完了） 作業ブランチ: クラウドセッションで実行する。work/1008-dic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-dic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
DIC-19・20 は、未マージの work/ ブランチが追記先と同じファイルの別の節を変えていただけで止まり、2往復増えた。`docs/instruction-template.md` には「重なりで止まる条件はファイル単位でなく、同じ行・同じ関数を変えている、または取り込みで衝突する形で書く」の規則が既にあるが、チャット側は雛形の「## 止まる条件」の欄を写して書くため守られなかった。規則を雛形の欄そのものに入れる（申送り A）。あわせて、シートから作る他のページにも検査だけを回す手段を持つ論点を #533 に足す（申送り C）。
決定（2026-10-10、平野さん）

* 振り返りの申送り A（重なりの止まる条件の書き方）と C（シートから作る他のページにも検査だけを回す手段を持つ。#533 に含める）を行う

前提（チャット側。平野さんの決定ではない）

* A は、`docs/instruction-template.md` の雛形（コードブロック内）の「## 止まる条件」の欄に、括弧書きの1行を足す。文面の案: 「（未マージの work/ ブランチとの重なり）未マージの work/ ブランチが〈変えるファイル〉の〈同じ行・関数・節〉を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）」。同じ文書の32行目あたりの規則の本文は残し、雛形からはそこを指さなくてよい（重複が気になれば、本文を雛形の行への参照に縮めてよい。どちらにしたか報告する）。規則だけを書き、事例（DIC-19・20）は書かない
* C は、#533 にコメントで論点を足す。文面の案: 「論点の補足: シートから作るページ（例: 帰り道の `video_wayhome`・`wayhome_episodes`）にも、`generate_resource_dictionary.py --check` と同じく、ファイルを書かずに生成と同じ検査だけを回す手段を持たせる。シートを直した直後に、止まるかどうかを再生成の前に確かめられるようにするため。対象のページの選び方（シートを読むページ全部か、止まった実績のあるページからか）も論点」。今の論点と重なれば、重なる論点の番号を書いて補足にする
* #533 の着手の状況（「状況:」ラベル・着手中のコメント・未マージの work/1009-rgn 等）を確かめて報告する（チャット側は、RGN-01 は起票までで、実装の指示はまだ出ていないと見ている〈要確認〉）

手順

1. 確かめる: 上の決定を `docs/decisions/` に足す。#533 の本文・コメント・ラベルを読み、Open であることと着手の状況を確かめる。未マージの work/ ブランチが `docs/instruction-template.md` の雛形の「## 止まる条件」の欄を変えていないか確かめる（同じファイルの別の箇所の変更は止まる理由にしない。ログに書く）。
2. 書く: A の1行を足す（追記先の今の内容を読んでから。容量は CLAUDE.md の更新ルールのとおり測る）。#533 にコメントする。
3. 報告する: マージの行のとおり、完了を報告して cloudflare へ入れる。

止まる条件

* #533 が Closed
* 未マージの work/ ブランチが `docs/instruction-template.md` の雛形の「## 止まる条件」の欄を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 追記先が容量の上限を超える
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（#533 の着手の状況を書く）
* マージは冒頭の「マージ:」の行のとおり（変更は docs/logs・docs/decisions・docs/instruction-template.md だけ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- `git log --all --grep="CHAT-1008-DIC-22"` は0件
- origin/work/1008-dic（a929cafc）は origin/cloudflare の祖先（マージ済み）。ローカルも同じで祖先のため、`git merge --ff-only origin/cloudflare` で 40eea4b1 に進め、ログを積んで push した

### 手順1（確かめ）

- `docs/decisions/features.md` に 2026-10-10（DIC-22）の決定を足した
- #533: Open。ラベルは「分野: 自動化」「対象: 全ページ」だけで「状況:」ラベルは無い。担当者なし。コメントは2件（どちらも DIC-18・DIC-21 のもの）で、着手中のコメントは無い
- #533 の着手の状況: work/1009-rgn（先頭 eabe7134、2026-10-09）は CHAT-1009-RGN-01（起票）のログだけで、cloudflare にマージ済み。`git log --all --grep="CHAT-1009-RGN-0[2-9]"` は0件で、実装の指示は出ていない（チャット側の見立てのとおり）
- 未マージの work/ ブランチ（work/1008-hou・work/1009-nen・work/1009-swp-526）は `docs/instruction-template.md` を変えていない。止まる条件に当たらない

### 手順2（書く）

- `docs/instruction-template.md` の雛形（コードブロック内）の「## 止まる条件」の欄に、前提の文面のとおり1行を足した（見込みのずれの行と、cloudflare への push の行の間）:
  「- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが〈変えるファイル〉の〈同じ行・関数・節〉を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）」
- 32行目あたりの規則の本文（「未マージの `work/` ブランチとの重なりで止まる条件は、ファイル単位でなく…」）は**残した**（理由と #277 の参照を持つ本文で、雛形の行は写すための形。変更を最小にするため縮めていない）
- 容量: `docs/instruction-template.md` 12,625 → 12,921 バイト（警告域 14KB・上限 16KB の内）
- #533 に論点の補足をコメントした（前提の文面のとおり。今の論点 (a)〜(h) は止まった後の扱いで直接は重ならないこと、DIC-21 で足した論点との組み合わせ、止まった実績のあるページを添えた）

## 報告

- 状態: 完了
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1008-DIC-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 済（docs のみ。cloudflare へ push した SHA は最終報告の「ログ（公開）」の行）
- issue: #533（論点の補足をコメント。Open・「状況:」ラベルなし・着手中のコメントなし。RGN-01 は起票までで実装の指示は出ていない）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dbea2935）: https://github.com/retroeater/mj-logs/tree/main/guide/dbea2935

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbea2935/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbea2935/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbea2935/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbea2935/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbea2935/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbea2935/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
