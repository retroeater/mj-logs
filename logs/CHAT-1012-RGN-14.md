# CHAT-1012-RGN-14

- 着手日時: 2026-10-12
- 対象issue: #551
- ブランチ: work/1012-rgn
- 着手時HEAD: fa4ef82f

## 指示

【Claude作成】Claude Code 向け指示：#551 の6本目（live・最強戦）。live_pages・saikyo_pages に、ファイルを書かず止まる理由と警告を全部出す --check を lib/check.py で作る Chat-Ref: CHAT-1012-RGN-14 マージ: 承認済み（チャットで）。下の「止まる条件」のどれかに当たったらマージしない 貼る時機: いつでも（CHAT-1012-RGN-13 は完了） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#551 が Open で、他セッションの着手中コメントが無いことを確かめる。

目的
#551 の (E) の2段目の続き。警告の多い2ページ（live_pages・saikyo_pages）で、`regenerate.py --check` が止まる理由を全部、ファイルを書かずに出せるようにする。
決定（2026-10-12、平野さん）

* 作りは (E)。各スクリプトの `--check`（書かない）を `scripts/lib/check.py` で作る。止まる理由と警告を分け、警告だけなら終了コード 0。順番は帰り道（済）→ houou 系（済）→ live・最強戦 → 残り、title 系は WHS-17（#553）のマージの後。実装のマージは承認済み（止まる条件つき）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 作りは CHAT-1012-RGN-09 の洗い出しの表（live_pages・saikyo_pages の行）と、RGN-10・11・13 で作った形（生成の止まり方を変えずに `Check` に集める）のとおり（要確認）
* 要点（RGN-09 から）:
   * live_pages: 今の Warnings（224〜249行、最後に `report()`）を `Check.warn()` に出す。止まる条件は live.py 146〜159・288行【全】、live_layer3 95・86行【初】、sns.py 56・75行。img.youtube.com への HEAD は `--check` でも回し、キャッシュは書かない（`resolve_hero_thumbs(save=False)` がある）。`_redirects` の上限の検査も書き出しの前に回す
   * saikyo_pages: データの不備はすべて警告で続けている（267・305・343・356・530・544・556・847・901行、NameBook）。これを `Check.warn()` に出す。止まる条件は 349行の assert【初】、1006行 `min(years)`（対局0件）、1052行の sitemap の XML 検査（ページの後）。1052行は `--check` では書き出しの前に回す。assert と空の `min()` は、`--check` では止まる理由として出す
   * live_pages は今の `--check all` で「公開しなかった【3】の行 830件」の警告が出ている（RGN-10）。`--check` でも同じ警告が出ること
* 生成のとき（`--check` でないとき）の止まり方・生成物・終了コードは変えない
* live・最強戦は別のチャット（最強戦の個別ページ、系列 ABM など）が作業していることがある。未マージの work/ ブランチと重ならないかを先に確かめる

手順

1. 確かめる: 上の前提の（要確認）を実物で確かめる。未マージの work/ ブランチを `git branch -r --no-merged origin/cloudflare` で一覧し、`scripts/generate_live_pages.py`・`scripts/generate_saikyo_pages.py`・関係する `scripts/lib/`（live・live_layer3・sns）・試験の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめる（別の行の変更は止まる理由にしない。重なりの内容はログに書く）。重なるページがあれば、そのページだけ外して残りを進めてよい（外したページと理由を報告に書く）。
2. 作る: 2本に `--check` を作り、試験を足す（作った行で: 複数の不整合が全部止まる理由に出る／書き出しの後にあった検査が書き出しの前に回る／警告だけなら終了コード 0／live_pages の `--check` はキャッシュ〈`data/live_thumbs.json`〉を書かない／`--check` ではファイルを書かない）。先に、直す前のコードでは足した試験が通らないことを確かめる（振る舞いを変えないことの確かめは除く）。`python3 -m unittest discover -s scripts/tests` を通す。今のシートで `python3 scripts/regenerate.py --check live_pages saikyo_pages` と、続けて同じページの生成を流し、結果と、生成物が変える前のコードで流したときと同じであること（saikyo_pages の写真の揺らぎ〈`_400x400`・srcset〉は docs/notes/saikyo-page-design.md のとおり差として許す）、`git status` が空であることを確かめる。docs/notes/static-generation.md の `--check` の節に足す（追記先の今の内容を読んでから）。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と push で動く再生成の結果を待つ（上限15分）。上の「決定」を `docs/decisions/automation.md` に足す（同じ決定が既にあれば足さない）。#551 に経過と、残り（title 系〈WHS-17 のマージ待ち〉・残り）をコメントする（閉じない）。

止まる条件

* #551 が Closed、または他セッションの着手中コメントがある
* 2本とも未マージの work/ ブランチと同じ行・同じ関数で重なる、または取り込みで衝突する（片方なら、そのページを外して進めてよい）
* 試験が通らない。または、直す前のコードでも足した試験が通る（振る舞いを変えないことの確かめは除く）
* 対象のページの生成物、または生成の終了コードが、変える前のコードで流したときと違う（シートの変化と写真の揺らぎで説明できる差を除く）
* `--check` の後の `git status` が空でない、または `--check` がファイルを書いた
* ワークフロー（`.github/workflows/`）を変える必要が出た
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（#551 の残りは #551 で追うので、状態は同節と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RGN-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RGN-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1012-rgn/docs/logs/CHAT-1012-RGN-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rgn
- 確認用URL: なし
- マージ: 未
- issue: #551
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fa4ef82f）: https://github.com/retroeater/mj-logs/tree/main/guide/fa4ef82f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa4ef82f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/950bd402.md
