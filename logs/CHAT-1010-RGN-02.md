# CHAT-1010-RGN-02

- 着手日時: 2026-10-10
- 対象issue: #533
- ブランチ: work/1010-rgn
- 着手時HEAD: 19111d74

## 指示

【Claude作成】Claude Code 向け指示：#533 の実装。regenerate.py が失敗したページだけを飛ばして残りを生成・push し、最後にワークフローを failure にする Chat-Ref: CHAT-1010-RGN-02 マージ: 承認済み（チャットで）。下の「止まる条件」のどれかに当たったらマージしない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#533 の本文・コメント・ラベルを読み、Open であることと、他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。

目的
#533 の論点 (a)〜(h) のうち、平野さんが決めた形で `scripts/regenerate.py` と `regenerate-page.yml` を直す。1ページの失敗で、その回のほかのページの再生成と push まで止まらないようにする。
決定（2026-10-10、平野さん）

* (a)(d) 1ページの生成が失敗したら、そのページだけを飛ばし、残りのページは生成して push する。失敗の種類（シートの検査・例外など）では分けない。失敗したページの途中の出力は戻す
* (b) ページを飛ばした回は、生成できたページを push したうえで、ワークフロー全体の結論を failure にする（今の失敗通知メールを保つ）
* (c) 知らせは、今の失敗通知メールに加えて、ジョブのサマリに「飛ばしたページと理由」の一覧を出す。常設 issue は作らない
* この指示のマージは承認済み（止まる条件つき）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 今の作りは #533 の本文「今の作り（cloudflare 5bba42c）」のとおり（要確認。その後の cloudflare の変更で行番号・作りが変わっていれば、今の実物に合わせる）
* 種類で分けない理由: `scripts/lib/` などの共有部品の誤りなら全ページが失敗し、結果として何も push されない。分ける仕組みは作らない
* (e) `regenerate.py` の中で直せば、セッションの `regenerate.py all`、手動実行、`update-live-channel.yml` の `workflow_call`（live_pages・title_pages）にも同じ扱いが及ぶ見込み（要確認）。セッションで使うときも、最後に飛ばしたページの一覧と0でない終了コードを出す
* (f) `check_asset_limits` とコミット対象の一覧は、成功したページだけで出す
* (g) 失敗したページの途中の出力を戻す方法は、実物を見て決めてよい（例: ページの生成の前後で `git status --porcelain` を比べ、そのページの生成中に変わったパスを戻す・消す）。前のページがすでに変えた共有のファイル（`sitemap*.xml`・`_redirects` など）を失敗したページがさらに変えた場合など、確実に戻せない形が見つかったら、その形と対処を報告に書く（直せないなら止まる条件のとおり止まる）
* (h) 再生成でページを飛ばした回も、YouTube・楽天の取得失敗を報告する2ステップは動かす（`if` に状態の関数を足すなど）
* ワークフローの形: 「対象ページを再生成」は、飛ばしたページがあっても成功したページの `files=` を出して後のステップ（lastmod・well-formedness・title/ の転送・コミット・push）を動かし、最後のステップで failure にする、など。サマリは `$GITHUB_STEP_SUMMARY`
* 帰り道の取り込みの決定（`docs/decisions/` の帰り道の分野。2026-10-10「JSON に無い回はその回だけ外して生成し、ワークフローは失敗扱い」「知らせはメールと常設 issue」）は別のセッションが `regenerate-page.yml` に入れる予定（要確認）。この指示はそれと同じ「外して続け、最後に失敗」の形にそろえる。帰り道の常設 issue の知らせはこの指示では作らない
* #533 に残る論点（シートから作るほかのページにも `--check` のような検査だけを回す手段を持たせる。DIC-22 のコメント）は、この指示では扱わない。#533 は閉じない（10/12〈月〉の週次の再生成を見てから、チャット側で閉じる指示を出す）
* 手順2の試験の手動実行はわざと失敗させるので、平野さんに失敗通知メールが届く（対応不要）

