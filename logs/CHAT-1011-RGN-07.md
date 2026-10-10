# CHAT-1011-RGN-07

- 着手日時: 2026-10-11
- 対象issue: なし
- ブランチ: work/1011-rgn
- 着手時HEAD: af7084f7

## 指示

【Claude作成】Claude Code 向け指示：申送り（ジョブのサマリの読み方・lastmod が動く仕組み・振り返りの Fable 5.1 レビューを文書に足す）。ドキュメントのみ Chat-Ref: CHAT-1011-RGN-07 マージ: ドキュメントのみ（docs/notes/・ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・生成物は変えない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
RGN-01〜06 の振り返り（2026-10-11）の申送りのうち、機械的に確かめられる手順に落とせる3つを文書に足す。規則と理由の一句だけを書き、事例は書かない。
決定（2026-10-11、平野さん）

* 申送り A（ジョブのサマリの読み方）と B（lastmod が動く仕組み）を文書に足す。C（一部だけ重なる issue の扱いの規則）は足さない（既存の規則で足りる）
* 「振り返り」は、その時点のモデル（Opus 5.5 等）が報告を作った後に、必ず Fable 5.1 のレビュー（advisor）を入れる

前提（チャット側。平野さんの決定ではない。追記先の実物に合わせて文面・場所を変えてよい）

* A の文面の案: 「Actions のジョブのサマリ（`GITHUB_STEP_SUMMARY`）は API に無く Code からは読めない。チャット側が Chrome で実行のページを開いて読む。注記（annotations）は Code が check-run の annotations API で読める」。確かめた所: CHAT-1010-RGN-02 の「手順2」（サマリは check-run の `output.summary` が空、github.com はプロキシが拒否）と CHAT-1010-RGN-04 の「手順2」（注記は annotations API で読めた）
   * 置き場所の第1候補: docs/notes/chat-side-operations.md「読み方」の表の「Actions の実行結果」の行に一句足す（容量に上限があるので、警告域に入るなら第2候補へ）。第2候補: docs/notes/chrome-reading.md「GitHub のファイルを読むとき」に1行足す。どちらに置いたかを報告に書く
* B の文面の案: 「HTML を変えるコミットは、後で戻しても lastmod を動かす（`--from-git` は最終コミット日を見るため）。試験の一時のコミットでも同じで、動いた lastmod は手で戻さない」。確かめた所: CHAT-1010-RGN-02 の「手順2」（video_en.html の印のコミットと戻しで、sitemap-pages.xml の lastmod が 2026-09-13 → 2026-10-10 になった）
   * 置き場所: docs/notes/sitemap-lastmod.md（CLAUDE.md「禁止事項」の「手で書き換えない」と同じ趣旨の記述があれば、置き換え・拡張でよい）
* 振り返りの規則の文面の案: docs/notes/chat-routines.md「振り返り」の「成果物の形」に「報告の後、平野さんが Fable 5.1 に切り替え（`/model claude-fable-5-1`）、Fable 5.1 が報告を読み直してレビュー（advisor）を返す。チャット側はモデルを切り替えられないので、報告の末尾で切り替えを促す」を足す。chat-routines.md は容量の上限が無い（同文書の冒頭）
* 容量の測り方は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」のとおり（chat-side-operations.md は警告 26KB／失敗 28KB、`assets-check.yml`）

手順

1. 確かめる: 同じ論点の open issue を検索し（クローズ済みも含めて確認。語: 「サマリ」「GITHUB_STEP_SUMMARY」「lastmod」「振り返り」「Fable」）、あれば止まって報告する。未マージの work/ ブランチが追記先の3文書（chat-side-operations.md または chrome-reading.md、sitemap-lastmod.md、chat-routines.md）の同じ節を変えていないか確かめる（同じファイルの別の節の変更は止まる理由にしない。重なりの内容はログに書く）。
2. 足す: 3か所とも、追記先の今の内容を読んでから足す（同じ趣旨の記述があれば置き換え・拡張し、どう処理したかを報告に書く）。chat-side-operations.md に足すときは前後のサイズを測り、警告域（26KB）に入るなら chrome-reading.md に置く。上の「決定」を `docs/decisions/` の合う分野（operations.md の想定）に足す。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。assets-check の結果を待つ（上限15分）。

止まる条件

* 同じ論点の issue（クローズ済みを含む）がある
* 追記先と矛盾していて、どちらが正か判断が要る（同じ趣旨なら止めず置き換え・拡張）
* chat-side-operations.md と chrome-reading.md のどちらに置いても容量の警告域に入る
* 未マージの work/ ブランチが追記先の同じ節を変えている、または取り込みで衝突する（両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（3か所の前後のサイズと、A をどちらに置いたかを書く）
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-RGN-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-RGN-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1011-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1011-rgn/docs/logs/CHAT-1011-RGN-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-rgn
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj efa62293）: https://github.com/retroeater/mj-logs/tree/main/guide/efa62293

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/efa62293.md
