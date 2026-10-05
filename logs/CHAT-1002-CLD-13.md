# CHAT-1002-CLD-13

- 着手日時: 2026-10-05
- 対象issue: なし（#448 の関連）
- ブランチ: work/1002-cld
- 着手時HEAD: be073b9f

## 指示

【Claude作成】Claude Code 向け指示：/live の D卓のページで、27分の動画（jt4E_u--mxg）の見出しを「ベスト16 D卓 4回戦 南場」にする方法を調べる（調査だけ） Chat-Ref: CHAT-1002-CLD-13 マージ: 判断待ちで止まる（調査だけ。コード・シート・カレンダーは変えず、ログも cloudflare へ入れない。続きの指示で同じ作業ブランチを使う） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、/live の生成（`scripts/generate_live*.py`・`scripts/lib/live_*.py`）に触れているものを書く。

目的
/live の鸞和戦 第6期 ベスト16 D卓のページ（`live/ranwa/6/b16-d.html`）の「ライブ」の欄で、27分の動画 `jt4E_u--mxg` の見出しが「4回戦 南場」になっている。平野さんはこれを「ベスト16 D卓 4回戦 南場」にしたい。【3】手動補正の書き換えだけでできるか、できるならどのセルに何を書くかを調べる。この指示では何も直さない。
決定（2026-10-05、平野さん。原文）

* 「27分の動画の見出し『4回戦 南場』→『ベスト16 D卓 4回戦 南場』にしたい」

前提（チャット側。平野さんの決定ではない）

* 同じ日に平野さんは、この動画の対局者について「例外なのでコードは直さない」と決めている（docs/decisions/live.md「2026-10-05（CHAT-1002-CLD-12、#448 の関連）」）。見出しについては方法の指定が無い。チャット側は、【3】だけでできる方法を優先し、できないときだけコードの案を出す進め方がよいと見ている
* CHAT-1002-CLD-11 のログが引用した `scripts/generate_live_pages.py` の `game_label()` では、回戦の動画は「4回戦」＋（ページと違う卓なら卓）＋枝番、卓の動画は ステージ＋卓＋枝番 になる。回戦の動画にステージは付かず、卓の動画に回戦は入らない
* CLD-11 のログの【3】手動補正の `jt4E_u--mxg` の行（3147行）: 掲載 Y、卓 D、動画の単位 空欄（【2】の「回戦」が使われる）、回戦 4、枝番 南場、まとめ単位 ベスト16D、対局者 金子正明、猪鼻拓哉、木戸僚之、猿川真寿、実況 大野雄輝、解説 阿久津翔太。今もこの値か、行番号が同じかは確かめていない（要確認）
* チャット側が考えた【3】だけの書き方の候補: 動画の単位を「卓」にし、枝番に「4回戦 南場」と書く（`game_label()` の卓の動画の形で「ベスト16 D卓 4回戦 南場」になる見込み）。枝番にこの値を書けるか（取りうる値の検査、`BRANCH_ORDER` での並び）、回戦の列の「4」をどうするか、カードの対局者が出なくなるか、並び順・検索の語・title/ への影響は、どれも確かめていない（要確認）
* `jt4E_u--mxg` は「【4】カレンダー非掲載」に入っていて、カレンダーには出ていない（2026-10-05 にチャット側が Google カレンダーの連携で確認）
* シートを読む手段は指定しない。docs/notes/live-channel-write.md・docs/notes/cloud-sessions.md を読み、生成と同じ経路で読む

手順

1. 実物を確かめる。/live のシートで `jt4E_u--mxg` の行を層ごと（【1】・【2】・【3】）に行番号つきで読み、見出しに関わる列の値と、どの層の値が使われているかを書く（読んだ行数を書き、2回読んで件数が違うなら止まる）。cloudflare の今の生成物 `live/ranwa/6/b16-d.html` の「ライブ」の欄のカード（`R3IZ244ANxQ`・`jt4E_u--mxg`）の今の見出しと、カードに出ている項目を書く。
2. 【3】だけの書き方を、シートを書き換えずに計算して確かめる。前提の候補と、Code が見つけたほかの書き方について、候補ごとに次を書く。
   * 書くセル（【3】の行番号・列）と値
   * D卓のページでの `jt4E_u--mxg` の見出しが「ベスト16 D卓 4回戦 南場」と完全に一致するか。カードの対局者・長さ・日付の表示、「ライブ」の欄の中の並び順、ページの本数と時間、ページ上部の「対局者」「実況・解説」の一覧が、今とどう変わるか
   * C卓のページ（`b16-c.html`）にこの動画が出ないままか
   * 鸞和戦の一覧・`live/index.html`・検索の語・「他の対局」の一覧・title/ での表示がどう変わるか。変わるファイルの数
   * 生成や検査（`assets-check.yml` が動かす検査、シートの値の検査）がエラーや警告を出さないか
   * `build_desired()` でカレンダーに変化が無いこと
3. 結論を書く（実装しない）。
   * 【3】だけで、ほかの表示を崩さずに「ベスト16 D卓 4回戦 南場」にできるなら、平野さんが書き換えるセルと値の一覧を書く
   * できない、または副作用が残るなら、その内容と、コードを変える案（変える場所と規則、見出しが変わるほかのカードの数と動画IDの一覧）を書く

止まる条件

* 0章の一覧に、/live の生成に触れている未マージのブランチがある
* シートを2回読んで件数が違う
* 調べるために、コード・ワークフロー・シート・カレンダーを書き換える必要が出た（しない）。書き込みありの実行もしない
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）。報告に、`jt4E_u--mxg` の行の今の値（層・行番号つき）、候補ごとの結果（見出しの文字列、ほかの表示の変化、変わるファイルの数、検査の結果）、結論（書き換えるセルと値、またはコードの案）、読めなかった項目（「未確認の項目」）を入れる
* マージは冒頭の「マージ:」の行のとおり（しない）。作業ブランチは片付けずに残す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-13` は無し
- 作業ブランチ: `origin/work/1002-cld`（be073b9f）は cloudflare へマージ済みで、cloudflare と同じ。ローカルも同じ（be073b9f）なのでそのまま使う
- 指示文の冒頭の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 14a118fd）: https://github.com/retroeater/mj-logs/tree/main/guide/14a118fd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/14a118fd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b89c3b19.md
