# CHAT-1010-SWP-12

- 着手日時: 2026-10-10
- 対象issue: #526
- ブランチ: work/1009-swp-526
- 着手時HEAD: 2d084315

## 指示

【Claude作成】Claude Code 向け指示：jpml_pros の予告（G4-05）を aria-describedby に変えて測り直し、基準内なら #526 の直しをマージする（超えたら G3-02 だけマージ） Chat-Ref: CHAT-1010-SWP-12 マージ: 承認済み（チャットで。2026-10-10、平野さん）。下の「止まる条件」に当たったらマージしない。手順2の計測の結果で、マージする中身が変わる（下の手順のとおり） 貼る時機: CHAT-1010-SWP-11 が判断待ちで止まった後（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-swp-526 を続けて使う（CHAT-1009-SWP-10・SWP-11 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-526 の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1010-SWP-11 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
CHAT-1010-SWP-11 で、`jpml_pros` のサイト内リンクに足した読み上げ用の予告（`<span>` 3,259個）が並べ替えを +35〜39% 遅くした。予告の付け方を、要素を増やさない形に変えて測り直す。基準内なら G3-02 と G4-05 をマージし、超えたら G4-05 を外して G3-02 だけをマージする。
決定（2026-10-10、平野さん）

* SWP-11 の選択肢のうち (b) を採る: G4-05 の予告を、ページに説明を1つだけ置き各リンクから `aria-describedby` で指す形に変えて測り直す
* 測り直して基準内なら、G3-02 と G4-05 をマージしてよい。基準を超えたら G4-05 は見送り、G3-02 だけをマージしてよい
* `jpml_pros` の見た目・並べ替え・検索は、SWP-10 のプレビューで変わっていないことを確認した

前提（チャット側。平野さんの決定ではない）

* 予告の新しい形（案）: `jpml_pros.html` に、画面に出ない説明の要素を1つ（例 `<span id="mj-newtab-hint" hidden>新しいタブで開く</span>`。`hidden` でも `aria-describedby` からは読まれる）置き、サイト内リンクに `aria-describedby="mj-newtab-hint"` を付ける。id 名と置き場所は実物に合わせてよい。画像リンクの既存の予告（visually-hidden の文字）は変えない
* 計測は SWP-11 と同じ方法・同じ環境・同じ列（「名前」）で、その時点の origin/cloudflare の `jpml_pros.html` と、この作業ブランチの新しい `jpml_pros.html` を交互に10回ずつ、計20回測り、中央値で比べる。止める基準も同じ（圧縮後のサイズが gzip で +10% を超える、または並べ替えの時間の中央値が +20% を超える）
* G4-05 を外すときは、生成スクリプト（`scripts/generate_jpml_pros.py`）・`jpml_pros.html`・`scripts/tests/test_jpml_pros.py` を origin/cloudflare の内容に戻す（G3-02 の `assets/title.js` の変更は残す）。#526 の G4-05 は見送りとしてコメントし、新サイト（#529）の要件に回す旨を書く
* マージで入る見込みの差分は、G3-02（`assets/title.js`）、G4-05 を入れる場合はその3ファイル、docs/logs/・docs/decisions/ だけ。取り込みで origin/cloudflare の再生成が `jpml_pros.html` に入っていれば、作業ブランチの生成スクリプトで生成し直して解く
* 読み上げソフトでの実際の読まれ方は確かめられない（未確認の項目に書く）。`aria-describedby` の参照先が存在し、リンクのアクセシブルな説明が「新しいタブで開く」になることは、Playwright のアクセシビリティの情報（`accessibility.snapshot` など）で確かめる
* マージ後の自動処理のコミットが cloudflare に入ることがある。シートの変化による差分は元に戻さず、種類だけ書く。本番の確かめは HTML の取得までで、ブラウザでの巡回はしない（#124）
* 使う skill は無い

手順

1. 直す: CHAT-1010-SWP-11 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1010-SWP-12` を足す。origin/cloudflare を取り込む。G4-05 の予告を上の新しい形に変え、`jpml_pros.html` を生成し直す。`python3 -m unittest discover -s scripts/tests` を通す（テストの期待値は新しい形に合わせてよい）。説明が付いたリンクの数（見込み 3,259）と、アクセシブルな説明を確かめる
2. 測る: 上の前提のとおり測り、表（回ごとの値・中央値・増加率）にしてログに書く
   * 基準内なら、G3-02 と G4-05 をそのままマージの対象にする
   * 基準を超えたら、G4-05 を上の前提のとおり外し、`git diff --stat origin/cloudflare...HEAD` が `assets/title.js` と docs/logs/・docs/decisions/ だけになったことを確かめて、G3-02 だけをマージの対象にする
3. マージと記録
   * CLAUDE.md「ブランチ運用」の「マージの手順」のとおり、push の直前に再 fetch し、祖先を確かめてから `git push origin work/1009-swp-526:cloudflare` する。本番反映（check-run「Workers Builds: mj」）を待つ（15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）。反映後、本番の HTML を取得して、入れた変更が入っていることを確かめる（「本番の HTML に反映を確認した。ブラウザでの見え方は未確認」の粒度で書く）
   * 上の「決定」と、どちらの結果になったかを docs/decisions/ に足す。#526 に結果をコメントする（末尾に `Chat-Ref: CHAT-1010-SWP-12`）。#526 は、残る G3-07・G4-09 があるので閉じない
   * 作業ブランチの片付けは docs/notes/cloud-sessions.md「ブランチの削除」と docs/notes/branch-operations.md「ブランチを削除するとき」に従う（自動の削除の対象ならそれに任せてよい）。片付けは本番の確かめの成否に条件づけない

止まる条件

* CHAT-1010-SWP-11 の状態が判断待ちでない、または work/1009-swp-526 がリモートに無い
* マージの対象の差分が、上の見込みの範囲を超える
* G4-05 を外した後も、`jpml_pros.html` などに G4-05 の変更が残る（origin/cloudflare と一致しない）
* 取り込みで衝突した。生成物は上の前提のとおり解く。生成物でない文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。どちらの結果（G4-05 を入れた／外した）になったかを冒頭に書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-SWP-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-SWP-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-10 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1010-SWP-12"` に該当なし。
- 指示欄の末尾は指示文の最後の行と一致。CHAT-1010-SWP-11 のログの `## 報告` の状態は「判断待ち」。`work/1009-swp-526` はリモートにあり、ローカルと一致（`2d084315`）。

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1009-swp-526
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-526/docs/logs/CHAT-1010-SWP-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-526
- 確認用URL: 作業中
- マージ: 未
- issue: #526
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 237592ff）: https://github.com/retroeater/mj-logs/tree/main/guide/237592ff

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/237592ff/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
