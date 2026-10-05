# CHAT-1002-CLD-18

- 着手日時: 2026-10-05
- 対象issue: なし（#448 の関連）
- ブランチ: work/1002-cld
- 着手時HEAD: 6f63525e（= origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：桜蕾戦の短い動画2本（ujj0qvH-mIM・az2iOf7kUpw）の【3】の書き換えと【4】への追加を確かめ、決定を書き残す Chat-Ref: CHAT-1002-CLD-18 マージ: ドキュメントのみの変更（docs/decisions/・docs/logs/）なので、CLAUDE.md「ブランチ運用」のとおり完了報告のうえ cloudflare へ入れてよい。それ以外のファイルを変える必要が出たら、変えずに判断待ちで止まる。 貼る時機: CHAT-1002-CLD-17 の完了の後（同じ作業ブランチを使うため。1つのセッションに貼る指示は1つ） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-CLD-17 のログの `## 報告` が「完了」でマージ済みであることを確かめる（違えば何もせず止まる）。

目的
同じ日の全編と時間が重なる短い動画が、鸞和戦の `jt4E_u--mxg` のほかに桜蕾戦に2本ある。平野さんが、鸞和戦と同じ形になるよう /live の【3】手動補正を書き換え、「【4】カレンダー非掲載」に足した。シートの今の値と、そこから作られる /live の表示・カレンダーの扱いを確かめ、決定を docs/decisions/ に書き残す。
決定（2026-10-05、平野さん。原文）

* 「全編と重なる2本（鸞和戦と同じ形）→鸞和戦と同じ形で直します」「ujj0qvH-mIM→D卓4回戦南場」「az2iOf7kUpw→D卓4回戦」
* 「書き換えを完了しました」
* チャット側の問い（この2本を「【4】カレンダー非掲載」に足したか）に、「非掲載に追加しました」
* 第42期鳳凰戦 A1リーグ第13節C卓について:「https://www.youtube.com/live/tSGKhgviqyE →こちらが本編、他の2本は続き」（他の2本は `iIPXx3m03RM`・`agtYiECMvZA`。中断して別の日に再開したもの）

前提（チャット側。平野さんの決定ではない）

* 「鸞和戦と同じ形」は、docs/decisions/live.md「2026-10-05（CHAT-1002-CLD-12）」「2026-10-05（CHAT-1002-CLD-15）」と docs/decisions/broadcast-calendar.md「2026-10-04（CHAT-1002-CLD-09）」の `jt4E_u--mxg` の扱い（/live に残す・D卓の対局者と実況と解説だけにする・見出しをステージと卓つきにする・カレンダーには載せない）を指すと読んでいる
* チャット側が平野さんに伝えた書き方: 【3】手動補正の各行で、K 卓「D」・L 動画の単位「卓」・M 回戦「4」・O まとめ単位「ベスト16D」・P/Q/R に D卓の対局者4名と実況と解説。N 枝番は `ujj0qvH-mIM` が「4回戦 南場」（半角の空白）、`az2iOf7kUpw` が「4回戦」。平野さんが実際に書いた値と行番号は、チャット側は確かめていない（要確認）
* 見込みの見出し: `ujj0qvH-mIM` は「ベスト16 D卓 4回戦 南場」、`az2iOf7kUpw` は「ベスト16 D卓 4回戦」（空白はどれも半角）。桜蕾戦のステージの名前が「ベスト16」か、枝番が「4回戦」だけのときに見出しがこの形になるかは確かめていない（要確認。鸞和戦で試したのは「4回戦 南場」だけ）
* チャット側が 2026-10-05 に Google カレンダーの連携で読んだ「mj_放送対局」（【4】に足す前の同期の時点）: 2025-03-11 に全編 `9mLtQ9B66Jw`「第9期桜蕾戦 ベスト16 C、D卓」（10:58〜23:56）と `ujj0qvH-mIM`「第9期桜蕾戦 ベスト16 D卓 4回戦」（22:54〜23:56）。2024-03-19 に全編 `3BOjuVnH-E0`「第7期桜蕾戦 ベスト16 C、D卓」（10:57〜翌0:02）と `az2iOf7kUpw`「第7期桜蕾戦 ベスト16 D卓 4回戦」（21:42〜翌0:02）。短い動画の説明欄はどちらも C卓を含む8名だった
* 鳳凰戦の3本は別の日の放送なので、カレンダーに別の予定として載ったままでよいとチャット側は見ている（平野さんからの指定は無い）。決定の記録では、中断の理由は書かない（決定の文書は公開の mj-logs に写る）
* この指示でも、コード・ワークフロー・シート・カレンダーは変えない。書き込みありの実行もしない

