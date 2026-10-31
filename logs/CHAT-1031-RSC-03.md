# CHAT-1031-RSC-03

- 着手日時: 2026-10-31
- 対象issue: #628
- ブランチ: work/1031-rsc
- 着手時HEAD: d21a95699

## 指示

【Claude作成】Claude Code 向け指示：調べものの照合の条件を直し（抜粋だけの候補を「未照合」で書く・ryoei.pro を除く）、名前かな（#628）を Haiku 5.5 で全員分回す Chat-Ref: CHAT-1031-RSC-03 マージ: 承認済み（チャットで）。止まる条件を1つでも満たしたらマージせずに止まる 貼る時機: いつでも（CHAT-1031-RSC-02 と同じセッションでよい） 作業ブランチ: クラウドセッションで実行する。work/1031-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1031-RSC-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1031-RSC-03` を足す。

目的
CHAT-1031-RSC-02 の「判断が必要なこと」への回答を入れ、名前かな（A、#628）を対象の全員について Haiku 5.5 で回して終える。
決定（2026-10-31、平野さん。RSC-02 の判断待ち 1〜6 への回答）

* 1: 残り（A の残りと B・C）のモデルは Haiku 5.5。B は性質が違うので、B の最初の数件だけ Haiku 5.5 と Sonnet 5.5 で比べる（B の指示で行う）
* 2・5: 検索結果の抜粋にだけある候補も、確度「未照合」で候補タブに書く（出典 URL は検索結果にあったもの、引用文はモデルが示した抜粋。コードでは本文を確かめられないことを備考に書く）。取得した本文で確かめられた候補は今どおり「照合済み」
* 2: ryoei.pro（www. を含む）を Web 検索・取得の対象から外す（`blocked_domains`）
* 3: A は対象の全員を Haiku 5.5 で1回回して終える。RSC-02 で「見つからない」だった5人も、新しい条件で調べ直す
* 4: 「小川光」は平野さんが「連盟プロ以外」に入れる。入っていればこの実行の対象に入る（コードは変えない）
* 6: R_候補 の読み取りの確かめがマージの後になったことは了承（新しいワークフローはこの順でよい）
* この指示のマージと、マージの後の A の実行（Haiku 5.5、1回の上限 $2、R_候補 に書く）を承認する

前提（チャット側。平野さんの決定ではない）

* 調べ直す5人の行は候補タブに「見つからない」で残っている。調べ直しの方法（`recheck` の広げ方、または古い行を残して新しい行を足す）は実物に合わせて選び、ログに書く。候補タブの既存の行は書き換えない（追記だけ、RSC-01 の設計）
* 抜粋の候補でも、名前の完全一致と、候補の読みが抜粋の文に含まれること（モデルが示した文の中で）は確かめる
* 費用の見込み: 1人約 $0.03（RSC-02 の Haiku の実績）×約20〜21名で約 $0.6

手順

1. #628 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）。未マージの work/ ブランチが調べもののスクリプト・ワークフローを変えていないか確かめる
2. 照合の条件と `blocked_domains` を直し、テストを足す。docs（仕組みの文書・決定）を直す。自己レビュー（docs/notes/self-review.md）の後にマージする
3. マージの後、cloudflare から `dry_run` で対象（名前の一覧、小川光が入ったか）を確かめ、Haiku 5.5・`max_cost` 2 で本実行する。ログに全員の表（候補・確度・出典 URL・引用文の要約・費用）と実額を書き、#628 に要約をコメントする（ログのどの表から引用するかを書く）。「未照合」「照合済み」の候補は平野さんが出典を開いて確かめる旨を添える

止まる条件

* #628 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: 調べもののスクリプト・テスト・ワークフロー、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
* テストが通らない（今回の変更による失敗は直してよい。無関係な失敗は原因を書いて進めてよい）
* 未マージの work/ ブランチが調べもののスクリプト・ワークフローの同じ関数を変えている、または取り込みで衝突する
* 本実行の実額が $2 を超えた・API が上限や権限のエラーを返した（もう1回は試さず、エラーをそのまま書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。次の B（#630）の前に平野さんの判断が要るもの（B の対象の単位、資料のサイトの一覧〈RSC-02 で読めた mahjong-saikyosen.com・kinmaweb.jp・times.abema.tv〉、最初に比べる件数）を「判断が必要なこと」に、選択肢・メリット・デメリット・推奨つきで書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-RSC-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-RSC-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし。`RSC` は続き（03）。作業ブランチ: ローカル・リモートの work/1031-rsc とも origin/cloudflare の祖先（マージ済み）なので、`git merge --ff-only origin/cloudflare` で進めた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1031-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1031-rsc/docs/logs/CHAT-1031-RSC-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1031-rsc
- 確認用URL: なし
- マージ: 未
- issue: #628
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 55a36fe9）: https://github.com/retroeater/mj-logs/tree/main/guide/55a36fe9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/55a36fe9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
