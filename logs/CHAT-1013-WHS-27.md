# CHAT-1013-WHS-27

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-whs-rules
- 着手時HEAD: b53374d9

## 指示

【Claude作成】Claude Code 向け指示：ドキュメントのみ — 帰り道の作業（WHS-15〜26）の振り返りから、チャット側の規則4つを docs/notes/chat-side-operations.md に足す。警告域（26KB）を超えないよう、シートの用意に関する長い項目を docs/notes/static-generation.md へ移す
Chat-Ref: CHAT-1013-WHS-27
マージ: ドキュメントのみ（docs/notes・docs/logs・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1013-WHS-26 のマージの後。2026-10-13 にマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1013-whs-rules を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-whs-rules origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

2026-10-12〜13 の帰り道の作業（CHAT-1012-WHS-15〜CHAT-1013-WHS-26）で、チャット側が誤ったことが4つあった。同じ誤りを繰り返さないための規則を `docs/notes/chat-side-operations.md` に足す。この指示では規則の文面を変えず（誤字・体裁の直しは可）、置き場所だけを手順のとおりにする。

### 決定（2026-10-13、平野さん）

- 下の4つの規則を `docs/notes/chat-side-operations.md` に足す（文面はチャット側の振り返りのレビューの結論。平野さんは「おすすめで進める」と判断した）

### 前提（チャット側。平野さんの決定ではない）

- 誤りと規則の対応（ログの `## 経過` に、この対応を書き写す）:
  1. CHAT-1012-WHS-13 で、`regenerate-page.yml` が `update-sns-book` から呼ばれる経路を見落とし、帰り道の手順がそこでも動いていた（WHS-15 で直した） → 規則 A
  2. 選択肢の説明に、平野さんが言っていない仕様（自動の紹介文をシートに書く）を足し、10/10 の決定（/live のシートをサービスアカウントに共有しない）と食い違った（WHS-16 で止まり、WHS-17 で直した） → 規則 B
  3. `curl` が通らないだけで「この環境から ryoei.pro に接続できない」と平野さんに伝えた。WebFetch と Chrome では読めた → 規則 C
  4. 86979e3d の check-run の failure（`mj-scheduler` のもの）を本番ビルドの失敗と読み違え、誤った警告を出した → 規則 D
- 足す規則の文面と置き場所（文面はこのまま。置き場所の見出し名は 7557aba1 時点のもの）:
  - **規則 A**（「### 書く前に実物で確かめる」の「場面ごとに次も確かめる」の箇条書きに1項目足す）:
    `- ワークフローを変える指示: そのワークフローを呼ぶ側（`uses:` で呼ぶワークフロー・schedule・`workflow_dispatch`）を `git grep` で洗って「前提」に列挙し、呼ばれ方ごとの振る舞いを「決定」に入れる（呼び出し元の見落としは、呼ばれた側で初めて出る）`
  - **規則 B**（「### 平野さんの判断とマージの許可」の箇条書きに1項目足す。「平野さんの決定は、ログの末尾のリンク…」の項目の直後）:
    `- **平野さんに選択肢を出す前に docs/decisions/ の分野のファイルを読み、選択肢の説明に平野さんが言っていない仕様を足さない。** 足すなら「チャット側の案」と分けて書く（選択肢に混ぜると、選んだだけで決定として記録される）`
  - **規則 C**（「## 読み方」の表の「本番の見え方」の行の末尾に足す）:
    `チャット側から本番を読む手段は WebFetch と Chrome（`curl` は通らない）。**「届かない・読めない」と書く前に両方を試す**`
  - **規則 D**（「## 読み方」の表に行を1つ足す。「Actions の実行結果」の行の直後）:
    `| Workers Builds の成否 | check-run（`gh api repos/retroeater/mj/commits/<sha>/check-runs`）。1つのコミットに Worker ごと（`mj`・`mj-ask`・`mj-scheduler`）と本番・プレビューの別で複数付く。**失敗を平野さんに伝える前に、失敗した check-run の Worker 名・Build ID・本番かプレビューかを書く**（見分け方は `docs/notes/cloudflare.md`「ビルドが Cloning で失敗したとき」） |`
- 大きさ: 7557aba1 時点の `docs/notes/chat-side-operations.md` は 25,669 バイト。上限 28KB（28,672）・警告域 26KB（26,624）（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」、`assets-check.yml`）。4つを足すと警告域に入る見込みなので、同ルール「警告域に近づいたら、足す前に `docs/notes/` へ移す」に従い、次を移す:
  - 「### 書く前に実物で確かめる」の「平野さんがシート（タブ）を用意した・直したと言ったとき: …」の項目（2段落、「消す・直すを頼むときは…止まる）」まで）を、`docs/notes/static-generation.md`「生成を止める条件の設計（#222 の実例）」の末尾に小見出し「#### 平野さんがシートを用意した・直したと言ったときのチャット側の指示」として、文面を変えずに移す
  - 元の場所には1行だけ残す: `- 平野さんがシート（タブ）を用意した・直したと言ったとき: `docs/notes/static-generation.md`「生成を止める条件の設計」の末尾の手順に従う（生成と同じ経路で読ませてから指示を書く）`
