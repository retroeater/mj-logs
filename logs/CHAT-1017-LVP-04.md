# CHAT-1017-LVP-04

- 着手日時: 2026-10-17
- 対象issue: #362
- ブランチ: work/1017-lvp
- 着手時HEAD: 0d31d5ca4

## 指示

【Claude作成】Claude Code 向け指示：【3】手動補正への平野さんの入力（掲載 86 行・まとめ単位 2 行）を確かめる Chat-Ref: CHAT-1017-LVP-04 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（確かめだけの指示。シート・コード・生成物・issue の本文は変えない） 貼る時機: 平野さんが【3】のまとめ単位の2行（3755・4197）を入れ終えた後（任意: LVP のセッションの続きでも新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
/live の正式公開（#362）の前に、平野さんが【3】手動補正に入れた掲載とまとめ単位が正しく入ったか、ほかの行を変えていないかを、生成と同じ経路で読んで確かめる。
決定（2026-10-17、平野さん）

* 【3】の掲載が空欄の行（日本シリーズ・達人戦などの未判断の行と、毎日の追記で増える行）は N で埋めず、空欄のまま残す
* /live の生成は、掲載が空欄の行を「未確認」として数え、警告は「未確認 N 件」の合計の1行だけにする（行ごとの警告は出さない。どの行かはシートを見れば分かるため）。実装は公開の変更の指示で行う（この指示では実装しない）

前提（チャット側。平野さんの決定ではない）

* 平野さんは 2026-10-17 に、動画IDで引き当てる式を使って【3】の「掲載」列に値のみ貼り付けた（入力の中身は下の表）。まとめ単位の2行は手で入れる
* 入れた中身（チャット側が CHAT-1016-LVP-02 のログ `## 経過` の「【3】を埋めるための表」から作った）:
   * 掲載 Y（55行）: 3501, 3507, 3511, 3523, 3532, 3596, 3612, 3629, 3630, 3724, 3751, 3755, 3781, 3793, 3800, 3903, 3920, 3955, 4011, 4026, 4047, 4051, 4076, 4087, 4115, 4162, 4166, 4186, 4192, 4197, 4211, 4212, 4213, 4214, 4215, 4216, 4218, 4306, 4308, 4310, 4311, 4313, 4314, 4315, 4316, 4320, 4324, 4325, 4326, 4327, 4328, 4329, 4331, 4333, 4334
   * 掲載 N（31行）: 1945, 3034, 3049, 3065, 3080, 3516, 3520, 3564, 3582, 3631, 3696, 3725, 3741, 3759, 3780, 3803, 3853, 3857, 3878, 3976, 3988, 4001, 4015, 4031, 4033, 4065, 4099, 4103, 4199, 4317, 4321
   * まとめ単位（2行）: 3755・4197 に「ベスト16C、ベスト16D」
   * 行番号は LVP-02 で読んだ時点のもの。照合は行番号ではなく、LVP-02 の表の行番号に対応する動画IDで行う
* 見込み（LVP-02 で読んだ時点の行の範囲〈LVP-02 の【3】の行数まで〉で数える。それより後に毎日の追記で足された行は別に数える）: 掲載 Y = LVP-01 の「公開」2,918＋55＝2,973、N = 579＋31＝610、空欄 = 836−86＝750
* 4316・4320（第24期プロクイーン決定戦のメンバー限定版）は、LVP-02 の時点では配信予定で公開の条件で落ちていた（要確認。今は配信が始まっていることがある）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。LVP-01 と同じ読み方で【2】【3】を読み、行数をログに書く
2. 上の 86 行と 2 行を動画IDで照合し、掲載・まとめ単位が上の表のとおりかを1行ずつ確かめる。LVP-02 の範囲の行の掲載の件数（Y・N・除外・空欄）を数えて見込みと比べ、範囲の外（後で足された行）の件数も書く。掲載・まとめ単位のほかの補正の列が LVP-02 の時点から変わった行があれば、その行を書く（LVP-02 の時点の値が分からない列は「比べられない」と書く）
3. `python3 scripts/regenerate.py --check live_pages` を回し（作業ツリーは戻る）、Y にした 55 行のうち公開の条件で落ちる行を理由つきで書く。3755・4197 がベスト16C・ベスト16D の両方のページに出ることを確かめる。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 【2】【3】の読み込みが失敗する、または読み直すたびに行数が変わる（件数を書いて止まる）
* 86 行のうち、動画ID が【3】に見つからない行がある（その行を書いて止まる）
* #362 に他セッションの着手中コメントがある
* 掲載・まとめ単位が表と違う行や、件数の見込みとのずれがあっても止まらない（ずれた行をすべて書いて報告する。直すのは平野さん）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、表と違った行・見込みとのずれ・Y でも落ちる行（理由つき）を書く。無ければ「なし」
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-LVP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-LVP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1017-LVP-04` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1017-lvp はローカル・リモートともあり、どちらも origin/cloudflare の祖先（LVP-03 でマージ済み）。`git merge --ff-only origin/cloudflare` で 0d31d5ca4 まで進めた

## 報告

- 状態: 作業中
- ブランチ: work/1017-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1017-lvp/docs/logs/CHAT-1017-LVP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f80880a6）: https://github.com/retroeater/mj-logs/tree/main/guide/f80880a6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f80880a6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f80880a6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f80880a6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f80880a6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f80880a6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f80880a6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
