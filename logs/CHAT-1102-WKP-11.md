# CHAT-1102-WKP-11

- 着手日時: 2026-11-02
- 対象issue: なし
- ブランチ: work/1102-wkp
- 着手時HEAD: cc8127988

## 指示

【Claude作成】Claude Code 向け指示：CLAUDE.md の「週次の check-meibo.yml」を、run-daily へ移した後の記述に直す Chat-Ref: CHAT-1102-WKP-11 マージ: ドキュメントのみ（CLAUDE.md・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（WKP の同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1102-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1102-WKP-10 の判断待ち（CLAUDE.md の古い記述）への回答。check-meibo は 2026-11-02 に run-weekly から run-daily へ移ったが、CLAUDE.md「判断・作業の原則」に「週次の`check-meibo.yml`も実行する」が残っている。事実の直しだけで、規則は足さない。
決定（2026-11-02、平野さん）

* CLAUDE.md の上の記述を直す（WKP-10 の判断待ちへの回答。チャット側の推奨のとおり）
* convert-x-posts.yml のコメントと work/1028-lgn の run-monthly の説明の古い記述は、LGN のチャットに伝言して 1028-lgn の側で直してもらう（この指示では触らない）

前提（チャット側。平野さんの決定ではない）

* 直す行は WKP-10 のログの `## 報告` の「判断が必要なこと」にあるもの（CLAUDE.md「判断・作業の原則」）。作業ブランチの上で、行の今の文を確かめ直す
* 規則を足す変更ではなく、移した仕組みの記述を今の事実に合わせる直しなので、CLAUDE.md「Chat-Ref」の「知見 → 選んだ層 → 理由」の表は要らない見込み
* 文面は、行の意味（名簿と「プロ」シートの不一致を検知する本がある、という参照）を保って「毎朝の run-daily の`check-meibo.yml`」などに直す。実物に合わせて短くしてよい
* `git grep -n -E '週次の.?check-meibo|check-meibo.{0,20}週' -- CLAUDE.md docs/handover.md docs/notes/chat-side-operations.md docs/instruction-template.md` で、ほかのガイド文書に同じ残りが無いかも見る（WKP-10 で docs/notes は直した）
* CLAUDE.md の上限は 28KB（警告域 26KB）。作業ブランチの上で前後を測る

手順

1. 上の行が作業ブランチの上でもあることを確かめる（無い、または別の内容なら止まる）。未マージの work/ ブランチで CLAUDE.md の同じ行を変えているものが無いことを `git branch -r --no-merged origin/cloudflare` で確かめる
2. 行を直し、上の grep でほかの残りがあれば同じ趣旨の直しとして一緒に直す（直した行をログに書く）。CLAUDE.md のサイズを前後で測る
3. 決定を docs/decisions/automation.md に足し、WKP-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1102-WKP-11` を足す。ドキュメントのみなので完了報告のうえ cloudflare へ入れる

止まる条件

* 上の行が作業ブランチの上で見つからない、または内容が違う
* 直す行が、ほかの記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換えてよい）
* 変更後に CLAUDE.md が警告域（26KB）を超える
* 未マージの work/ ブランチが CLAUDE.md の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-WKP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-WKP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: 0 件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行ともあり
- 作業ブランチ: work/1102-wkp はリモート・ローカルとも origin/cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare`（Already up to date、cc8127988）
- 指示文の issue の state: 着手・コメント・クローズの対象の issue は無い
- 直す行は作業ブランチの上でもあった: CLAUDE.md「判断・作業の原則」の「テストは`scripts/tests/`（unittest、標準ライブラリのみ）。実行は`python3 -m unittest discover -s scripts/tests`。週次の`check-meibo.yml`も実行する。」
  - 前提の補い: この行の意味は「単体テストは check-meibo.yml の中でも回る」（check-meibo の meibo ジョブの setup-python が `test: 'true'`）。前提に書かれた「名簿と「プロ」シートの不一致を検知する本がある、という参照」とは少し違うが、案の文面（毎朝の run-daily の `check-meibo.yml`）はどちらの読みでも合うので、止まらずに直した
- 未マージの work/ ブランチで CLAUDE.md を変えているものは無い
- 直した行: 「週次の`check-meibo.yml`も実行する。」→「毎朝の run-daily の`check-meibo.yml`も実行する。」（コミットはこのログの直前のもの）
- `git grep -n -E '週次の.?check-meibo|check-meibo.{0,20}週' -- CLAUDE.md docs/handover.md docs/notes/chat-side-operations.md docs/instruction-template.md`: 直す前は CLAUDE.md の1件だけ、直した後は0件
- CLAUDE.md のサイズ: 23,967 → 23,981 バイト（警告域 26,624 未満）
- 決定を docs/decisions/automation.md に足し、WKP-10 のログの状態に ` / 続き: CHAT-1102-WKP-11` を足した

## 報告

- 状態: 完了
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1102-WKP-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL: なし
- マージ: 済（ドキュメントのみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f55eb646）: https://github.com/retroeater/mj-logs/tree/main/guide/f55eb646

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f55eb646/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f55eb646/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f55eb646/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f55eb646/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f55eb646/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f55eb646/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc812798.md
