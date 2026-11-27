# CHAT-1127-SNU-02

- 着手日時: 2026-11-27
- 対象issue: なし
- ブランチ: work/1127-snu
- 着手時HEAD: f92ac9d

## 指示

【Claude作成】Claude Code 向け指示：最終報告を「状態＋公開ログの URL」の2行にし、雛形の「作業ブランチ」の行にブランチ名の例を足す
Chat-Ref: CHAT-1127-SNU-02
マージ: ドキュメントのみの変更なので完了報告のうえ cloudflare へ入れてよい（CLAUDE.md「ブランチ運用」）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は下の「完了条件」の形）。平野さんは、この指示のための作業ブランチ work/1127-snu の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1127-snu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1127-snu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
平野さんが Code からチャットへ渡しているのは公開ログの URL の1行だけなので、ターミナルへの最終報告をそれに合わせて短くする。あわせて、CHAT-1127-SNU-01 でチャット側が作業ブランチ名を `work/SNU` と書き間違えた原因（雛形の `work/<識別子>` に例が無い）を直す。変更は docs のみ（docs/decisions を含む）。

### 決定（2026-11-27、平野さん）
- ターミナルへの最終報告は2行にする: 1行目に状態（完了・判断待ち など）、2行目に公開ログ（mj-logs）の URL だけ（ラベル「ログ（公開）:」は付けない。`?v=<SHA>` は今までどおり付ける）。今回の例:
  ```
  完了
  https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-SNU-01.md?v=890e95e
  ```
- 無くす行: mj のログの URL・「ブランチ:」・「Chat-Ref:」（どの指示の報告かは URL のファイル名で分かる）
- プレビューで確認する作業では「確認用: <プレビューの URL>」の行だけ足す
- 質問して止まるとき・push に失敗したときに内容をターミナルに書く例外は、今のまま残す
- Chat-Ref そのもの（指示文の先頭・ログのファイル名・コミットのトレーラ・作業ブランチ名）は変えない
- 平野さんがチャットへ送る完了の知らせは、公開ログの URL の1行（2026-10-14 の「最終報告の最後の2行」を置き換える。docs/decisions/operations.md の該当の行に「→ 置き換え」を付ける）
- docs/instruction-template.md の「作業ブランチ」の行に、ブランチ名の例（Chat-Ref から取る。`CHAT-1127-SNU-01` なら `work/1127-snu`）を足す

### 前提（チャット側。平野さんの決定ではない）
- 知見 → 選んだ層 → 理由:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 最終報告の5行のうち、チャットへ渡るのは公開ログの URL の1行だけ | ガイド文（docs/logs/_template.md の形の置き換え。規則は減る） | 形そのものの定義で、テスト・hook で決める対象が無い |
| チャット側が雛形の `work/<識別子>` に3文字の識別子をそのまま入れた（`work/SNU`） | ガイド文（雛形の行に例を足す） | 推論的センサーは既にある（CLAUDE.md「Chat-Ref」の「前提・ルールの側を優先」で Code が直した）。写す元の行を正すのが残り |

- 直す所（チャット側が mj-logs のガイド f92ac9d9 で見た分。ほかは手順2で洗う）: docs/logs/_template.md「ターミナルへ返す最終報告」（規則の行・例のコードブロック・「2行目の URL は…」の説明）、docs/instruction-template.md（23行目あたりの「完了条件の Chat-Ref の行は…」の項・雛形の「共通手順」の行の括弧・「完了条件」の最後の行・「作業ブランチ」の行の文面2つ）、docs/notes/chat-side-operations.md「作業ログの読み方」（「最終報告の最後の2行」・「貼られた `## 報告` には Chat-Ref の行が無い…」の項）、docs/notes/chat-routines.md「mj-logs を取得できないとき」と docs/notes/cloud-sessions.md の「ログ（公開）」の行という呼び方
- 写し待ちのときは、2行目を「写し待ち: <Chat-Ref>（理由）」にする案（URL が無いと、どの指示か分からなくなるため）
- 複数の指示に答えるときは、URL を1行ずつ並べる案
- mj-logs で読めないとき（写し漏れ）に平野さんが開く mj のログの場所は、最終報告から無くなるので、docs/notes/chat-side-operations.md に1か所だけ書く案（マージ前は `blob/<作業ブランチ>/docs/logs/<Chat-Ref>.md`、マージ後は `blob/cloudflare/docs/logs/<Chat-Ref>.md`。ブランチ名は Chat-Ref から決まる）
- この変更より前に貼られた指示文は、完了条件に前の形（「ログ（公開）:」と「Chat-Ref:」の行）が書いてある。前の形で終えてもよいと _template.md に一句書く案（外す時機: 2026-12-11 以降に、前の形を完了条件に持つ未完了の指示が無くなったら消す）
- chat-side-operations.md は上限のある文書。置き換えで書き、純増させない
- 雛形の `<識別子>` と Chat-Ref の「識別子（英大文字3文字）」は語が重なっている。語そのものの直しはこの指示に含めない（例を足すだけ）

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。
2. `git grep -n` で、リポジトリ全体（`.claude/`・`scripts/`・`workers/`・`.github/` を含む）から、最終報告の今の形に触れる所（「ログ（公開）」「Chat-Ref の行」「最後の行に」「最後の2行」「ブランチ:」を最終報告の行として指す記述）を洗い、一覧（ファイル・行・扱い）をログに書く。除外は docs/decisions の過去の行・docs/logs・docs/notes/handover-archive-2026.md の過去の行だけ。最終報告の「Chat-Ref:」「ブランチ:」「ログ（公開）:」の行を機械的に読むスクリプト・hook・ワークフローがあれば、直さずに止まる
3. 追記先の今の内容を読んでから、上の「決定」のとおり直す（前提の案は実物に合わせて変えてよい。変えた点は報告に書く）。docs/decisions/operations.md に今回の決定を足し、2026-10-14 の「最終報告の最後の2行」の決定に「→ 置き換え」を付ける。直した後の _template.md の「ターミナルへ返す最終報告」の節と、雛形の「作業ブランチ」の行2つ・「完了条件」の節を、ログにそのまま引用する

## 止まる条件
- 同じ論点の issue がある、他セッションの着手中コメントがある
- 最終報告の行を機械的に読む仕組みが見つかった（手順2）
- 追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、既存の記述を置き換え・拡張してよい。処理を報告に書く）
- 未マージの work/ ブランチが、直すファイルの同じ行・節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。両方の変更が両立する衝突〈追記どうし・隣り合う行〉は、両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- check-assets.yml がガイド文書のサイズ・chat-side-operations.md の純増で警告した（警告の中身を書いて止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は、直した後の docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-SNU-02.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f92ac9d9）: https://github.com/retroeater/mj-logs/tree/main/guide/f92ac9d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f92ac9d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f92ac9d9.md
