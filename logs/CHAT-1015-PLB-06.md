# CHAT-1015-PLB-06

- 着手日時: 2026-10-15
- 対象issue: #567・#467・#547
- ブランチ: work/1015-plb
- 着手時HEAD: bcec5f2

## 指示

【Claude作成】Claude Code 向け指示：「(旧)」の3タブの削除を確かめて #567 と #467 を閉じ、「対局」タブを消せる条件を #547 に書き、docs/decisions の置き換えの注記を足す Chat-Ref: CHAT-1015-PLB-06 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1015-PLB-05 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-plb を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-plb origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1015-PLB-04 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。判断待ちなら、その状態の末尾に `/ 続き: CHAT-1015-PLB-06` を足す（このログの push と同じ時に）。

目的
シートの整理（#567）の締め。平野さんの手作業が全部済んだので、#567 と、新しいブックで症状が消えた #467 を閉じ、このチャットにしか残っていない「対局」タブの件を #547 に書き、docs/decisions の古い行に置き換えの注記を足す。
決定（2026-10-15、平野さん）

* 「(旧)プロ」（正本）・「(旧)連盟プロ以外」「(旧)別名」（ryoei.pro_その他マスタ）の3タブは、2026-10-15 に前倒しで消した（平野さん、完了）
* #467（「プロ」シートの A 列の gviz の見出しが2行分になる）は閉じる。新しいブック「ryoei.pro_選手」では A1 が「登録名」の1行で症状が無いため。`pro_sheet.label_keys()` の改行の前で引く処理（回避策）はコードに残す（消さない）
* （2026-10-14 の、チャットでの確認の記録）「プロ」のブックの「対局」タブは今は消せない。`generate_video_live.py`（video_live.html）と `lib/pro_stats.py`（jpml_pros の「放送対局」の列）が読んでいるため。消せるのは、#547 で放送対局の数え方の読む先を変え、video_live を廃止した後

前提（チャット側。平野さんの決定ではない）

* 「対局」タブの読み手は、チャット側が 2026-10-14 の origin/cloudflare（a31620f）で確かめたもの。着手時に `git grep` で読み手を洗い出し直し、増減があれば #547 のコメントにはその時点のものを書く（要確認）
* docs/decisions/pros.md の「2026-10-14（CHAT-1014-PLB-03）」の節に「載せる issue は平野さんが別に起票する」とある行は、PLB-05 で #571 を起票したので古い。README.md の書き方に従って「→ 置き換え: 2026-10-15（CHAT-1015-PLB-05、#571）」の注記を足す（行は消さない）
* #567 のクローズ時に残る作業は無い見込み。残っていれば CLAUDE.md「issue の着手ルール」のとおり別の issue に起票する

手順

1. 正本のブックと ryoei.pro_その他マスタのタブ一覧（htmlview）を読み、「(旧)」の3タブが無いことを確かめる（あれば止まる）。#567 に着手中のコメントを残す。
2. 「対局」タブの読み手を `git grep` で洗い出し、#547 に「『対局』タブを消せる条件」をコメントする（読み手の一覧と、#547 で読む先を変え video_live を廃止した後に消せること、#567 からの持ち越しであること）。#467 に、閉じる理由（新しいブックで A1 が1行。回避策は残す）をコメントして閉じる。
3. docs/decisions/pros.md の PLB-03 の行に置き換えの注記を足す。#567 の本文の「マージ後の平野さんの手作業」をすべて済みにし、クローズのコメント（#571・#547 への持ち越しを書く）を残して閉じる。閉じるときは「状況:」ラベルを外す。

止まる条件

* 手順1で「(旧)」のタブが残っている
* 「対局」タブの読み手に、チャット側の確認（generate_video_live.py・lib/pro_stats.py）に無いものがある（止まらず、#547 のコメントに実物の一覧を書き、報告にも書く）
* #567・#467・#547 に他セッションの着手中コメントがある
* docs/decisions/pros.md の該当の行が見つからない、または既に置き換えの注記がある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-PLB-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-PLB-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-plb
- ログ: https://github.com/retroeater/mj/blob/work/1015-plb/docs/logs/CHAT-1015-PLB-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-plb
- 確認用URL: なし
- マージ: 未
- issue: #567・#467・#547
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66464371）: https://github.com/retroeater/mj-logs/tree/main/guide/66464371

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
