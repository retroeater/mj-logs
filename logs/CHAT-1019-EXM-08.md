# CHAT-1019-EXM-08

- 着手日時: 2026-10-19
- 対象issue: なし（#570・#613 の申送り）
- ブランチ: work/1019-exm
- 着手時HEAD: a33a81859

## 指示

【Claude作成】Claude Code 向け指示：EXM の申送り（公開後のインデックス登録の確認をカレンダーに作る項目をチェックリストに足す）
Chat-Ref: CHAT-1019-EXM-08
マージ: ドキュメントのみ（docs/ と docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-exm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-exm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
EXM 系列（/exam/ の作成と公開、CHAT-1019-EXM-01〜07）の振り返りと Fable 5.1 のレビューを受けた申送り。公開後に Search Console の登録の結果を確かめる作業が抜けないよう、`docs/new-page-checklist.md` の段2に1行足し、Mつくの公開時の決定を置き換える。

### 決定（2026-10-19、平野さん）
- 公開後のインデックス登録の結果を確かめる予定は、カレンダー「ryoei.pro」に作る（/exam/ では 2026-10-26 の予定を作った）。`docs/decisions/mtsuku.md` 2026-10-15（CHAT-1015-MTK-11）の「Search Console の登録の結果を待つ作業は作らない」を置き換える
- `docs/new-page-checklist.md` 段2「公開後の確かめ」の「平野さんの作業: Search Console で `sitemap.xml` を再送信し、入口を URL 検査でインデックス登録をリクエスト」の行の直後に、次の1行を足す（文面は Fable 5.1 の案のまま）:
  「チャット側: 登録の結果を確かめる予定（1週間後、終日、件名【R#公開の issue】）をカレンダー「ryoei.pro」に作る。結果を待つ作業は issue にしない」
- 振り返りの提案5（Mつくの公開日・再生時間を YouTube の取り込みデータから取る形にそろえる）は見送る。Mつくは 2026-10-15 の決定（YouTube の情報は使わず、シートに手で入れる）のまま

### 前提（チャット側。平野さんの決定ではない）
- 追記はガイド文への規則の追加にあたるとみなし、下の表を付ける（CLAUDE.md「Chat-Ref」節）

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| 公開後に Search Console の登録の結果を確かめる作業が抜ける（Mつく〈#570〉では作らないと決め、/exam/〈#613〉で平野さんがカレンダーへの登録を求めた） | ガイド文（`docs/new-page-checklist.md` 段2） | 計算的センサー（テスト・hook・ワークフロー）では、チャット側がカレンダーに予定を作ったかを確かめられない。推論的センサーにすると Code の作業になるが、カレンダーを作るのはチャット側。段2は公開の指示を書くときにチャット側が必ず読む文書なので、ここに置く |

- 2026-10-19 にチャット側が読んだ版（mj-logs の guide/405e702e）の段2「公開後の確かめ」に、上の「平野さんの作業: Search Console で…」の行がある（要確認）

## 手順
1. 追記先の今の内容を読む: `docs/new-page-checklist.md` 段2「公開後の確かめ」、`docs/decisions/mtsuku.md` の 2026-10-15（CHAT-1015-MTK-11）の節、`docs/decisions/exam.md`。上の前提の行が無い・同じ趣旨の行がすでにある・矛盾する記述があれば、その旨を書いて止まる（同じ趣旨の記述があるだけなら置き換え・拡張してよい）。`docs/new-page-checklist.md` の今のサイズを測って `## 経過` に書く（上限のある文書かも書く）
2. `docs/new-page-checklist.md` に決定の1行を足す。`docs/decisions/mtsuku.md` の「Search Console の登録の結果を待つ作業は作らない」に「→ 置き換え: 2026-10-19（CHAT-1019-EXM-08）」の注記を付ける（docs/decisions/README.md の書き方に合わせる）。`docs/decisions/exam.md` に、この指示の決定（3つ）を足す
3. 変更が `docs/` だけであることを `git diff --stat origin/cloudflare...HEAD` で確かめ、ログを仕上げて cloudflare へ入れる

