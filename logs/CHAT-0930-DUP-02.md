# CHAT-0930-DUP-02

- 着手日時: 2026-09-30
- 対象issue: 未定（手順1で確かめる）
- ブランチ: work/0930-dup-02
- 着手時HEAD: 717082969fe5e13a38aed04f4c3d38b2d6113064

## 指示

【Claude作成】Claude Code 向け指示：title/ の期ページの「決勝ライブ」で、同じ日の完全版が無料・メンバー限定の両方あるときは無料だけを出す（プレビューまで） Chat-Ref: CHAT-0930-DUP-02 マージ: 判断待ちで止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/0930-dup-02 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-dup-02 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-dup-02 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。識別子 `DUP` が使われていないこと（CHAT-0930-DUP-01 のコミット・ログが無いこと）を確かめる。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、title/ の生成（生成スクリプト・生成物）に触れているものを書く。

目的
title/ の期ページの「決勝ライブ」で、同じ内容の完全版が「無料」と「メンバー限定」の両方載っているのを直し、プレビューで確かめられるところまで進める。 なお CHAT-0930-DUP-01 は、送る前に差し替えたため欠番。
決定（2026-09-30、平野さん）

* 同じ決勝の日の完全版が「無料」「メンバー限定」の両方あるときは、無料を優先し、メンバー限定は表示しない。

前提（チャット側。平野さんの決定ではない）

* 平野さんは代わりの案として「完全版がメンバー限定であれば無料版を出さない（限定で統一）」でもよいとしたが、チャット側は複雑さが同程度と見て上の決定を勧めた。上の決定の実装が大きくなる場合は、両案の規模を比べて止まる。
* メンバー限定の完全版しか無い日は、これまでどおりメンバー限定を出す（決定の対象外なので今の動きを変えない）。
* 既存の規則（完全版があれば冒頭版〈公開版〉は出さない、完全版が無ければ何も出さない、決勝の放送は初日から順に並べる）は変えない。実物の規則と食い違えば止まる。
* チャット側が本番で見た第43期十段戦（https://ryoei.pro/title/judan/43.html）の決勝ライブは、次の順で5本: 初日 xDw5CYnCJ4I（限定）→ 2日目 dxmDlj61m1c（無料）→ 最終日 5RHZTOEAVIE（無料）→ 2日目 wZfDqW3dMh4（限定）→ 最終日 eOXTZ93geGI（限定）。重複は2日目と最終日。並びが無料・限定ごとに固まっていて「初日から順」と合っていないように見える（本番の実物で確かめ直す）。
* この論点の issue はチャット側では確かめていない。同じ論点の issue が無ければ、新しく起票して着手中のコメントを残す案。

手順

1. 確かめる: 同じ論点の issue（クローズ済みを含む。決勝ライブ・メンバー限定・冒頭版・重複などの語で）を検索し、あれば番号と状態を書く（着手中のコメントがあれば止まる）。無ければ起票して着手中のコメントを残す。決勝ライブの動画を選ぶ処理（完全版／冒頭版の判定、メンバー限定の判定、「同じ日」の結び付け、並べ替え）の場所と、上の前提の規則が実物と一致するかを書く。
2. 調べる（ログに書く）: title/ の全期ページで、同じ日の完全版が無料・限定の両方ある組の件数と一覧（大会・期・日・動画ID2本）。そのうち【3】手動補正で「掲載」を明示的に Y にした限定版が消える組があれば、その一覧。第43期十段戦の並びが「初日から順」になっていない原因。
3. 実装とプレビュー: 決定のとおり、生成側に規則を入れる（シート【2】【3】は書き換えない）。並び順の崩れの原因がはっきりしていて直しが小さければ、あわせて直す。テストを足し、テスト・配信上限・CLAUDE.md の検証を通す。title/ を生成し直し、生成物の差分を種類に分けて書く（変わる期ページの一覧と前後の本数、それ以外の差分の有無）。第43期十段戦の決勝ライブが「初日（限定）→ 2日目（無料）→ 最終日（無料）」の3本になることを確かめる。プレビュー URL はログに書かず、最終報告の「確認用:」の行にだけ書く。判断待ちで止まる（cloudflare へは入れない）。

止まる条件

* 識別子 `DUP` が使われている。未マージのブランチが title/ の生成に触れている。同じ論点の issue に、ほかのセッションの着手中コメントがある。
* 前提の既存の規則が実物と食い違う。
* 「同じ日」を確実に結び付けられない組がある（案と該当の組を書いて止まる）。
* 決定の実装が大きくなる（代わりの案との規模の比較を書いて止まる）。
* 並び順の崩れの原因が大きな直しを要する、またはほかのページに広く影響する（原因と案を書いて止まる。重複の直しだけ先に進めてよい）。
* /live の表示も同じ処理を通っていて、/live の出方が変わる（/live は変えない。影響と案を書いて止まる）。
* シートやワークフローを変える必要が出た。
* テスト・配信上限・検証が通らない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。報告に比較URLを入れ、「確認用:」の行にプレビュー URL を書く。
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-DUP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-DUP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 `DUP` の確認（`git fetch --unshallow origin` の後）: `CHAT-0930-DUP-*` のコミット・トレーラ・`docs/logs/` の履歴なし。`work/0930-dup-02` はローカル・リモートとも無し
- `git checkout -b work/0930-dup-02 origin/cloudflare`（単独のコマンド）が auto モードの分類器に拒否された（理由: Modify Shared Resources）。同時に出した `cat docs/logs/_template.md` も拒否。止まって報告し、平野さんがチャット経由で両方を許可（「同じ操作がまた拒否されたら、別の手段を試さずに止まって報告」）。再実行で成功

## 報告

- 状態: 作業中
- ブランチ: work/0930-dup-02
- ログ: https://github.com/retroeater/mj/blob/work/0930-dup-02/docs/logs/CHAT-0930-DUP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-dup-02
- 確認用URL: 未
- マージ: 未
- issue: 未定
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: 分類器の拒否（経過のとおり。許可を得て再実行し成功）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 54c2563e）: https://github.com/retroeater/mj-logs/tree/main/guide/54c2563e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/54c2563e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/54c2563e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/54c2563e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/54c2563e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/54c2563e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/54c2563e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b7f138e5.md
