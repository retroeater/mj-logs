# CHAT-1102-WKP-14

- 着手日時: 2026-11-02
- 対象issue: なし
- ブランチ: work/1102-wkp
- 着手時HEAD: c3c97901e

## 指示

【Claude作成】Claude Code 向け指示：WKP-13 の残り2つ（scheduler-worker.md の起動の表の run-daily の説明、check_wayhome.py の issue の本文の雛形の時刻）を直す Chat-Ref: CHAT-1102-WKP-14 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおりで、1つでも当たればマージせずに止まる 貼る時機: いつでも（WKP の同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1102-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1102-WKP-13 の判断待ちへの回答。WKP-13 で「週次」の文言の外として残した2か所を、今の起動に合わせて直す。文言だけの変更で、動きは変えない。
決定（2026-11-02、平野さん）

* WKP-13 の判断待ちの2か所を直す
* マージは、単体テストがすべて通り、差分がその2か所の文言（とテストの期待値）だけであれば行ってよい

前提（チャット側。平野さんの決定ではない）

* 直す2か所（WKP-13 のログの `## 報告` の「判断が必要なこと」から）:
   * docs/notes/scheduler-worker.md「起動の表の直し方」の今の表の run-daily の説明に、2026-11-03 から run-daily にある5本（check-meibo・sync-birthday-calendar・check-saikyo-unregistered・fetch-x-posts → convert-x-posts）が無い
   * scripts/check_wayhome.py の `ISSUE_BODY`（36行あたり）の「`.github/workflows/fetch-youtube.yml` の週次の実行（毎週月曜 05:37 JST の予定。手動実行も同じ）」。チャット側が cloudflare の今の版を Chrome で読んだ。`ISSUE_BODY` は issue「帰り道の新しい回」が無いときに作る本文（`<out-dir>/issue.md`）で、今ある issue の本文は書き換えない見込み（要確認。書き換える経路があれば、そのことをログに書く）。今の起動は run-weekly（月曜 03:00 JST）から呼ぶ fetch-youtube
* 文面は実物に合わせて決めてよい（例: 「`run-weekly.yml`（毎週月曜 03:00 JST）から呼ぶ `fetch-youtube.yml` の実行（手動実行も同じ）」）。`ISSUE_BODY` の文を期待値に持つテストがあれば合わせる
* WKP-13 のマージの後に push で起動したページの再生成（run 41367390119）は success（mj-logs の actions/status.md、09:47 JST の書き出し）。この指示を書いた時点で、mj-logs の actions/morning.md の1節に cloudflare の失敗は無い（06:24 JST の書き出しの時点）
* 未マージの work/1101-prp-pub が scripts/generate_jpml_pros.py を変えている（WKP-13 の着手時の確かめ）。今回は触らない

手順

1. 上の2か所が作業ブランチの上でもあることを確かめる（無い、または別の内容なら止まる）。未マージの work/ ブランチで同じ行を変えているものが無いことを `git branch -r --no-merged origin/cloudflare` で確かめる。`ISSUE_BODY` が今ある issue の本文を書き換えるかをコードで確かめる
2. 2か所を直す。`python3 -m unittest discover -s scripts/tests` を通す
3. 決定を docs/decisions/automation.md に足し、WKP-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1102-WKP-14` を足す。止まる条件に当たらなければ cloudflare へマージする

止まる条件

* 上の2か所が作業ブランチの上で見つからない、または内容が違う
* 単体テストが通らない（自分の変更で落ちると分かっているテストの期待値は直してよい）
* マージの差分に、2か所の文言とテストの期待値・docs/decisions・docs/logs のほか（動きの変わる行・生成物）が出る
* 未マージの work/ ブランチが同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージの後の check-run の失敗は、今回の変更によるものなら止まり、無関係なら原因をログに書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-WKP-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-WKP-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: 0 件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行ともあり
- 作業ブランチ: work/1102-wkp はリモート・ローカルとも origin/cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare` で c3c97901e に進めた
- 指示文の issue の state: 着手・コメント・クローズの対象の issue は無い
- 2か所は作業ブランチの上でもあった:
  - docs/notes/scheduler-worker.md 72行（「起動の表の直し方」の今の表）の run-daily の説明「update-live-channel・update-sns-book・check-image-links・regenerate-saikyo を順に、sync-dojo-calendar・cleanup-branches を並行に呼ぶ」（移した5本が無い）
  - scripts/check_wayhome.py 36行（`ISSUE_BODY`）の「`.github/workflows/fetch-youtube.yml` の週次の実行（毎週月曜 05:37 JST の予定。手動実行も同じ）」
