# CHAT-1012-SNM-06

- 着手日時: 2026-10-12
- 対象issue: #548
- ブランチ: work/1012-snm-ampai
- 着手時HEAD: cc0eff0e

## 指示

【Claude作成】Claude Code 向け指示：「鳳凰Ampai」「桜花Ampai」を1つの「Ampai」タブにまとめるため、今のタブを調べ、新しいタブのレイアウトと初期データ（貼り付け用 TSV）を作る（#548。調査のみ。コードもシートも変えない）
Chat-Ref: CHAT-1012-SNM-06
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-snm-ampai の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜05 で使ったもので、重複ではない。
作業ブランチ: クラウドセッションで実行する。work/1012-snm-ampai を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-snm-ampai origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
「プロ」の「鳳凰Ampai」「桜花Ampai」の2列と、「鳳凰Ampai」「桜花Ampai」の2タブを、棋戦（鳳凰戦・女流桜花）を1列で持つ新しい「Ampai」タブ1つにまとめたい。そのために今のタブの中身を調べ、新しいタブのレイアウトを決める材料と、平野さんがシートに貼る初期データを作る。この指示ではコード・生成物・シートは変えない（変わるのはこのログと docs/decisions/ だけ）。生成を新しいタブに切り替える・古い列とタブを消すのは次以降の指示。

### 決定（2026-10-12、平野さん）
- 「プロ」の「鳳凰Ampai」「桜花Ampai」の2列を、新しく作る「Ampai」タブに移す。鳳凰戦・女流桜花の区別を1列に持ち、1つのタブにまとめる
- 新しいタブのレイアウトと初期データは、チャット側（Claude）が用意する
- （既存の決定: 2026-10-11〈CHAT-1011-RDN-14〉「Ampai の URL は複数の期をすべて管理し、選手の期・リーグ・組で URL を選ぶ形に変える。新しい期で URL が無いときは知らせる」〈#548〉）

### 前提（チャット側。平野さんの決定ではない）
- ログにこう書いてある（このチャットはシートを読んでいない。すべて要確認）:
  - 「鳳凰Ampai」595行・「桜花Ampai」138行。先頭の列の見出しが期の名前（「鳳凰43後」「桜花21」）で、「リーグ」「Ampai URL」などの見出しが重複している（CHAT-1010-RDN-07 のログ「経過」）
  - 「プロ」の鳳凰Ampai・桜花Ampai の列は `=XLOOKUP($A2,'鳳凰Ampai'!$A:$A,'鳳凰Ampai'!$C:$C,"")`（桜花も同じ形）で、名前で引いている（CHAT-1010-RDN-01 のログ「経過」）
  - `generate_jpml_pros.py` は URL を「プロ」の2列から読み、Ampai のタブは先頭の見出し（期）だけを読んで最新の期と照合する（CHAT-1010-RDN-07）
- レイアウトの案（チャット側。実物に合わせて変えてよい）: Ampai の URL がリーグ・組ごとに1つなら、1行を「棋戦 × 期 × リーグ × 組」にする。列は 棋戦（「鳳凰戦」「女流桜花」）・期・リーグ・組・Ampai URL・備考。期・リーグ・組の書き方は、生成が選手の期・リーグ・組を引く集計元のタブ（「鳳凰」「桜花」など。`lib/pro_stats.py` の読み方）と同じにし、生成が文字列の一致で突き合わせられるようにする。複数の期を足していける形にする（初期データは今ある期だけ）
- URL が選手ごとに違う（同じリーグ・組でも人によって違う）なら、上の案は使えない。その場合は1行を「棋戦 × 期 × 名前」にする案（棋戦・期・名前・Ampai URL・備考）と比べる
- 使う skill は無い

## 手順
1. 確かめと読む所: #548 の Open/Closed・本文・コメントを読み、ほかの Chat-Ref の未完了の着手中コメントが無いことを確かめ、#548 に着手中のコメントを書く（本文・状態・ラベルは変えない）。`git branch -r --no-merged origin/cloudflare` の一覧を書く。リポジトリ全体（`scripts/`・`scripts/tests/`・`.github/workflows/`・ページ側の JS・`docs/`）を「鳳凰Ampai」「桜花Ampai」「Ampai」で grep し、読む所（ファイル・関数・読む値）を表にする
2. 今のタブの中身: 「鳳凰Ampai」「桜花Ampai」を生成と同じ経路でそれぞれ2回読み、見出し（列の英字・並び順のまま、重複も）・行数・列ごとの値の入っている件数・先頭の5行を書く。「プロ」のブックを xlsx でエクスポートし（CHAT-1011-RDN-10 と同じ方法）、2つのタブの数式の有無（どの列が手入力でどの列が数式か）と、ほかのタブ・「プロ」の列からこの2タブを引いている数式（タブ・セル範囲・数式の例・件数）を書く。そのうえで、Ampai の URL の単位を調べる: 異なる URL の数、URL ごとの人数、同じ期・リーグ・組で URL が2つ以上ある組とその人の名前の一覧。あわせて、集計元のタブから引いた選手の最新の期のリーグ・組と、Ampai のタブのリーグ・組が食い違う人の名前の一覧を書く
3. 初期データ: 手順2の結果に合う方の案（上の前提）で、新しい「Ampai」タブの見出しの行と初期データを作る（今ある期の全部。棋戦・期・リーグ・組の書き方は集計元のタブと同じ）。ログの `## 報告` の項目の後ろに、見出しの行を含む TSV（タブ区切り。平野さんがシートの A1 に貼るとそのまま列に分かれる形）をコードブロックで書き、行数と、今の2タブから何を写したか（写さなかった列・行とその理由）を書く。あわせて、生成を新しいタブに切り替えるときに直す箇所（ファイル・関数）と、残る決めること（新しい期で URL が無いときの知らせ先など、#548 の本文の項目）を一覧にする。docs/decisions/pros.md に上の「決定」を足す

## 止まる条件
- 「鳳凰Ampai」「桜花Ampai」が読めない、2回の読みで行数・値が変わる、行数が前提（595・138）から ±50 を超えて違う
- #548 が Closed、またはほかの Chat-Ref の未完了の着手中コメントがある
- 「棋戦 × 期 × リーグ × 組」の案で TSV が 200 行を超える、または URL が選手ごとに違い「棋戦 × 期 × 名前」の案になる（TSV は書かず、両案の行数と、選手ごとに違う URL の例〈名前と URL〉を書いて止まる）
- コード・生成物・シートを変える必要が出た（変えずに止まる）
- docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た（変えずに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- 手順1の表、手順2の見出し・件数・数式・URL の単位・食い違いの名前の一覧、手順3の TSV・行数・直す箇所と残る決めることの一覧がログにある（TSV は `## 報告` の後ろ）
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-SNM-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-SNM-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6d9223d9）: https://github.com/retroeater/mj-logs/tree/main/guide/6d9223d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d9223d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d9223d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d9223d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d9223d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d9223d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d9223d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7b3e1334.md