- 既存の記述との関係: 規則 D の見分け方は `docs/notes/cloudflare.md`「ビルドが Cloning で失敗したとき（2026-10-13）」に Code 側が書いており、chat-side-operations.md には参照だけを置く（同じ内容を2箇所に書かない）。規則 A は既存の「変える関数・定数の呼び出し元〈別のページの生成を含む〉」（複数のチャットが同じ仕組みを変えるときの項目）と趣旨が近いが、場面（ワークフロー）が違うので別項目にする

## 手順

1. 確かめる: `docs/notes/chat-side-operations.md` と `docs/notes/static-generation.md` の大きさ（`wc -c`）と、「前提」の見出し名・移す項目の文面が実物にあることを確かめる。食い違えば（見出しが改名されている、項目が既に移されている等）止まる。未マージの `work/` ブランチが同じ2ファイルを変えていれば止まる
2. 直す: 「前提」のとおりに、移す項目を `static-generation.md` へ移して元に1行残し、規則 A〜D を足す。`wc -c` で両ファイルの大きさを測り、ログに前後の数を書く。`chat-side-operations.md` が 26,624 バイト以上なら止まる（足した規則は戻さず、その状態でログに書いて止まる）。`python3 -m unittest discover -s scripts/tests` が通ること（文書だけの変更なので変わらない見込み）
3. 記録してマージする: 決定を `docs/decisions/` の合う分野（README に従う。運用の分野が無ければ README のとおり選ぶ）に1行足す。ドキュメントのみなので完了報告のうえ cloudflare へ入れる。作業ブランチを片付ける

## 止まる条件

* 「前提」の見出し名・移す項目の文面が実物と食い違う。未マージのブランチが同じファイルを変えている
* 直した後の `docs/notes/chat-side-operations.md` が 26,624 バイト以上
* 取り込みで文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（両ファイルの前後のバイト数を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-WHS-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-WHS-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1013-WHS-27"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- 作業ブランチ: `work/1013-whs-rules` はローカル・リモートとも無し → `git checkout -b work/1013-whs-rules origin/cloudflare`（b53374d9）

### 誤りと規則の対応（指示文の前提から写した）

1. CHAT-1012-WHS-13 で、`regenerate-page.yml` が `update-sns-book` から呼ばれる経路を見落とし、帰り道の手順がそこでも動いていた（WHS-15 で直した） → 規則 A
2. 選択肢の説明に、平野さんが言っていない仕様（自動の紹介文をシートに書く）を足し、10/10 の決定（/live のシートをサービスアカウントに共有しない）と食い違った（WHS-16 で止まり、WHS-17 で直した） → 規則 B
3. `curl` が通らないだけで「この環境から ryoei.pro に接続できない」と平野さんに伝えた。WebFetch と Chrome では読めた → 規則 C
4. 86979e3d の check-run の failure（`mj-scheduler` のもの）を本番ビルドの失敗と読み違え、誤った警告を出した → 規則 D

### 手順1 確かめ

- 大きさ（b53374d9）: `chat-side-operations.md` 25,669 バイト、`static-generation.md` 83,445 バイト（前提どおり）
- 見出し・項目: 「### 書く前に実物で確かめる」の「場面ごとに次も確かめる」、その中の「平野さんがシート（タブ）を用意した・直したと言ったとき: …（消す・直すを頼むときは…止まる）」の2段落、「### 平野さんの判断とマージの許可」の「平野さんの決定は、ログの末尾のリンク…」、「## 読み方」の表の「本番の見え方」「Actions の実行結果」の行、`static-generation.md`「### 生成を止める条件の設計（#222 の実例）」、`cloudflare.md`「#### ビルドが Cloning で失敗したとき（2026-10-13）」。すべて実物にある
- 未マージのブランチ（`work/1009-swp-526`・`1011-swp-hnd`・`1011-swp-nav`・`1013-dsn`）は2ファイルに差分なし

### 手順2 直す

- 移した項目は `static-generation.md`「生成を止める条件の設計（#222 の実例）」の末尾に「#### 平野さんがシートを用意した・直したと言ったときのチャット側の指示」として置いた（文面は変えず、入れ子の箇条書きの字下げだけを1段浅くした。移した元の注記を1行添えた）。元の場所には指示文の1行を残した
- 規則 A は「場面ごとに次も確かめる」の残した1行の直後、規則 B は「平野さんの決定は、ログの末尾のリンク…」の直後、規則 C は「本番の見え方」の行の末尾、規則 D は「Actions の実行結果」の行の直後に足した（文面は指示文のまま）
- 大きさ: `chat-side-operations.md` 25,669 → **26,419 バイト**（警告域 26,624 の手前）、`static-generation.md` 83,445 → 84,437 バイト（上限なし）
- `python3 -m unittest discover -s scripts/tests`: 995件 OK
- 決定を `docs/decisions/operations.md`（運用、チャット側の書き方）に足した


## 報告

- 状態: 完了
- ブランチ: work/1013-whs-rules
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-WHS-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-whs-rules
- 確認用URL: なし
- マージ: 済（ドキュメントのみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9c18087e）: https://github.com/retroeater/mj-logs/tree/main/guide/9c18087e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0186d17f.md
