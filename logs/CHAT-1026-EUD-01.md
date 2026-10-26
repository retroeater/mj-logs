# CHAT-1026-EUD-01

- 着手日時: 2026-10-26
- 対象issue: 未起票（手順1で起票）
- ブランチ: work/1026-eud
- 着手時HEAD: bdf27d95b

## 指示

【Claude作成】Claude Code 向け指示：/exam/ と /study/ の動画の構造化データの uploadDate にタイムゾーンを付ける（起票して直し、マージする） Chat-Ref: CHAT-1026-EUD-01 マージ: 承認済み（チャットで。2026-10-26 に平野さんが「差分が構造化データの uploadDate の行とテストだけ・テストが通る、を満たせばプレビューを見ずにマージしてよい」と答えた。条件は下の「止まる条件」） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1026-eud の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1026-eud を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-eud origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-26 に平野さんが Search Console の URL 検査で `https://ryoei.pro/exam/` を見たところ、登録済みだったが、動画の構造化データ（VideoObject）に「重大ではない問題」が2件出ていた:「uploadDate の日時値が無効です（任意）」「日時プロパティ uploadDate にタイムゾーンがありません（任意）」。値は `2026-06-30`（日付だけ）。Mつくで #583 として直したもの（docs/decisions/mtsuku.md の 2026-10-16〈CHAT-1016-VTZ-01〉）と同じ問題が、/exam/ と、同じ作り（`lib/study.py`）の /study/ に残っていると見られる。起票し、直してマージする。
決定（2026-10-26、平野さん）

* /exam/ と /study/ の直しを issue に起票して直す
* `uploadDate` は Mつく（#583）と同じく、公開日の JST の 0 時とタイムゾーンを付けた形（例 `2026-06-30T00:00:00+09:00`）にする
* マージは上の「マージ:」の行のとおり（見た目は変わらないため、プレビューは見ない）

前提（チャット側。平野さんの決定ではない）

* 本番の /exam/ の一覧の JSON-LD の `uploadDate` が日付だけの形であることは、平野さんの Search Console の画面で確かめた（動画 -PoeE0Bm2sE、`2026-06-30`）。/exam/ の各話と /study/（一覧・各話）が同じ形かは未確認（要確認）
* JSON-LD は `lib/mtsuku.py`、層1の読み方は `lib/study.py` が持つ（docs/notes/video-exam.md「共有」の行）。Mつくは #583 で直ったのに /exam/ が日付だけなのは、/exam/・/study/ が `lib/mtsuku.py` に渡す値か経路が違うためと見られる（要確認。原因は実物で確かめてログに書く）
* 帰り道は実際の公開時刻（docs/notes/video-wayhome.md）、Mつくは #583 の形。どちらの生成物も今回は変えない
* 起票のラベルの案: 「種類: 不具合」。案のラベルが無ければ付けずに報告に書く（作るかは判断待ち）

手順

1. 同じ論点の issue（`uploadDate`・`タイムゾーン`・#583 の続き）をクローズ済みとコメントまで含めて検索し、/exam/・/study/ の uploadDate を直す issue が既にあれば止まって報告する。無ければ起票する（題の案: 「/exam/・/study/ の構造化データの uploadDate にタイムゾーンを付ける」。本文に上の「目的」と、#583・#613 への参照）。起票した issue に着手中コメントを書く。`git branch -r --no-merged origin/cloudflare` の各ブランチが、変える関数・ファイルの同じ行を変えている、または取り込みで衝突するかを確かめる。origin/cloudflare の生成物（`exam/`・`study/`・`mtsuku/`・`wayhome/` などの HTML）を `git grep -n '"uploadDate"'` で調べ、日付だけの形の `uploadDate` を出しているページを全部、ページの系統ごとに件数とファイル名の一覧で `## 経過` に表で書く。日付だけの形を作っている箇所（関数と呼び出し元）を特定して書く
2. /exam/ と /study/ の `uploadDate` を決定の形に直す（日付はページに表示している公開日と同じ日付）。Mつく・帰り道の出力は変えない。テストを `scripts/tests/` の該当ファイル（`test_study.py` など）に足す。`python3 scripts/regenerate.py all` を作業ブランチと origin/cloudflare のクローンで回し、差分を種類に分けて `## 経過` に書く。`docs/notes/video-exam.md`・`docs/notes/video-study.md` の今の内容を読んでから `uploadDate` の形を足し、`docs/decisions/exam.md`・`docs/decisions/study.md` にこの指示の決定を足す
3. 止まる条件に当たらなければ docs/notes/branch-operations.md「マージするとき」の手順でマージする。マージ後、本番で `https://ryoei.pro/exam/` と /exam/ の各話1枚、`https://ryoei.pro/study/` と各話1枚の JSON-LD の `uploadDate` が `+09:00` 付きであることを確かめる（URL に `?v=<未使用の値>` を付ける）。15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする。起票した issue に結果（ログの `## 経過` の差分の表を引用）を書いてクローズする

止まる条件

* 手順1で同じ論点の issue が見つかった、または起票した issue に他セッションの着手中コメントがある
* 手順1で /exam/・/study/ 以外にも日付だけの `uploadDate` を出しているページがあった: そのページは直さず、一覧を issue と報告に書く（止まらずに /exam/・/study/ の直しを進めてよい）
* 未マージの work/ ブランチが、変える関数・テストの同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。/exam/ と /study/ のどちらかだけが重なるなら、重なる方を外して残りを進め、外したものと理由を報告に書く。両方が重なるときだけ止まる）
* 生成物の差分（データの変化で両方の木で同じく変わるものを除く）に、`exam/`・`study/` の JSON-LD の `uploadDate` の行以外の変化がある（Mつく・帰り道など他のページの変化を含む）。テスト・docs の変更は差分に含めてよい。止まるときは想定外の行を全部書く
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* マージ後の check-run・`regenerate-page.yml` の失敗: 今回の変更による失敗なら原因を書いて止まる。無関係な失敗なら原因を報告に書いたうえで、本番の確かめと issue への記録・クローズを進めてよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-EUD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-EUD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d1ef6e5）: https://github.com/retroeater/mj-logs/tree/main/guide/0d1ef6e5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdf27d95.md
