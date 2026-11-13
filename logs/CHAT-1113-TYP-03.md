# CHAT-1113-TYP-03

- 着手日時: 2026-11-13
- 対象issue: #682
- ブランチ: work/1113-typ
- 着手時HEAD: 46a658d

## 指示

【Claude作成】Claude Code 向け指示：文字の点検の指摘（#682、102件）を仕分けし、シートごとの直しの一覧を #682 に書く。コードの不具合は直す Chat-Ref: CHAT-1113-TYP-03 マージ: 承認済み（チャットで。コードの不具合の直しは、下の「止まる条件」の範囲に収まるときに限る） 貼る時機: いつでも（TYP-02 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1113-typ を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-typ origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-TYP-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-TYP-03` を足す。

目的
CHAT-1113-TYP-02 の判断待ちへの回答（案1）を入れ、#682 の指摘 102件を、平野さんが場所の分かった直しだけをすればよい形に仕分ける。
決定（2026-11-13、平野さん。TYP-02 の判断待ちへの回答）

* 案1: Claude Code が先に仕分けをする（シートは読むだけ）。指摘ごとに、シートのどのタブ・どの行か、コードの不具合かを確かめ、シートごとの直しの一覧（タブ・行・今の値・直す案）を #682 に書く。コードの不具合（No.29・No.95、まとめ D がコードなら D）は Claude Code が直す
* 平野さんはシートの一覧に沿って手で直し、公式の名前・記録の確認が要るもの（まとめ A・C、No.82 など）を決める。シートは Claude Code が書かない（TYP の決定のまま）
* この指示のマージ（コードの直しと docs）を承認する

前提（チャット側。平野さんの決定ではない）

* 費用: Claude API は使わない（シートを読むだけ）
* シートを読む手段は実物に合わせて選ぶ（セッションには鍵が無い〈docs/notes/cloud-sessions.md「鍵」〉。既存のワークフローの dry-run・生成の手元の再現など）。新しいワークフローを足すなら一度きりにし、使い終えたら同じ指示で外す（TYP-01・02 と同じ扱い）。シートの値はログ・issue に、公開ページに出ている範囲だけ書く（ブックの ID・公開していない列は書かない）
* 指摘の仕分けの分類の案: (1) シートの直し（タブ・行・今の値・直す案） (2) コードの不具合 (3) 誤った指摘（理由） (4) 平野さんの判断が要る（公式の確認・表記の方針。何を決めるかを1行で）。同じ原因の指摘はまとめてよい（まとめ A〜D の形）
* 「別名」で直すべき表記の違い（改名・同一人物の書き方の違い）が出たら、(1) ではなく「別名」に足す行の案として別にまとめる（RSC-17 で同じ扱いを進めている。RSC の系列と重ならないよう、行の案を書くだけにする）
* コードの直しは、指摘の箇所だけを直す（見た目・レイアウトは変えない）。直した後の再生成で変わるページの範囲（無関係なページ・シート側の変更の反映を含む）を見込んで書く

手順

1. #682 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）。未マージの work/ ブランチが直す予定のコードの同じ関数を変えていないか確かめる
2. 102件を上の (1)〜(4) に仕分けし、全件の表（No.・分類・場所〈シートのタブ・行／ファイル・関数〉・今の値・直す案・理由）をログに書く。#682 には、シートごと・タブごとの直しの一覧（(1)）と「別名」の行の案、(4) の一覧、(3) の件数と主な理由を書く（長ければ複数のコメントに分けてよい）
3. (2) のコードの不具合を直し、試験を足し、自己レビュー（docs/notes/self-review.md）の後にマージする。マージの後の再生成で直ったことを公開のページで確かめ、ページの URL をログに書く。「判断が必要なこと」に (4) の一覧を書く。#682 は、平野さんのシートの直しが済むまで Open のままにする

止まる条件

* #682 が Closed、または他のセッションの着手中のコメントがある
* コードの直しが、指摘の箇所の外の見た目・レイアウトを変える、または共有の部品（lib/ の関数）の振る舞いを変えて指摘と無関係なページの中身が変わる（直しをマージせずに、差分と理由を書いて止まる）
* (2) と見立てた不具合が、シートの直しで解決する（コードの直しは要らない）と分かった（(1) に移して続ける。止まらない）
* 差分が次の範囲を超える: 指摘の箇所のコード・試験、一度きりのワークフロー（足したなら外すまで）、docs（notes・decisions・ログ）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-TYP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-TYP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-11-13）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし
- 同じ Chat-Ref のコミット: 0件。作業ブランチ: origin/work/1113-typ は origin/cloudflare の祖先（マージ済み）。ローカルの work/1113-typ も祖先なので、`git checkout work/1113-typ` のうえ `git merge --ff-only origin/cloudflare`（46a658d）
- TYP-02 のログの状態を「判断待ち / 続き: CHAT-1113-TYP-03」にした
- #682: Open、コメント 0件（ほかのセッションの着手中なし）。着手中をコメントした（https://github.com/retroeater/mj/issues/682#issuecomment-6843640623 ）
- 直す予定のコード（`generate_live_pages.build_individual`）を変えている未マージの work/ ブランチは無し

### 仕分けの調べ方

- ページの文字は手元の HTML（cloudflare の生成物）から読んだ
- /live の動画の日付は YouTube の公開日（ライブは配信開始日）で、シートの値ではない（`generate_live_pages.Video.sort_date`）。日付の並びの指摘は、動画の題名（YouTube の oEmbed で引いた）と回戦・卓の割り当てを比べて仕分けた
- シートの行番号は、一度きりのワークフロー `oneoff-sheet-lookup.yml`（`scripts/oneoff_sheet_lookup.py`、読むだけ）で引く。新しいワークフローは既定ブランチに無いと起動できないので、cloudflare に入れて実行し、使い終えたら同じ指示で外す

### コードの直し（No.29）と自己レビュー

- `generate_live_pages.build_individual`: 対局者が空のページで、説明文の「対局者: 。」と見出しだけの「対局者」の節を出さない（試験 `EmptyPlayersTest`。修正前のコードでは `test_no_players` が FAIL）
- `regenerate-page.yml` を作業ブランチで dry_run・compare_ref（cloudflare 589c080）で実行（run 43364782962、success）: コードの違いで中身が変わるのは `live/wrc/4/f.html`（10行）の1件だけ。最初の実行（compare_ref に `cloudflare` と書いた run 43364600528）は「fatal: invalid reference: cloudflare」で失敗した（ワークフローの中では SHA で渡す）
- 自己レビュー1回目（新しい文脈のサブエージェント）: 止めるほどの指摘なし。低2件

| 指摘 | 扱い |
|---|---|
| `EmptyPlayersTest` が `unittest.main()` の後ろにあり、直接実行で回らない（低） | 直した（e797709） |
| 良栄の引き当てで「表示」を出していない（サイトに出ていない行を見分けられない）（低） | 直した（e797709。ログ・issue には表示=Y の行だけを写す） |

- 2回目（e797709 の差分だけ）: 指摘なし

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d1a3ab3c）: https://github.com/retroeater/mj-logs/tree/main/guide/d1a3ab3c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d1a3ab3c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d1a3ab3c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d1a3ab3c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d1a3ab3c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d1a3ab3c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d1a3ab3c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
