# CHAT-1017-MCK-20

- 着手日時: 2026-10-17
- 対象issue: なし（申送り）
- ブランチ: work/1017-mck-handoff
- 着手時HEAD: 3a02c685e

## 指示

【Claude作成】Claude Code 向け指示：（MCK-19 の続き）申送りの3つの規則（b85f3e501）を cloudflare へマージし、作業ブランチを片付ける
Chat-Ref: CHAT-1017-MCK-20
マージ: 承認済み（チャットで、2026-10-17）。条件: origin/cloudflare を取り込んだ後も、cloudflare との差分が MCK-19 の3箇所（chat-side-operations.md「平野さんの判断とマージの許可」の1文・chat-routines.md「期日とカレンダー」の1つ目の項・「ほかのセッションへの共有」の最後の項）と、MCK-19・MCK-20 のログ・docs/decisions/operations.md の追記だけ。chat-side-operations.md が上限（28KB）を超えない
貼る時機: いつでも（CHAT-1017-MCK-19 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-mck-handoff の使用・push・マージ後の削除を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。MCK-19 の work/1017-mck-handoff をそのまま使う（作り直さない）。リモートに無い、またはすでにマージ済みなら止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-MCK-19 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

## 目的

MCK-19 で作業ブランチに足した3つの規則を、平野さんがチャットで文面を読んで承認したので、cloudflare へマージする。

### 決定（2026-10-17、平野さん）

- MCK-19 の文面（`## 経過`「2. 追記後の全文」）のままマージしてよい。(1) は既存の項に1文を足す形、(2) は既存の項を直す形のままでよい
- chat-side-operations.md が増えた 223 バイト分は、今回は他の文書へ移さない（次のレビュー〈文書の整理〉でまとめて扱う）

### 前提（チャット側。平野さんの決定ではない）

- マージで動く自動処理: docs/notes/ の変更で mj-logs へのガイド文書の写し（guide/）が更新される見込み（要確認。動いたかをログに書く）。ページの再生成は動かない見込み

## 手順

1. work/1017-mck-handoff に origin/cloudflare を取り込み（merge）、`git diff origin/cloudflare...HEAD --stat` と chat-side-operations.md の大きさを測って、冒頭の「マージ:」の条件を満たすか確かめる。満たせば cloudflare へマージ（push）する
2. マージで動いた自動処理（ガイド文書の写しなど）の run と結果をログに書く。CHAT-1017-MCK-19 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1017-MCK-20` を足し、決定を docs/decisions/operations.md に記録する
3. マージ済みを `git merge-base --is-ancestor origin/work/1017-mck-handoff origin/cloudflare` で確かめてから、リモートの work/1017-mck-handoff を削除する（手順は CLAUDE.md「ブランチ運用」のブランチの削除）

## 止まる条件

- 冒頭の「マージ:」の条件を満たさない（差分に3箇所以外の変更がある、上限を超える）
- 解けない衝突がある
- cloudflare への push、またはブランチの削除が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-MCK-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-MCK-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1017-MCK-19 の `## 報告` の状態は「判断待ち」
- Chat-Ref の重複: `CHAT-1017-MCK-20` のコミット 0件
- 作業ブランチ: `work/1017-mck-handoff` はリモートにあり（3a02c685e）、`git merge-base --is-ancestor origin/work/1017-mck-handoff origin/cloudflare` が偽（未マージ）。ローカルも同じ先頭なので、そのまま使った（作り直していない）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行ともある

### 1. origin/cloudflare の取り込み

- `git merge origin/cloudflare` で docs/decisions/operations.md が衝突した（このブランチの MCK-19 の節と、他セッションの CHAT-1017-PAD-09 の節が、同じ末尾に追記されていた。共通の最後の行「- 作業中の回答: なし」を両方が使っていた）。追記どうしなので両方を残して解いた（PAD-09 の節 → MCK-19 の節の順、それぞれに「作業中の回答」の行）。解いた後の該当箇所:

```
## 2026-10-17（CHAT-1017-PAD-09）

CHAT-1017-PAD-08 の「判断が必要なこと」への回答:

- ルールは足さない（…）
- Build history で（…）は見ない（結論が変わらないため）
- 訂正: 振り返り（…）は外れた（PAD-08 の H1）。（…）
- 作業中の回答: なし

## 2026-10-17（CHAT-1017-MCK-19）

- MCK 系列の申送りは、振り返りのレビュー（Fable 5.1）が勧めた3つ（…）で行う
- 作業中の回答: なし
```

- ほかの衝突は無かった

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a9cd733c）: https://github.com/retroeater/mj-logs/tree/main/guide/a9cd733c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9cd733c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9cd733c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9cd733c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9cd733c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9cd733c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9cd733c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
