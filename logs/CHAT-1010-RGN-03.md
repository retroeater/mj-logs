# CHAT-1010-RGN-03

- 着手日時: 2026-10-10
- 対象issue: #533（ほかは起票する）
- ブランチ: work/1010-rgn
- 着手時HEAD: 7e1c39ce

## 指示

【Claude作成】Claude Code 向け指示：RGN-02 の判断待ちの2点を記録し、ubuntu-latest の Ubuntu 26 への移行（と Node.js 20 の廃止）を調べて issue に起票する（コード・ワークフローは変えない） Chat-Ref: CHAT-1010-RGN-03 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・生成物は変えない（直す必要が見えても直さずに issue の論点に書く） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1010-RGN-02.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。RGN-02 の `## 報告` の状態の末尾に `/ 続き: CHAT-1010-RGN-03` を足す（`## 指示` 欄より後ろの見出しを相手にする）。

目的
RGN-02 の判断待ちを片付ける。あわせて、regenerate-page.yml #257 のログに出た GitHub の警告（ubuntu-latest が 2026-10-19 から Ubuntu 26 へ移る、Node.js 20 の廃止）が mj のワークフローに与える影響を調べ、issue に起票する。
決定（2026-10-10、平野さん）

* RGN-02 の試験の実行 run 38057059331（#257）のジョブのサマリの表示でよい（チャット側が Chrome で開き、「生成に失敗して飛ばしたページ(#533)」の見出しと `jpml_test` の行を確かめ、平野さんが OK とした）
* 試験のコミットで本番の sitemap の video_en.html の lastmod が 2026-10-10 になった件は、このままでよい
* ubuntu-latest の Ubuntu 26 への移行の件は issue にする

前提（チャット側。平野さんの決定ではない。手順で確かめる）

* #257 の注記（チャット側が Chrome で読んだもの）: 「"The ubuntu-latest label will migrate to Ubuntu 26 beginning October 19, 2026. For more information, see https://github.com/actions/runner-images/issues/14748"」と「Node.js 20 is deprecated. The following actions target Node.js 20 but are being forced to run on Node.js 24: actions/checkout@v4, actions/setup-python@v5.」（要確認。同じ注記が出ているかを、ほかのワークフローの最近の実行でも確かめる）
* 起票で並べる論点の候補（チャット側の案。どれも未決として書く）:
   * (a) `runs-on: ubuntu-latest` のまま移行を受けるか、`ubuntu-24.04` などに固定して自分の時期に上げるか
   * (b) 移行で壊れうる所（Python の版と `setup-python` の指定、`pip install` するパッケージ〈Pillow など〉、ランナーの Chrome を使うジョブ〈check-image-links.yml の saikyo など〉、apt で入れるもの、フォント）
   * (c) Node.js 20 の廃止に合わせて、actions の版（`actions/checkout`・`actions/setup-python`・`actions/github-script` など）を上げるか
   * (d) 移行の前後（10/19 の前後）で確かめる方法（作業ブランチでの手動実行など。docs/notes/branch-operations.md「ワークフローを変更したとき」）
   * (e) mj-logs 側の `.github/workflows/sync-from-mj.yml` と、Worker（`mj-scheduler`）の Workers Builds は対象か（セッションから読めなければ「未確認」と書く）
* issue に期日は書かない（チャット側が調べた結果を見て平野さんと決める）

手順

1. 記録する: 上の「決定」を `docs/decisions/` の合う分野のファイル（`automation.md` の想定）に足す。#533 に、RGN-02 の判断待ち2点が片付いたことをコメントする（閉じない。10/12〈月〉の週次の再生成を見てから閉じる）。
2. 調べる（読むだけ。直さない）: 同じ主題の issue（クローズ済みとコメントを含む。検索語に少なくとも「ubuntu」「runner」「Node.js 20」「node20」「checkout@v」「setup-python」を入れる）と、`.github/workflows/` を変えている未マージの work/ ブランチを確かめる。見つかったら起票せずに止まる。`.github/workflows/` の全ファイルについて、`runs-on`・使っている actions とその版・Python の版の指定・ランナーに入っているものに頼る所（Chrome・apt・フォントなど）を表にする。上の注記を、ほかのワークフローの最近の実行のログ・注記でも確かめる（GitHub MCP か `gh`）。移行の中身（Ubuntu 26 で変わるもの・既定の Python の版など）は、上の runner-images の issue など GitHub の公式の情報で確かめ、読めた範囲と読めなかった範囲を分けて書く。
3. 起票する: 新しい issue を作る。題の案は「Actions: ubuntu-latest の Ubuntu 26 への移行（2026-10-19〜）と Node.js 20 の廃止への対応」（実物に合わせて直してよい）。本文は「何が出ているか」（#257 の注記の引用と、ほかの実行で出ているか）・「今の作り」（手順2の表。ファイルと行を示す）・「移行で変わるもの」（確かめた出典つき）・「壊れうる所」・「論点（未決）」（上の (a)〜(e)。手順2で見つかった論点があれば足す。決めたこととして書かない）・「関係」（#533 など）・末尾に `Chat-Ref: CHAT-1010-RGN-03` の行。ラベルは `分野: 自動化` と、対象のラベルは `gh label list` で合うもの。「状況:」ラベルは付けない。

止まる条件

* CHAT-1010-RGN-02 の状態が「判断待ち」でない
* 同じ主題の issue（クローズ済みを含む）、または `.github/workflows/` の同じ行を変えている、もしくは取り込みで衝突する未マージの work/ ブランチがある
* ログ・`docs/decisions/` 以外（コード・ワークフロー・生成物・ほかの文書）を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（論点を起票した issue に移したら、その番号を「issue」の項目に書き、状態は同節と docs/notes/branch-operations.md「作業ログの寿命」のとおり。10/12 の週次の確かめは #533 で追うので、この指示の未確認の項目にしない）
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RGN-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RGN-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1010-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1010-rgn/docs/logs/CHAT-1010-RGN-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rgn
- 確認用URL: なし
- マージ: 未
- issue: #533
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a930b4a9）: https://github.com/retroeater/mj-logs/tree/main/guide/a930b4a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
