# CHAT-1008-DIC-19

- 着手日時: 2026-10-10
- 対象issue: #533
- ブランチ: work/1008-dic
- 着手時HEAD: 22ca975d

## 指示

【Claude作成】Claude Code 向け指示：申送り（辞書シートの検査だけを行う手段を作る・#533 に論点を足す） Chat-Ref: CHAT-1008-DIC-19 マージ: 生成物を変えない検査の追加とドキュメントだけなので、完了報告のうえ cloudflare へ入れてよい（生成物が変わるときは止まる） 貼る時機: いつでも（CHAT-1008-DIC-18 は完了） 作業ブランチ: クラウドセッションで実行する。work/1008-dic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-dic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-10 に「辞書」タブの変化（見出しの「コメント」列の削除・見出しの行の写し・同じ語の重複）で、全ページの再生成が止まった（#243。10/9 の #233 も同じ仕組み）。DIC-17 では、シートを直したと聞いた後に実物を検査しないまま指示を書き、1往復増えた。シートを直した後、指示を書く前に、生成と同じ検査をファイルを書かずに回せるようにする（申送り A）。あわせて、#533 に論点を足す（申送り B）。
決定（2026-10-10、平野さん）

* 振り返りの申送り A（辞書シートの検査だけを行う手段）と B（#533 への論点の追加）を行う

前提（チャット側。平野さんの決定ではない）

* A は、`scripts/generate_resource_dictionary.py` に、シートを生成と同じ経路で読み、生成と同じ検査（見出し・知らないカテゴリ・読みと語の組の重複など、今ある検査すべて）を行い、ファイルを書かずに結果（通った／止まる行と理由、カテゴリごとの語数）を出す手段を足す（例: `--check`。形は実物に合わせてよい）。検査の中身は今のものを使い、二重に書かない。止まる理由が複数あれば、できれば1回で全部出す（DIC-17 では2か所が見つかった）。難しければ最初の1つでよく、どちらにしたか報告する
* 生成物（`resource_dictionary.html`・`dic/`・`resource_dictionary.*`）は変えない。`regenerate.py` も変えない（#533 で扱う）
* 使い方を書く場所は2つ。(1) `docs/notes/static-generation.md`「ページの一覧」の辞書の行（または近く）に、検査の手段とその使い方を1行。(2) `docs/notes/chat-side-operations.md` の「平野さんがシート（タブ）を用意した・直したと言ったとき」の項に、「『辞書』タブは、実装・マージの指示の前に検査の手段を Code に回させ、通ってから書く」を短く足す。規則だけを書き、事例（10/10 の経緯）は書かない
* B は、#533 にコメントで論点を足す: 「シートの検査の失敗（見出しの変化・知らないカテゴリ・重複など）で全ページの再生成が止まった例が #233（10/9）・#243（10/10）の2回ある。シートの検査の失敗は、そのページだけ飛ばして通知し、残りのページの再生成と push は続ける案を論点に足す」。#533 の今の論点 (a)〜(h) と重なれば、重なる論点の番号を書いて補足にする

手順

1. 確かめる: 上の決定を `docs/decisions/` に足す。#533 の本文・コメントを読み、Open であることと、今の論点を確かめる。未マージの work/ ブランチが `scripts/generate_resource_dictionary.py`・`docs/notes/static-generation.md`・`docs/notes/chat-side-operations.md` を変えていないか確かめる。
2. 作る: 前提のとおり検査の手段を足し、テストを足す（壊れたデータ〈見出しの不足・知らないカテゴリ・重複〉で止まる理由が出ること、正しいデータで通ること、ファイルを書かないこと）。今の「辞書」タブで回して、通ることと語数（1,870 の見込み）を報告する。前提の2か所に書く（追記先の今の内容を読んでから。容量は CLAUDE.md の更新ルールのとおり測る）。全ページの再生成で生成物が変わらないことを確かめる。#533 にコメントする。
3. 報告する: マージの行のとおり、完了を報告して cloudflare へ入れる。

止まる条件

* #533 が Closed
* 未マージの work/ ブランチが上の手順1のファイルを変えている
* 検査の手段を足すと生成物が変わる、または今の「辞書」タブで検査が止まる（止まる行と理由を報告する）
* 追記先が容量の上限を超える
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（検査の手段の呼び方を書く）
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- `git log --all --grep="CHAT-1008-DIC-19"` は0件
- origin/work/1008-dic（4544bac2）は origin/cloudflare の祖先（マージ済み）。ローカルの work/1008-dic も同じで祖先のため、`git merge --ff-only origin/cloudflare` で 22ca975d に進め、ログを積んで push した

### 手順1（確かめ）と、止まった理由

- `docs/decisions/features.md` に 2026-10-10（DIC-19）の決定を足した
- #533: Open。論点は (a)〜(h)。コメントは1件（DIC-18 のもの）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が手順1の3ファイルを変えているか:
  - work/1008-hou: **`docs/notes/static-generation.md` を変えている**（先頭 5ae8de6b、2026-10-10 09:41 UTC、CHAT-1010-HOU-08。#518）。変更は「ページの一覧」の節の2か所: 冒頭の段落の末尾に「鳳凰戦の新ページ（`houou/`…）」を足し、表に「ビルド時生成（サブディレクトリ、鳳凰戦の新ページ）」の行を足す（タイトル戦の新構成の行の後）。辞書の行（「ビルド時生成（独自: 全カテゴリをまとめた辞書ファイルを組み立てる）」）は同じ表の4行上
  - work/1008-dic・work/1009-nen・work/1009-swp-526・work/1010-rev-limit・work/1010-xap: 3ファイルとも変えていない
  - `scripts/generate_resource_dictionary.py`・`docs/notes/chat-side-operations.md` を変えているブランチは無い
- 止まる条件「未マージの work/ ブランチが上の手順1のファイルを変えている」に当たるため、手順2（検査の手段・テスト・2か所の追記・#533 へのコメント）には手を付けずに止めた。生成スクリプト・生成物・#533 は変えていない

## 報告

- 状態: 判断待ち / 続き: CHAT-1008-DIC-20
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未（止まる条件に当たったため。work/1008-dic にはログと決定だけを push した）
- issue: #533（Open を確かめた。コメントはしていない）
- 判断が必要なこと:
  - 未マージの work/1008-hou（#518、CHAT-1010-HOU-08）が `docs/notes/static-generation.md`「ページの一覧」を変えている（冒頭の段落と表に鳳凰戦の新ページを足す。辞書の行の4行下に行を足す）。DIC-19 の追記先 (1) と同じ節。次のどれで進めるか: (i) work/1008-hou のマージを待ってから DIC-19 をやり直す、(ii) 重なりを承知で進める（辞書の行の中か直後に1行足すだけで、work/1008-hou の変更とは行が離れているため、取り込みは衝突しない見込み）、(iii) 使い方の1行を「ページの一覧」以外（例: 同じ文書の「メンテナンス用スクリプトの詳細」）に書く
  - 検査の手段の呼び方は、まだ作っていないので未定（案は `python3 scripts/generate_resource_dictionary.py --check`）
- 未確認の項目:
  - 今の「辞書」タブでの検査の結果（手順2で行う。DIC-18 の生成では 1,870 語で通っている）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e3c7dfe9）: https://github.com/retroeater/mj-logs/tree/main/guide/e3c7dfe9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c7dfe9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c7dfe9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c7dfe9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c7dfe9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c7dfe9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c7dfe9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
