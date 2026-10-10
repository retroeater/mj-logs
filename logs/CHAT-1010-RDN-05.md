# CHAT-1010-RDN-05

- 着手日時: 2026-10-10
- 対象issue: なし
- ブランチ: work/1010-rdn-yama
- 着手時HEAD: 4544bac2

## 指示

【Claude作成】Claude Code 向け指示：RDN-03 の判断を記録し、「連盟プロ以外」への「山口哲也（17期）」の登録を確かめる
Chat-Ref: CHAT-1010-RDN-05
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。判断が残れば状態は判断待ち）
貼る時機: いつでも（CHAT-1010-RDN-04 とは別のブランチで行う）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rdn-yama の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1010-rdn-yama を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rdn-yama origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。未マージの work/1010-rdn（RDN-04）は使わず、触らない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1010-RDN-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1010-RDN-05` を足す（このログと同じコミットでよい）。

目的
CHAT-1010-RDN-03 の「判断が必要なこと」に平野さんが答えたので、決定を記録し、平野さんが行ったシートの登録を生成と同じ経路で確かめる。コード・issue は変えない。
決定（2026-10-10、平野さん）

* `houou_ranking.html`（旧方式）の名前検索が部分一致で「山口哲也」から「山口哲也（17期）」も出す件は、直さない
* 「連盟プロ以外」に「山口哲也（17期）」を登録した（平野さんが登録済み）
* 「鳳凰」タブの旧い山口哲也さんの4行の名前を「山口哲也（17期）」にした（RDN-03 の決定。すでに記録済みなら重ねて書かない）

前提（チャット側。平野さんの決定ではない）

* 登録した行の形は RDN-03 のログの案（所属団体 `-`・所属補足 `元連盟`・X の欄は空）と見込むが、平野さんが実際に入れた値は未確認（要確認）

手順

1. 「連盟プロ以外」を生成と同じ経路（`lib/names.py` の読み方）で読み、「山口哲也」を含む行を全件、`repr()` で書く（名前・所属団体・所属補足・X ID・X画像URL）。`NameBook` を「プロ」「連盟プロ以外」「別名」で組み立て、`resolve("山口哲也（17期）")` が「連盟プロ以外」の人として引けること、`resolve("山口哲也")` が今までどおり現役のプロであること、`NameBook` の警告（所属団体が想定外・「プロ」にもいる、など）にこの名前が出ないことを書く。
2. 上の決定を `docs/decisions/` の該当の分野（鳳凰戦なら `houou.md`。書き方は `docs/decisions/README.md`）に足す。

止まる条件

* 「連盟プロ以外」に「山口哲也（17期）」の行が無い、または2行以上ある（件数と表記を書いて止まる）
* `NameBook` の警告にこの名前が出る、または `resolve()` の結果が上の見込みと違う（内容を書いて止まる）
* 変更が `docs/logs/`・`docs/decisions/` の外に及びそうになった

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。止まる条件に当たらなければ状態は完了
* マージは冒頭の「マージ:」の行のとおり（ログと decisions のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RDN-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RDN-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き
- ブランチ: ローカル work/1010-rdn-yama は origin/cloudflare の祖先（RDN-03 のマージ済み）。`git checkout work/1010-rdn-yama` のうえ `git merge --ff-only origin/cloudflare`（4544bac2）。work/1010-rdn は触っていない
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致。RDN-03 のログの状態に `/ 続き: CHAT-1010-RDN-05` を足した
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

## 報告

- 状態: 作業中
- ブランチ: work/1010-rdn-yama
- ログ: https://github.com/retroeater/mj/blob/work/1010-rdn-yama/docs/logs/CHAT-1010-RDN-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rdn-yama
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cf3cb320）: https://github.com/retroeater/mj-logs/tree/main/guide/cf3cb320

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/647a8db8.md
