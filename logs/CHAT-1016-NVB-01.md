# CHAT-1016-NVB-01

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-nvb
- 着手時HEAD: 6a497a74f

## 指示

【Claude作成】Claude Code 向け指示：白地の新しいナビで WRC の項目が「JPML」と出るのを「WRC」に直す（黒地の旧ナビは「JPML / WRC」のまま） Chat-Ref: CHAT-1016-NVB-01 マージ: 承認済み（チャットで） 貼る時機: いつでも（新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-nvb の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-nvb を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-nvb origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
`navbar.js` の WRC の項目は「JPML<br>WRC」の2行で、白地の新しいナビ（houou/・最強戦の新しいページなど）は `<br>` の前の1行だけを出すため「JPML」と表示される。「連盟 / JPML」や JPMLリーグ と紛らわしいので、白地のナビでは「WRC」と出す。
決定（2026-10-16、平野さん）

* 白地の新しいナビでの WRC の項目の表記は「WRC」にする（今は「JPML」。JPML は別に「JPMLリーグ」があるため紛らわしい）
* 黒地の旧ナビは今のまま「JPML / WRC」の2行で残す（上の行を「WRC」にして下の行を変える案〈「WRC / League」など〉は採らない）
* マージは承認済み（止まる条件に当たらなければ cloudflare へ入れてよい）

前提（チャット側。平野さんの決定ではない）

* 本番の `navbar.js`（2026-10-16 にチャット側が WebFetch で読んだ）では、WRC のドロップダウンの見出しが `JPML<br>WRC`、白地のナビは `navLink.innerHTML.split('<br>')[0]` で1行目だけを出している（要確認。実物で確かめ、食い違えば止まる）
* 直し方の案: 白地のナビで出す1行の表記を項目ごとに上書きできるようにする（例: 見出しのリンクに `data-short-label="WRC"` を付け、白地のナビはそれがあれば使い、無ければ今のとおり `<br>` の前を使う）。実物に合わせて別の形でもよい。ほかの項目の表記は変えない
* 2026-10-15 の決定（docs/decisions/title.md、#523）は「ryoei.pro での表示はすべて新名称」だが、旧ナビの「JPML / WRC」は今回の決定で平野さんが残すと決めた（決定の節に例外として記録する）

手順

1. 着手の確かめ: 上の前提（`JPML<br>WRC` と `split('<br>')[0]`）を `navbar.js` で確かめる。`git branch -r --no-merged origin/cloudflare` の各ブランチ（work/1011-swp-nav を含む）が `navbar.js`・`scripts/tests/test_navbar.py` の同じ行・同じ関数を変えているか、取り込みで衝突するかを確かめ、ログに書く。
2. 直す: `navbar.js` を直し、`scripts/tests/test_navbar.py`（無ければ navbar のテストの置き場所に合わせる）に「白地のナビで WRC の項目が『WRC』、黒地のナビは『JPML』『WRC』の2行」のテストを足す。足したテストが直す前のコードでは通らないことを確かめる。`python3 -m unittest discover -s scripts/tests` を通す。生成物にナビの文字が焼き込まれているページがあれば `python3 scripts/regenerate.py --changed` 等で生成し直し、差分を確かめる。
3. 確かめとマージ: headless Chrome（ローカルか Workers Builds のプレビュー。待つのは15分まで）で、白地のナビのページ（houou/ のトップ）と黒地のナビのページ（`jpml_pros.html`）を 375px・明で見て、白地は「WRC」、黒地は「JPML / WRC」であること、ドロップダウン（ランキング・成績詳細）が開けることを確かめ、PNG 2枚を `SendUserFile` で送る。止まる条件に当たらなければ cloudflare へマージし、本番（`navbar.js?v=<未使用の値>`）が新しい版を返すことを確かめる。決定の節を docs/decisions/（ナビ・WRC の表記の決定がある分野のファイル。迷えば title.md の #523 の節の後）に足す。

止まる条件

* 前提（`JPML<br>WRC`・`split('<br>')[0]`）が実物と食い違う
* 差分が `navbar.js`・navbar のテスト・docs（docs/decisions/・docs/logs/）・（ナビの文字が焼き込まれている場合だけ）その生成物のナビの部分 以外に出る
* 黒地の旧ナビの表示、またはほかの項目の白地での表記が変わる
* 未マージの work/ ブランチが `navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-NVB-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-NVB-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: 全ブランチのコミット・docs/logs の履歴に `NVB` なし。work/1016-nvb はローカル・リモートとも無く、origin/cloudflare（6a497a74f）から作成
- 0章: 指示欄の末尾は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 手順1（前提）: `navbar.js` の WRC の見出しは `aria-expanded="false">JPML<br>WRC</a>`、白地のナビは `navLink.innerHTML = navLink.innerHTML.split('<br>')[0]`（白地は houou/ と `mj-saikyo-n-page` の付いたページ）。前提と一致
- 手順1（未マージのブランチ）: `git branch -r --no-merged origin/cloudflare` は 1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1016-hou5・1016-nvb（自分）。
  各ブランチの `git diff --name-only origin/cloudflare...<branch> -- navbar.js scripts/tests/test_navbar.py` はすべて空（1011-swp-nav の差分は docs/logs/CHAT-1011-SWP-14.md のみ）。重なり・衝突なし
- 焼き込みの確認: `JPML<br>WRC` を含む HTML なし（`wrc_ranking.html` の一致はページ本体の `?sheet=JWRC` の選択肢）。全ページは `navbar.js` を読み込んで描画するので生成し直しは不要
- 手順2: WRC の見出しに `data-short-label="WRC"` を付け、白地のナビは `navLink.dataset.shortLabel || navLink.innerHTML.split('<br>')[0]` を出す。
  `scripts/tests/test_navbar.py` に `test_wrc_item_label`（黒地の見出しは `JPML<br>WRC` のまま・`data-short-label` は WRC の1件だけ・白地の式が短い表記を優先）を追加。
  直す前のコードで実行して失敗（`FAILED (failures=1)`）を確認、直した後 `python3 -m unittest discover -s scripts/tests` は `Ran 1283 tests ... OK`
- 手順3（ローカルの headless Chromium、`python3 -m http.server`、外部ドメインは遮断、375px・明）: 見出しの innerHTML を修正前（origin/cloudflare の navbar.js を差し込み）と修正後で比べた
  - houou/（白地 `mj-nav-new`）: 前 `連盟,鳳凰戦,女流桜花,JPML,最強戦,番組,リソース,良栄` → 後 `連盟,鳳凰戦,女流桜花,WRC,最強戦,番組,リソース,良栄`（WRC 以外は同じ）
  - jpml_pros.html（黒地 `navbar-dark bg-dark`）: 前後とも `連盟<br>JPML,…,JPML<br>WRC,…,良栄<br>Ryoei`（変化なし）
  - 両ページでハンバーガーを開き WRC の見出しを押すと、ドロップダウンに「ランキング」「成績詳細」が出る。PNG 2枚を SendUserFile で送付
- 成果物のコミット: cb078db55（`navbar.js`・`scripts/tests/test_navbar.py`・`docs/decisions/title.md`）。決定は title.md の末尾（#523 の節の後）に足した

## 報告

- 状態: 作業中
- ブランチ: work/1016-nvb
- ログ: https://github.com/retroeater/mj/blob/work/1016-nvb/docs/logs/CHAT-1016-NVB-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-nvb
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0bead334）: https://github.com/retroeater/mj-logs/tree/main/guide/0bead334

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6a497a74.md