手順

1. シートと表示を確かめる。
   * /live のシートを読み（読んだ行数を書き、2回読んで件数が違えば止まる）、【3】手動補正の `ujj0qvH-mIM`・`az2iOf7kUpw` の行の行番号と、B 掲載・K 卓・L 動画の単位・M 回戦・N 枝番・O まとめ単位・P 対局者・Q 実況・R 解説の今の値を書く。N 枝番は文字を1つずつ書き、空白が半角か全角か、前後に余分な空白が無いかを書く。全編 `9mLtQ9B66Jw`・`3BOjuVnH-E0` の行の同じ列も書く
   * 「【4】カレンダー非掲載」に2本の動画ID があるかを書く
   * 今のシートからこの環境で `generate_live_pages.py` を動かし、2本それぞれについて、出るページ（ファイルのパス）、カードの見出しが上の見込みと完全に一致するか、カードに対局者の名前が出ているか、同じ大会の C卓のページに出ないか、ページ上部の「対局者」「実況・解説」の一覧を書く。cloudflare の今の生成物と違うファイルの数と、2本のカードの今の見出しも書く
   * `build_desired()` で、カレンダーに2本が載らないことを書く。今のカレンダー（公開の iCal）と比べて、次の毎朝の実行で消す予定の件数を数え、`MAX_DELETES` と比べて書く（docs/notes/yotei-sheet.md の手順）
2. 記録を直す。docs/decisions/README.md と docs/decisions/live.md・docs/decisions/broadcast-calendar.md の今の内容を読み、上の「決定」を動画ID つきで足す（/live の扱いは live.md、カレンダーの非掲載と鳳凰戦の3本の関係は broadcast-calendar.md。README の書き方に合わせる）。
3. 「マージ:」の行のとおり cloudflare へ入れ、作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）。マージ後に `regenerate-page.yml` が動いたかを書く（docs だけなので動かない見込み。待つのは15分まで）。

止まる条件

* 0章で、CLD-17 の報告が「完了」でない、またはマージされていない
* シートを2回読んで件数が違う
* 2本のどちらかで、生成した見出しが見込みと完全に一致しない、D卓のページ以外にも出る、または「【4】カレンダー非掲載」に動画ID が無い。今の値（N は文字ごと）と表示を書いて止まる（記録は直さない。状態は判断待ち）
* 次の毎朝の実行で消す予定の件数が `MAX_DELETES` を超える見込み（件数と内訳を書いて止まる）
* docs/decisions/ の既存の決定と矛盾していて、どちらが正か平野さんの判断が要る
* docs/ 以外のファイルを変える必要が出た
* 作業ブランチへの push や cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、2本の【3】の行の今の値（行番号つき、N は文字ごと）、【4】の有無、2本が出るページのファイルのパスと見出しとカードの表示、cloudflare の今の生成物の見出し、カレンダーで消す予定の件数、docs/decisions/ に足した文面、本番で確かめられていないこと（次の /live の生成での反映、次の毎朝の同期での削除、ブラウザでの見た目）を入れる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-18` は無し
- 作業ブランチ: `origin/work/1002-cld` はマージ済み（`origin/cloudflare` の祖先）。ローカルの `work/1002-cld` も祖先で、`git merge --ff-only origin/cloudflare` は「Already up to date」（6f63525e）
- 指示文の冒頭の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: なし（#448 の関連）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 87134fea）: https://github.com/retroeater/mj-logs/tree/main/guide/87134fea

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/87134fea/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/87134fea/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/87134fea/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/87134fea/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/87134fea/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/87134fea/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4ba44518.md
