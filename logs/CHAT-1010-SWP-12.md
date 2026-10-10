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

### 手順1 直す

- CHAT-1010-SWP-11 のログの状態の末尾に ` / 続き: CHAT-1010-SWP-12` を足した。
- `origin/cloudflare` を merge で取り込んだ（rebase なし）。**衝突は無し。** `2d084315..origin/cloudflare` で `jpml_pros.html`・`scripts/generate_jpml_pros.py`・`assets/title.js` を変えるコミットは無い（再生成の取り込みなし）。
- 直し方（`scripts/generate_jpml_pros.py`）: `get_internal_link()` から `NEW_TAB_HINT` の `<span>` をやめ、リンクに `aria-describedby="mj-newtab-hint"` を付ける。
  `PAGE_TEMPLATE` の `result_count` の `<p>` の次に `<span id="mj-newtab-hint" hidden>新しいタブで開く</span>` を1つ置く。画像リンクの既存の予告（visually-hidden の文字）は変えない。
- `scripts/tests/test_jpml_pros.py` の期待値を新しい形に合わせた（`DESCRIBED_BY` を参照。別タブのリンクは「説明の参照」か「画像リンクの中の文字」のどちらかを持つことを確かめるループ）。`python3 -m unittest discover -s scripts/tests` は686件 OK。
- `python3 scripts/regenerate.py jpml_pros` で生成し直した。確かめた値:
  - `aria-describedby="mj-newtab-hint"` のリンク **3,259**（見込みどおり）。`id="mj-newtab-hint"` は1つ。`target="_blank"` は4,401で、全部が（説明の参照か画像リンク内の文字の）予告を持つ
  - `jpml_pros.html` は 1,109,803→1,220,674 バイト（SWP-11 の版は 1,328,156）
  - 生成物の差分の種類: 作業ブランチの直しによるもの = 上記の属性と説明の1要素。シートの変化によるもの = 2行（高宮まり・宮内こずえ。`origin/cloudflare` の版と、属性・説明を除いて比べて差が出たのはこの2行だけ。SWP-11 でも同じ2行がシートの変化として出ていた）。元に戻していない。それ以外 = 無し
- アクセシブルな説明（Playwright の CDP `Accessibility.getPartialAXTree`、ローカルの Chromium）: `a[href*="title/"]`（名前「3回」）・`a[href*="saikyo"]`（「5回」）・`a[href*="video_live"]`（「8件」）のいずれも description が「新しいタブで開く」。`aria-describedby` を持つ3,259個の全部で参照先が存在する（参照先は `hidden`、`display:none`）。

### 手順2 測る

SWP-11 と同じ方法・環境・列（「名前」）。`jpml_pros.html` を `page.route` で差し替え、cf（origin/cloudflare の版、1,109,803 バイト）と br（作業ブランチの新しい版、1,220,674 バイト）を交互に10回ずつ（計20回）、直前に各1回の捨て測り。2回行った。

**圧縮後のサイズ**

| 版 | 元 | gzip -9 | brotli（品質11） |
|---|---|---|---|
| cf | 1,109,803 | 147,135 | 100,066 |
| br | 1,220,674 | 149,891 | 100,639 |
| 増加 | +10.0% | +2,756 バイト（**+1.9%**） | +573 バイト（+0.6%） |

**並べ替えの時間**

**計測1回目**（単位 ms。cf=origin/cloudflare の版、br=作業ブランチの版）


クリックから2フレーム描画後まで（total）

| 回 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 中央値 | 増加率 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| cf | 603.2 | 407.7 | 396.3 | 375.4 | 438.4 | 428.4 | 370.4 | 446.1 | 403.9 | 412.2 | 409.9 | - |
| br | 381.9 | 368.2 | 393.0 | 364.2 | 758.8 | 386.0 | 368.2 | 405.5 | 367.1 | 383.9 | 382.9 | -6.6% |

クリックのハンドラ〈同期処理〉のみ（handler）

| 回 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 中央値 | 増加率 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| cf | 52.8 | 50.0 | 50.0 | 47.6 | 51.6 | 68.3 | 51.7 | 63.1 | 61.9 | 58.2 | 52.3 | - |
| br | 50.3 | 52.2 | 50.3 | 46.2 | 67.1 | 69.3 | 53.2 | 63.0 | 52.9 | 51.4 | 52.6 | +0.6% |

**計測2回目**（単位 ms。cf=origin/cloudflare の版、br=作業ブランチの版）