## 止まる条件
- 手順1の前提が食い違う、追記先と矛盾していてどちらが正か判断が要る
- 変更が `docs/` 以外に及ぶ
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep=CHAT-1019-EXM-08` と `docs/logs/` の履歴に無い
- 作業ブランチ: ローカル・リモートの `work/1019-exm` は cloudflare にマージ済み。`git merge --ff-only origin/cloudflare` で進めた（a33a81859）
- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。ガイド文への追記の「知見 → 選んだ層 → 理由」の表は指示文にある

### 手順1: 追記先の確かめ

- `docs/new-page-checklist.md` 段2「公開後の確かめ」の86行目に「平野さんの作業: Search Console で `sitemap.xml` を再送信し、入口を URL 検査でインデックス登録をリクエスト」がある（前提と一致）。同じ趣旨（登録の結果の確かめ・カレンダー）の行は無い
- サイズ: 13,916 バイト（追記後 14,121 バイト）。`check-assets.yml` が上限を見る4文書（CLAUDE.md・handover.md・chat-side-operations.md・instruction-template.md）には入っておらず、上限は無い
- `docs/decisions/mtsuku.md` 2026-10-15（CHAT-1015-MTK-11）に「#570 をクローズし、Search Console の登録の結果を待つ作業は作らない」がある
- `docs/decisions/exam.md` 2026-10-19（CHAT-1019-EXM-07）にも「登録の結果を待つ作業は作らない。Mつく〈#570〉と同じ」がある（EXM-07 の決定をそのまま写したもの）。今回の決定で同じく置き換わるため、こちらにも置き換えの注記を付けた（指示文は mtsuku.md だけを挙げていた。判断の要る矛盾ではなく、同じ決定の置き換えとして扱った）

### 手順2: 追記

- `docs/new-page-checklist.md`: 86行目の直後に「- [ ] チャット側: 登録の結果を確かめる予定（1週間後、終日、件名【R#公開の issue】）をカレンダー「ryoei.pro」に作る。結果を待つ作業は issue にしない」（決定の文面のまま。周りの行に合わせてチェックボックスを付けた）
- `docs/decisions/mtsuku.md`・`docs/decisions/exam.md`（EXM-07）の該当行の末尾に「→ 置き換え: 2026-10-19（CHAT-1019-EXM-08）」（docs/decisions/README.md の書き方）
- `docs/decisions/exam.md` にこの指示の決定3つを足した

### cloudflare の取り込み

- 最初は追記の後に `git merge origin/cloudflare` で取り込もうとして、`docs/decisions/exam.md` が衝突した。別のセッション（CHAT-1019-ENG-02、596a114d4、#617）が EXM-07 の節の2行目（「テスト」シートの行）に「英語」シートの削除を書き足していて、こちらの置き換えの注記（1行目）と隣り合っていた
- 末尾への追記どうしではないので衝突は解かずに `git merge --abort` で戻した。追記の2コミットは push 前だったので `git reset --keep` で push 済みのログのコミット（ec7e1f947）まで戻し、先に cloudflare を取り込んで（2b09343da、衝突なし）、取り込んだ後の文書に同じ3つの追記をやり直した。ENG-02 の書き足しはそのまま残っている

## 報告

- 状態: 完了
- ブランチ: work/1019-exm（docs のみ。cloudflare へ入れた）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-EXM-08.md
- 比較URL: なし（docs のみ）
- 確認用URL: なし
- マージ: 済（docs のみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 43121c2a）: https://github.com/retroeater/mj-logs/tree/main/guide/43121c2a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/43121c2a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/43121c2a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/43121c2a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/43121c2a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/43121c2a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/43121c2a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0a1e266.md
