# CHAT-1103-TOP-04

- 着手日時: 2026-11-03
- 対象issue: #101
- ブランチ: work/1103-top-hosho
- 着手時HEAD: 795228a73

## 指示

【Claude作成】Claude Code 向け指示：鳳匠戦を title/ に加える下準備（`_redirects` の /title/hosho と検査、記録0件の大会の確かめ） Chat-Ref: CHAT-1103-TOP-04 マージ: 承認済み（チャットで）。条件: (1) テスト（`python3 -m unittest discover -s scripts/tests`）が通る (2) cloudflare との差分が `_redirects` の /title/hosho の行・検査・docs（docs/decisions・docs/notes・docs/logs）だけ (3) 全ページの再生成（`regenerate-page.yml` の作業ブランチでの手動実行、`target_page: all`）で生成物の差分が0件。1つでも満たさなければマージせずに止まる 貼る時機: いつでも（CHAT-1103-TOP-03 と並行。別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top-hosho の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-top-hosho を使う（work/1103-top は TOP-03 が使っているので分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-top-hosho origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
鳳匠戦を title/ のタイトル戦に加える。大会の正本は「タイトル戦」タブ（平野さんが行を足す）なので、この指示では先に `_redirects` と検査を入れ、記録が0件の大会でも生成が止まらないことを確かめてマージし、平野さんが足す行の値を報告する（逆の順だと翌朝の生成や検査が止まるおそれがあるため）。
決定（2026-11-03、平野さん。TOP 系列のチャットで）

* 鳳匠戦をタイトル戦（title/）に加える。表示順は鸞和戦の次、slug は `hosho`
* 進め方: Code が先に `_redirects` の行・検査を入れてマージし、その後で平野さんが「タイトル戦」タブに1行足す
* マージは上の3つの条件をすべて満たしたときだけ（2026-11-03、チャットで別に聞いて「マージしてよい」）
* 背景: 新トップ（#101）の大会日程は title/ に載っている大会の放送対局だけを出すため、2026-11-20 の「第1期鳳匠戦決勝」が今は出ない

前提（チャット側。平野さんの決定ではない）

* docs/notes/title-pages.md（mj-logs のガイドで読んだ）: 大会マスタは「タイトル戦」タブ（大会名 / slug / 表示順 / 区分 / 状態）。止める条件に「slug の重複・不正・空欄（記録の無い大会の空欄だけは警告）」「表示=Y の行の大会名が大会マスタに無い」ほか。`/title/<slug> /title/<slug>/ 301` は大会ごとに手で1行（`scripts/tests/test_title_redirects.py` が検査、20行）。`/title/:slug/` の動的ルールはファイルの末尾。改名は `scripts/lib/taikai_names.py` の `RENAMED`
* 鳳匠戦は予定表の取り込み・/live ではすでに大会として扱われている（docs/decisions/live.md「達人戦・昇龍戦・鳳匠戦を /live に載せる」、docs/notes/yotei-sheet.md に鳳匠戦の記述。`yotei.EVENTS` に語があるかは要確認）
* 「タイトル」タブ（決勝の記録）には鳳匠戦の行はまだ無い見込み（第1期の決勝は 2026-11-20）。記録0件の大会を大会マスタに足したとき、title/ の入口・大会のページ・`title/search.json` の `t`（新トップが「title/ に載っている大会」の判定に使う）がどうなるかは要確認
* 区分・状態の値は、鸞和戦（表示順の直前）の行と同じ形を案にする（要確認。実物の値を報告し、違う値にすべき理由があれば書く）

手順

1. 同じ論点の issue（「鳳匠戦」「hosho」）をクローズ済みも含めて検索し、関係するものを `## 経過` に書く（無ければ起票はしない。作業の記録は #101 にコメント）。`generate_title_pages.py` の大会マスタの読み方・止める条件・警告と、`test_title_redirects.py` が何と何を照合しているか（`_redirects` の行の集合と大会マスタ／生成物のどちらか、両向きか）を読み、`## 経過` に書く。記録0件の大会を大会マスタに足したときの生成の振る舞い（止まる・警告・入口や大会のページや `search.json` に出るか）を、シートを読まない検査（既存のテストの形の入力）で確かめる
2. `_redirects` に `/title/hosho /title/hosho/ 301` を既存の大会の行と同じ場所・形で足し、検査を必要なだけ直す（大会マスタに行がまだ無い段階でも検査が通る形。通らないなら、大会マスタの側に行が無い状態で何が落ちるかを書いて止まる）。`docs/notes/title-pages.md` の「20行」などの件数と `docs/decisions/title.md` を直す。#101 に着手中のコメント（作業ブランチ名を書く。TOP-03 の着手中と並ぶので、この指示の範囲を一文で）
3. マージの条件 (1)〜(3) を確かめ、満たせば `git push origin work/1103-top-hosho:cloudflare` でマージする（push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD`）。マージ後、本番の `/title/hosho` が 301 を返すかを確かめる（15分で反映されなければ「未確認の項目」に回して完了にする）。最後に、平野さんが「タイトル戦」タブに足す1行の値（大会名・slug・表示順・区分・状態。表示順は鸞和戦の次に入れるために、ほかの行の表示順を変える必要があるかも）と、足した後に起きること（翌朝 04:00 JST の再生成で title/hosho/ ができるか、新トップの大会日程に 11/20 の決勝が載る条件）を `## 報告` の「判断が必要なこと」に書く

止まる条件

* 鳳匠戦がすでに大会マスタ・`_redirects` にある、または同じ目的の未マージ work/ ブランチ・open issue がある
* 記録0件の大会を大会マスタに足すと生成が止まる（コードの直しが要る）。直しの案を書いて止まる（マージの条件 (2) の範囲を超えるため）
* 大会マスタに行が無い段階で `_redirects` の行を足すと検査が落ち、検査の直しで済まない
* 未マージの work/ ブランチが `_redirects` の title/ の行の近く・`scripts/tests/test_title_redirects.py`・`docs/notes/title-pages.md` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージの条件のどれかを満たさない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d2277ebe）: https://github.com/retroeater/mj-logs/tree/main/guide/d2277ebe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
