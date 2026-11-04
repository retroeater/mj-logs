# CHAT-1104-SKN-02

- 着手日時: 2026-11-04
- 対象issue: #641（コメントのみ）
- ブランチ: work/1104-skn
- 着手時HEAD: 1c5333055

## 指示

【Claude作成】Claude Code 向け指示：SKN-01 の続き（pro/ の公開より先に入れる。名前の面の写真はリンクなし） Chat-Ref: CHAT-1104-SKN-02 マージ: 判断待ちで止まる 貼る時機: いつでも（SKN-01 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-skn を続けて使う（SKN-01 のログと決定の記録があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-SKN-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-SKN-02` を足して push する。

目的
CHAT-1104-SKN-01 の「判断が必要なこと」の1つ目・2つ目への回答。SKN-01 の決定1〜4（docs/decisions/saikyo.md に SKN-01 が記録したもの）を、pro/ の公開（work/1101-prp-pub）より先に実装する。
決定（2026-11-04、平野さん）

* SKN を pro/ の公開より先に入れる。今の origin/cloudflare の上で作り、work/1101-prp-pub（`players_data()` の12番目の slug・`renderPlayer()` の写真のリンク、#644）との重なりは、pro/ の公開のマージの指示で解く（TTL と同じやり方）。SKN-01 の止まる条件のうち work/1101-prp-pub との重なりは、この指示では止まる理由にしない
* 個人成績の名前の面の写真はリンクなしにする。X へは決定2のアイコンだけで行く。pro/ の公開の後は、在籍者（slug のある人）の写真だけ `/pro/<slug>/` へのリンクにし、slug の無い人の写真はリンクなしのまま（PRP-20 の「slug が無ければ X」は、公開のマージで重なりを解くときにこの形にする）
* SKN-01 の決定1〜4はそのまま

前提（チャット側。平野さんの決定ではない）

* SKN-01 の「前提」（実物の仕組み・仮置き）はそのまま使う。SKN-01 で中身を読んでいない `vs_records()`・`pair_counts()` の数え方と、卓のページの写真カードに今リンクがあるかを、着手時に読んで確かめる
* 仮置き（プレビューで平野さんが決める）: タブの URL `?view=vs`・対戦回数＝同卓の回数／「3勝1敗2分」（0 の項目も出す）／アイコンを pro/ のアイコンにそろえる／卓のページの写真カードの押す範囲と今のリンクの兼ね合い／タブの行数の上限／年度・対局のページの行の ›

手順

1. SKN-01 の手順1〜3のとおり実装・再生成・プレビューの確かめを行う（work/1101-prp-pub との重なりでは止まらない。ほかの未マージのブランチとの重なりは SKN-01 の止まる条件のまま）。
2. 重なりの一覧（work/1101-prp-pub と同じ関数・同じ行を変えた箇所と、公開のマージで解くときの形〈上の決定の写真のリンク〉）をログに書き、#641 にコメントする（公開のマージの指示で解く重なりとして。本文・状態・ラベルは変えない）。
3. 仮置きを「判断が必要なこと」に並べ、スクリーンショットを docs/logs/CHAT-1104-SKN-02/ に置いて止まる。

止まる条件

* SKN-01 の止まる条件（work/1101-prp-pub との重なりを除く）
* 「対戦成績」タブの勝敗・同卓の数が、対戦成績のページ（`?name=A,B`）の数と1組でも合わない（合わない組を全部書く）
* 再生成の差分に、この変更とその日のシートの変化で説明できないものがある（ページの種類ごとに変わった数をログに書く）
* マージは判断待ち。cloudflare には入れない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1104-skn
- ログ: https://github.com/retroeater/mj/blob/work/1104-skn/docs/logs/CHAT-1104-SKN-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-skn
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: #641
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1a06d89d）: https://github.com/retroeater/mj-logs/tree/main/guide/1a06d89d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1a06d89d.md
