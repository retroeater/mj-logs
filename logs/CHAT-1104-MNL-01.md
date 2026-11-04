# CHAT-1104-MNL-01

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-mnl
- 着手時HEAD: 96e0bc84b

## 指示

【Claude作成】Claude Code 向け指示：chat-side-operations.md に「手作業の手順・確認の依頼には、その操作をするページへ直接飛べるリンクを付ける」を足す
Chat-Ref: CHAT-1104-MNL-01
マージ: 承認済み（チャットで）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-mnl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-mnl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
チャット側が平野さんに手作業（ワークフローの手動実行、ダッシュボードの設定、画面の確認など）を頼む・手順を答えるとき、各手順にその操作をするページへ直接飛べるリンクを必ず付ける規則を、チャット側の運用の正（docs/notes/chat-side-operations.md）に書く。
決定（2026-11-04、平野さん）

* 手作業の手順を聞かれて答えるときは、直接そのページに飛べるリンクを付ける。チャットのルールとして徹底する（きっかけ: 「最強戦2026 超技巧合戦」の結果をシートに入れた後の regenerate-saikyo.yml の手動実行の手順に、リンクが無かった）
* この指示の変更は、差分をログに引用したうえで cloudflare にマージしてよい

前提（チャット側。平野さんの決定ではない）

* 書き場所の案: chat-side-operations.md「書く前に実物で確かめる」の「平野さんの手作業（画面を見るだけ等）が安価で…」の項の近く、または「Claude Code とのやり取り」節。実物を読んで、より合う所に置いてよい
* 文面の案: 「平野さんに手作業（手動実行・設定・画面の確認）を頼む・手順を答えるときは、各手順に、その操作をするページへ直接飛べるリンクを付ける（例: ワークフローは `https://github.com/retroeater/mj/actions/workflows/<ファイル名>`。探す手間を省き、別の画面での操作を防ぐ）」。リンクは推測で組み立てず、ファイル名・パスを実物で確かめたものにする旨を一句入れてよい
* チャット側が mj-logs の guide で探した範囲では、同じ趣旨の記述は docs/ に無かった（要確認）
* chat-side-operations.md は上限のある文書（冒頭の注意）。純増した分は、同じ量を docs/notes/ の各文書へ移す（冒頭の規則と check-assets.yml の警告に従う）

手順

1. 同じ論点（チャット側が手順・確認の依頼に付けるリンク）の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。
2. docs/notes/chat-side-operations.md の今の内容（冒頭の上限の注意を含む）と、`git grep -n -e 'リンク' -- docs/notes/chat-routines.md docs/notes/chat-side-operations.md docs/instruction-template.md CLAUDE.md` で同じ趣旨の記述を確かめる。同じ趣旨の記述があれば、止めずにそれを置き換え・拡張する（処理を報告に書く）。上の「前提」の文面の案を元に、規則と理由の一句で1項目を足す。純増した分は冒頭の規則どおり docs/notes/ の他の文書へ移し、移した箇所と行数をログに書く。
3. check-assets.yml（work/** への push で走る）の結果を確かめ、差分（変更した行の前後）をログに引用してから、cloudflare にマージする。

止まる条件

* 同じ論点の issue がある
* 足す規則が既存の記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る
* 上限を守るために移せる記述が見つからない（移す先の候補を報告に書く）
* check-assets.yml がこの変更による失敗・警告を出し、直せない
* 未マージの work/ ブランチが docs/notes/chat-side-operations.md の同じ節・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-MNL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-MNL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべて揃っている
- Chat-Ref の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-MNL-'` は0件、`docs/logs/CHAT-*-MNL-*.md` の追加履歴も0件（このセッションの最初の指示）
- 作業ブランチ: `work/1104-mnl` はローカル・リモートとも無し。`origin/cloudflare`（96e0bc84b）から作った
- **規則の表の確認で止まった。** この指示は docs/notes/chat-side-operations.md（チャット側のガイド文）に規則を1項目足すものだが、指示文に「知見 → 選んだ層 → 理由」の表が無い。
  CLAUDE.md「Chat-Ref」の「ガイド文（CLAUDE.md・handover.md・docs/notes/ のチャット側の文書）に規則を足す指示に『知見 → 選んだ層 → 理由』の表が無ければ、着手せず報告して止まる」（#492）に当たる。
  同じ止まり方の前例は CHAT-1103-PLG-02
- 手順 1〜3（issue の検索、chat-side-operations.md の確認・編集、check-assets.yml の確認、マージ）はどれも行っていない。docs/decisions/ への決定の追記もしていない（着手していない指示のため）

## 報告

- 状態: 判断待ち / 続き: CHAT-1104-MNL-02
- ブランチ: work/1104-mnl
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-MNL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-mnl
- 確認用URL: なし
- マージ: 済（8b773d6de を fast-forward で push。ログのみ）
- issue: なし
- 判断が必要なこと:
  - 着手前に止まった。docs/notes/chat-side-operations.md に規則（手作業の依頼・手順には操作するページへの直接リンクを付ける）を足す指示だが、「知見 → 選んだ層 → 理由」の表が無い（CLAUDE.md「Chat-Ref」、#492）。
    表を付けて新しい番号で貼り直してほしい（MNL-01 は欠番）。検討の材料（docs/notes/chat-routines.md「観点のチェックリスト」の順）: 計算的センサー（テスト・hook・ワークフロー）は、チャット側の応答にリンクがあるかを見られない。
    推論的センサーの候補は、チャット側の送信前の確認（chat-routines.md の観点のチェックリスト）に「手作業の手順に直接リンクがあるか」を1項目足す案で、これならガイド文の本文に規則を足さずに済む可能性がある
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bb265e15）: https://github.com/retroeater/mj-logs/tree/main/guide/bb265e15

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96e0bc84.md
