# CHAT-1013-RGN-19

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-rgn
- 着手時HEAD: e92eef3e

## 指示

【Claude作成】Claude Code 向け指示：申送り A。chat-side-operations.md のシートの規則に、止まるかどうかを先に `regenerate.py --check` で確かめられる一句を足す。ドキュメントのみ Chat-Ref: CHAT-1013-RGN-19 マージ: ドキュメントのみ（docs/・ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・生成物は変えない 貼る時機: いつでも（CHAT-1013-RGN-18 は完了） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1013-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#551 で全ページが `regenerate.py --check`（ファイルを書かず、止まる理由と警告を全部出す）を持った。チャット側が、平野さんがシートを直したと聞いたときに、止まるかどうかだけなら先にこれで確かめられることを、チャット側の規則の文書に一句で残す。
決定（2026-10-13、平野さん）

* 申送り A を行う: docs/notes/chat-side-operations.md のシートの規則に、`regenerate.py --check` の一句を足す。今の規則（生成と同じ経路で読ませ、見出し・件数・既存の公開物との差を報告させる）は置き換えずに残す（`--check` はそれらを返さないため。Fable 5.1 のレビュー）

前提（チャット側。平野さんの決定ではない。追記先の実物に合わせて文面を変えてよい）

* 追記先: docs/notes/chat-side-operations.md の「平野さんがシート（タブ）を用意した・直したと言ったとき: 実装の指示の前に、生成と同じ経路で読ませ、見出し・件数・既存の公開物との差を報告させる…」の項（要確認。ガイドの a83cb48a では 76行）
* 文面の案（項の末尾に足す）: 「生成で止まるかどうかだけなら、先に `python3 scripts/regenerate.py --check <ページ>` を流させる（ファイルを書かず止まる理由と警告を全部出す。static-generation.md）」
* 容量: 今 25,112 バイト（CHAT-1013-RGN-18 の報告）、警告 26KB（26,624）／失敗 28KB。足した後のサイズを測り、警告域に入るなら足さずに止まる
* 規則と理由の一句だけ。事例・経緯は書かない

手順

1. 確かめる: 同じ論点の open issue を検索し（クローズ済みも含めて確認。語: 「--check」×「シート」「チャット側」）、あれば止まって報告する。未マージの work/ ブランチが docs/notes/chat-side-operations.md の追記先の項を変えていないか確かめる（同じファイルの別の項の変更は止まる理由にしない。重なりの内容はログに書く）。
2. 足す: 追記先の今の内容を読んでから一句を足す（同じ趣旨の記述があれば置き換え・拡張し、どう処理したかを報告に書く）。前後のサイズを測る。上の「決定」を `docs/decisions/operations.md`（合う分野）に足す。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。assets-check の結果を待つ（上限15分）。

止まる条件

* 同じ論点の issue（クローズ済みを含む）がある
* 追記先と矛盾していて、どちらが正か判断が要る（同じ趣旨なら止めず置き換え・拡張）
* 足した後のサイズが警告域（26,624 バイト）に入る（足さずに止まる）
* 未マージの work/ ブランチが追記先の同じ項を変えている、または取り込みで衝突する（両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（前後のサイズを書く）
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-RGN-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-RGN-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e92eef3e）: https://github.com/retroeater/mj-logs/tree/main/guide/e92eef3e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e92eef3e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e92eef3e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e92eef3e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e92eef3e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e92eef3e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e92eef3e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