- 未マージの work/ ブランチで2つのファイルを変えているものは無い
- `ISSUE_BODY` の使われ方: `scripts/check_wayhome.py` 125行が `<out-dir>/issue.md` に書き出し、`regenerate-page.yml` の「帰り道の知らせを書く」が、issue の番号が無いとき（`if (!number)`、issue「帰り道の新しい回」が無いとき）だけ `issues.create` の `body` に使う。今ある issue の本文を書き換える経路は無い（前提のとおり）
- 直したこと:
  - scheduler-worker.md: 「sync-dojo-calendar・cleanup-branches と、2026-11-03 から check-meibo・sync-birthday-calendar・check-saikyo-unregistered・fetch-x-posts → convert-x-posts〈run-weekly から移した〉を並行に呼ぶ」にした（b6d4cdbfa）
  - check_wayhome.py: 「`.github/workflows/fetch-youtube.yml` の実行（`run-weekly.yml` から毎週月曜 03:00 JST。fetch-youtube.yml の手動実行も同じ）が」にした（b6d4cdbfa、自己レビューの指摘で並びを直した b210092a2）
  - `ISSUE_BODY` の文を期待値に持つテストは無い
- `python3 -m unittest discover -s scripts/tests` 成功（1,739 件）
- 決定を docs/decisions/automation.md に足し、WKP-13 のログの状態に ` / 続き: CHAT-1102-WKP-14` を足した

### 自己レビュー（docs/notes/self-review.md、1回）

| 指摘 | 扱い |
|---|---|
| 変わったのは2行の文言だけで、事実（run-daily.yml・run-weekly.yml・schedule.json）と合う。`ISSUE_BODY` は issue を作るときだけ使う | 意図どおり |
| check_wayhome.py の「（手動実行も同じ）」が run-weekly の手動実行（youtube を飛ばす）にも読める | 直した（b210092a2） |
| docs/notes/github-actions.md 54行の「`run-weekly.yml`（02:00 JST）」が古い（今は 03:00。WKP-10 で入れた記述の直し漏れ） | 見送り（この指示のマージの差分は2か所に限られる。判断が必要なことに書いた） |
| docs/notes/books-freeze.md 43行の「毎週月曜05:37 JST」 | 意図どおり（凍結の経緯の記録） |
| 知らせが出る起動の列挙に regenerate-page.yml を直接 all で手動実行した場合が無い（直す前から同じ） | 見送り（今回の変更で生まれたものではない） |

## 報告

- 状態: 判断待ち
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1102-WKP-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL: なし（配信物は変えていない）
- マージ: 済（SHA は cloudflare の先頭〈このログを含むコミット〉）
- issue: なし
- 判断が必要なこと:
  - docs/notes/github-actions.md 54行（regenerate-page の説明）の「毎週月曜の`all`は`run-weekly.yml`（02:00 JST）の`fetch-youtube.yml`が」が古い（今は 03:00）。WKP-10 で入れた記述の直し漏れ。この指示のマージの差分は2か所に限られるので直していない
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c9e94ae8）: https://github.com/retroeater/mj-logs/tree/main/guide/c9e94ae8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c9e94ae8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c9e94ae8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c9e94ae8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c9e94ae8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c9e94ae8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c9e94ae8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c3c97901.md