クリックから2フレーム描画後まで（total）

| 回 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 中央値 | 増加率 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| cf | 346.1 | 380.0 | 400.7 | 404.7 | 362.9 | 369.8 | 432.8 | 344.1 | 376.3 | 383.2 | 378.1 | - |
| br | 340.3 | 411.3 | 381.9 | 373.6 | 384.7 | 357.1 | 376.5 | 376.8 | 338.1 | 363.5 | 375.1 | -0.8% |

クリックのハンドラ〈同期処理〉のみ（handler）

| 回 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 中央値 | 増加率 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| cf | 48.8 | 54.6 | 52.1 | 68.1 | 49.5 | 47.3 | 60.2 | 51.0 | 52.9 | 55.5 | 52.5 | - |
| br | 47.6 | 82.3 | 53.9 | 51.1 | 49.6 | 51.6 | 55.1 | 61.7 | 48.4 | 49.7 | 51.4 | -2.2% |

- **判定: 基準内（gzip +1.9% ≦ +10%。並べ替えの中央値は total が -6.6%・-0.8%、handler が +0.6%・-2.2%で、+20% を超えない）。G4-05 を外さず、G3-02 と G4-05 をマージの対象にする。**
  SWP-11 の `<span>` 方式（total +35〜39%）と比べ、並べ替えの時間は cf と同じ水準に戻った。

### 手順3 マージと記録

- push の直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真であることを確かめて `git push origin work/1009-swp-526:cloudflare`（`5f72cc79..a4e01b69`）。権限判定の拒否は無かった。マージ対象の差分は `assets/title.js`・`jpml_pros.html`・`scripts/generate_jpml_pros.py`・`scripts/tests/test_jpml_pros.py` と docs/logs/・docs/decisions/ だけで、見込みの範囲内。
- 本番反映: check-run「Workers Builds: mj」は `completed / success`（push から約1分）。同じコミットの Actions（check・sync・regenerate）も success。
- **本番の HTML に反映を確認した。ブラウザでの見え方は未確認。**

| 確かめた URL | 結果 |
|---|---|
| `https://ryoei.pro/jpml_pros.html` | HTTP 200、1,220,674 バイト（作業ブランチの版と同じ）。`aria-describedby="mj-newtab-hint"` が3,259、`id="mj-newtab-hint"` が1つ |
| `https://ryoei.pro/assets/title.js` | HTTP 200。作業ブランチの版と一致（`cmp`）。キャッシュ回避のクエリを付けた取得で確認（付けない最初の取得は反映前の版だった） |

- マージ後に cloudflare に入った自動処理のコミット: `37de7b17 chore: regenerate jpml_pros.html via GitHub Actions`（件名はそうだが中身は `sitemap-pages.xml` の lastmod 1行のみ）。種類: lastmod = 1件、シートの変化 = 無し（`jpml_pros.html` は変わっていない）、それ以外 = 無し。
- 決定を `docs/decisions/site-review.md`（2026-10-10〈CHAT-1010-SWP-12〉）に足した（マージの前に）。#526 に結果をコメントした（`Chat-Ref: CHAT-1010-SWP-12` 付き）。#526 は G3-07・G4-09 が残るため閉じていない。
- ブランチの片付け: マージ済みの `work/*` は `delete-merged-branches.yml` が毎日削除する（セッションの git プロキシは削除を拒否する）。削除はせず自動に任せる。本番の確かめの成否に条件づけていない。

## 報告

- 状態: 完了（G4-05 を**入れた**: `aria-describedby` 方式で基準内だったため、G3-02 と G4-05 の両方をマージした）
- ブランチ: work/1009-swp-526（マージ済み。削除は自動に任せる）
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-526/docs/logs/CHAT-1010-SWP-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-526
- 確認用URL: なし（本番の HTML の取得のみ。確かめた URL はログの「手順3」の表）
- マージ: 済（`a4e01b69`。Workers Builds・Actions は `completed / success`）。この後のログの追いの push は docs のみ
- issue: #526（結果をコメント。G3-07・G4-09 が残るため閉じない）。起票なし
- 判断が必要なこと: なし
- 未確認の項目: 読み上げソフトでの実際の読まれ方（指示で未確認に書く指定。`aria-describedby` の参照先とアクセシブルな説明は Chromium のアクセシビリティツリーで確認済み）。ブラウザでの本番の見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 37de7b17）: https://github.com/retroeater/mj-logs/tree/main/guide/37de7b17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/37de7b17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
