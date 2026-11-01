# CHAT-1102-LVP-08

- 着手日時: 2026-11-01（指示の発行日は 11-02）
- 対象issue: #362・#490
- ブランチ: work/1017-lvp
- 着手時HEAD: 8a63c670a

## 指示

【Claude作成】Claude Code 向け指示：live/ の作り直し（LVP-06）に cloudflare を取り込み、見出しの重なりを直して、未公開のまま cloudflare へマージする Chat-Ref: CHAT-1102-LVP-08 マージ: 承認済み（チャットで） 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-lvp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1017-lvp を続けて使う（CHAT-1017-LVP-06 の作り直しがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。origin/work/1017-lvp の先頭が 8a63c670a（CHAT-1017-LVP-06 の報告のコミット）でなければ、増えたコミットをログに書いて止まる。

目的
CHAT-1017-LVP-06 の作り直し（判断待ち）を、プレビューの確認の結果に合わせて直し、cloudflare に入れる。live/ は noindex のまま、navbar・サイトマップにも出さない（公開は次の指示）。あわせて、平野さんが【3】に入れた3大会（達人戦・昇龍戦・鳳匠戦）の掲載を、生成と同じ経路で確かめる。
決定（2026-11-02、平野さん）

* CHAT-1017-LVP-06 の仮置き 1〜11 は、すべて仮置きした形のまま採る
* 期のページで、ステージに表が1つしかないときは、行の題からステージ名を省き、ステージの見出しだけを残す（例: `/live/judan/43.html` の「初段戦Select」で、見出しと行の題が同じ語で続けて出ている）
* 直しと確かめが通れば、work/1017-lvp を cloudflare にマージしてよい（live/ は未公開のまま）

前提（チャット側。平野さんの決定ではない）

* 2026-11-02 にチャット側が `git merge-tree` で見た、取り込みで衝突するファイル: 生成されたページ（live/ の 922 ファイル）、`docs/decisions/live.md`、`docs/decisions/design-renewal.md`、`scripts/lib/study.py`、`scripts/tests/test_study.py`（要確認。navbar.js は衝突しない。OUK・ほかの系列がナビに足した項目は取り込みで入る）
* study の2ファイルの衝突は、cloudflare 側が VideoObject を外した変更（CHAT-1024-VOD-03、#629・#620）と、work 側がメンバー限定の札を `lib/members.py` の共通の部品にした変更（LVP-06）の重なり（要確認）
* 平野さんは 2026-10-31 に【3】へ3大会の掲載を入れた。中身: TSV の86行（Y 12行・N 74行。行の一覧は #490 の 2026-11-01 のコメントにある元のログ〈a3e6ae1ba の docs/logs/CHAT-1017-LVP-07.md〉の「手順2」の表の Y 案・N 案から、プレーオフの6行と行4232・4244 を除いたもの）。さらに行4232 は N（冒頭のみ無料の公開版のため）、行4244 は Y でまとめ単位「ベスト8、決勝」、行4247 はまとめ単位「ベスト8、決勝」。プレーオフの6行は空欄のまま（載せることは決まっているが、ステージの記号とまとめ単位は公開の指示で入れる）。行番号は 2026-10-17 の時点のもの。照合は動画ID で行う
* LVP-07 の模擬では、Y 案の12行を Y にしても落ちる行・作られないページは0、個別ページは +9 だった。行4244 は模擬に入っていない

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。CHAT-1017-LVP-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1102-LVP-08` を足す。origin/cloudflare を merge で取り込む。衝突は次のとおり解き、解いた後の該当箇所をログに引用する:
   * 生成されたページ: CLAUDE.md「ブランチ運用」のとおり、取り込んだ後のスクリプトで生成し直して解く
   * `docs/decisions/live.md`・`docs/decisions/design-renewal.md`: 追記どうしなら両方を残す（日付の順）
   * `scripts/lib/study.py`・`scripts/tests/test_study.py`: cloudflare 側の VideoObject を外した変更（`build_video_object()` の削除と、VideoObject が無いことのテスト）を残し、work 側の札の共通化（`members.badge_html()`、テストが見る札のクラス `mj-members`）を入れる
   * 上のほかの衝突、または上の形で両立しない衝突は、解かずに止まる
2. 上の「決定」の見出しの重なりを直す。`python3 scripts/regenerate.py --check live_pages` を回し、止まる理由と警告を書く。3大会の入力を動画ID で照合する（上の「前提」の中身のとおりか、1行ずつ）。3大会で増えた個別ページを、URL の一覧で全件書く。行4244・4247 で作られるページと、URL の重なりが無いかも書く。全ページを再生成し、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`（workers に変更があるときだけ）を回す。live/ 以外の生成物の差分を書く（study/ の札のクラスの変化など、共通化と取り込みで説明できるものだけであること）。study/ のメンバー限定の回は、375px の明・暗で、札の見え方が cloudflare の今の版と変わらないことを画面で確かめる
3. 作業ブランチに push し、プレビューで次を確かめる（確認用の URL は最終報告にだけ書く）。`/live/judan/43.html`（見出しの重なりが消えたこと）、白地のページと個別ページの共通ナビ（取り込みで入った項目が並び、「番組」に印があること）、3大会のタイトル戦のページ1つ。上の「決定」を docs/decisions/live.md に足す。止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。マージ後、Workers Builds の check-run の結果と、本番の `/live/` の HTML に noindex が残っていることを確かめる（CLAUDE.md「判断・作業の原則」の確認範囲で書く）

止まる条件

* 0章の先頭のコミットが違う
* 手順1の解き方に当てはまらない衝突がある
* `--check live_pages` が止まる理由を出す、または3大会の行に落ちる行・作られないページ・URL の重なりがある（どの行か・理由を書いて、マージせずに止まる）
* 3大会の照合で、「前提」の中身と違う行がある（違う行を全部書いて、マージせずに止まる。直すのは平野さん）
* live/ 以外の生成物に、共通化と取り込みで説明できない差分が出る。study/ の札の見え方が変わる
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/generate_live_pages.py`・`scripts/lib/live.py`・`assets/live.js`・`style.css`・`navbar.js`・`scripts/lib/members.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、3大会の照合の結果（違った行が無ければ「なし」）と、増えた個別ページの数を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-LVP-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-LVP-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1102-LVP-08` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。origin/work/1017-lvp の先頭は 8a63c670a で一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 着手時刻はコンテナの時計で 2026-11-01（指示の決定日 11-02 と1日ずれる。時計の違いとみて進める）

## 報告

- 状態: 作業中
- ブランチ: work/1017-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1017-lvp/docs/logs/CHAT-1102-LVP-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 096eaef6）: https://github.com/retroeater/mj-logs/tree/main/guide/096eaef6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/096eaef6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/096eaef6.md