手順

1. 確かめる: 上の「前提」の（要確認）を実物で確かめる。未マージの work/ ブランチを `git branch -r --no-merged origin/cloudflare` で一覧し、`scripts/regenerate.py`・`.github/workflows/regenerate-page.yml` の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いか確かめる（別の行への追加〈例: work/1008-hou の `OUTPUT_OVERRIDES` の1行〉は止まる理由にしない。重なりの内容はログに書く）。docs/notes/branch-operations.md「ワークフローを変更したとき」を読み、`git log -- .github/workflows/regenerate-page.yml` と関係するログ・issue を見る。この変更を cloudflare に push したとき、push の再生成の対象がどうなるか（`regenerate.py` 自身の変更で何ページが対象になるか）を確かめ、マージ後の見込みとして書く。
2. 直して試す: 上の「決定」「前提」のとおり `scripts/regenerate.py` と `regenerate-page.yml` を直し、`scripts/tests/` に試験を足す（失敗するページを含む並びで、残りが生成されること・失敗したページの出力が戻ること・終了コードが0でないこと・成功したページだけがコミット対象に出ること）。`python3 -m unittest discover -s scripts/tests` を通す。先に、直す前のコードでは足した試験が通らないことを確かめる。変える前（その時点の origin/cloudflare）と変えた後のコードで、間を空けずに続けて `python3 scripts/regenerate.py all` を流し、生成物が同じであることを確かめる。作業ブランチで `regenerate-page.yml` を手動実行して2通り試す: (i) 普通の実行（success になること）。(ii) 試験用の一時のコミットで軽いページ1つの生成を失敗させ、そのページともう1つのページを `target_page` に指定する（もう1つのページは生成・コミットされ、失敗したページの出力はコミットされず、サマリに一覧が出て、結論が failure になること）。試験の後に一時のコミットを戻し、戻すコミットの差分がその変更だけであることを確かめる。作業ブランチに入った生成物のコミットは、シートの変化で説明できるものかを確かめる。docs/notes/static-generation.md（「regenerate-page.yml」「シートのフィルタの検知」の通知の書き方など、今の止まり方を書いている所）を、追記先の今の内容を読んでから直す。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と regenerate-page.yml の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）。#533 に経過をコメントする（閉じない。残る論点は `--check` の件だけであることを書く）。上の「決定」を `docs/decisions/automation.md` に足す。

止まる条件

* #533 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが `scripts/regenerate.py`・`regenerate-page.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する
* 失敗したページの途中の出力を確実に戻せない形があり、この指示の中で直せない
* 試験が通らない。または、直す前のコードでも足した試験が通る（試験が直しを確かめていない）
* 変える前と変えた後の `regenerate.py all` の生成物に差がある（シートの変化で説明できる差を除く。どちらかの回でページが失敗したときは、そのページを除いて比べ、失敗の内容を書く）
* 手動実行の (i) が success にならない。または (ii) で、もう1つのページがコミットされない／失敗したページの出力がコミットされる／サマリに一覧が出ない／結論が failure にならない
* 一時のコミットを戻すコミットの差分に、その変更以外が含まれる
* マージ後の regenerate-page.yml が、今回の変更による理由で失敗した（無関係な失敗〈シートの検査など〉なら、原因をログに書いたうえで残りの手順を進めてよい）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（手動実行の試験で届いた失敗通知メールは対応不要、と書く）
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RGN-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RGN-02 を書く。最終報告にも「試験の手動実行の失敗通知メールが届くが対応不要」と書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1010-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1010-rgn/docs/logs/CHAT-1010-RGN-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rgn
- 確認用URL: なし
- マージ: 未
- issue: #533
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2e7da207）: https://github.com/retroeater/mj-logs/tree/main/guide/2e7da207

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e7da207/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
