# CHAT-1011-SWP-18

- 着手日時: 2026-10-11
- 対象issue: #227（G5-07）
- ブランチ: work/1011-swp-hnd
- 着手時HEAD: a24c58c02

## 指示

【Claude作成】Claude Code 向け指示：横断レビューの申送り — 指示文の雛形に2つの規則、design.md に数字の列の規則を足し、sitemap の冒頭コメントから件数を消す（G5-07） Chat-Ref: CHAT-1011-SWP-18 マージ: 承認済み（チャットで。2026-10-11、平野さん。変わるのは文書と sitemap の冒頭コメントだけ）。下の「止まる条件」に当たったらマージしない 貼る時機: いつでも（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。work/1011-swp-hnd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-swp-hnd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-swp-hnd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
横断レビュー（CHAT-1006-SWP-01〜CHAT-1011-SWP-17）の振り返りで決めた申送りを、文書と sitemap に書き残す。
決定（2026-10-11、平野さん）

* 振り返りの提案を推奨どおり採る:
   * (1) 表の数字の列は `font-variant-numeric: tabular-nums` を指定して数字を等幅にし、列の幅は `ch` で決める（`ch` は「0」の幅。等幅にしないと「4」などで折り返す。CHAT-1011-SWP-16 で `rh_results_detail` の着順がこれで折り返した）
   * (2) 見た目を直す指示で、受け入れた値（ページの長さ・高さ・サイズなど）が既にあるときは、その値を止まる条件に写す
   * (3) `git diff --stat` で生成物が +10% を超えて大きくなる直しは、マージの指示でなくプレビューの指示の段階で計測させる
   * G5-07（sitemap の冒頭コメントの件数が古い）は、件数そのものをコメントから消して解く（件数を書かなければ古くならない）
* `rh_results_detail` の着順の列に (1) を今すぐ当てるかは、この指示では決めない（見た目が少し変わるので、当てるなら別の指示でプレビューを見てから）

前提（チャット側。平野さんの決定ではない）

* 書き先と残り容量:
   * `docs/instruction-template.md`（上限 16KB・警告域 14KB、CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）: cloudflare の `cc2de5f` で 13,546 バイト。(2) と (3) を足しても警告域（14,336 バイト）を超えないよう、規則と理由の一句だけを書く。超えるなら足さずに止まる
   * `docs/notes/design.md`（上限なし）: (1) を「4. 既決の値」の「表の数字の列（`rh_results_detail`）」の行の近くに、既決の値として足す。今の行（`min-width: calc(3.2em + 8px)`）は消さず、「等幅にしていないため `em` で決めている」旨を書き添える
   * `sitemap.xml`・`sitemap-pages.xml` の冒頭コメント: 「27ページ中25件」「4ファイルを束ねる」のような件数を消し、件数を持たない言い方にする。`<loc>`・`lastmod` などコメント以外は変えない（lastmod は手で書き換えない。CLAUDE.md 禁止事項）
   * 事例（SWP-08・SWP-11・SWP-16 の経緯）は、`docs/notes/handover-archive-2026.md` の「外した記録」の形式に合わせて1行ずつ足す（他の文書には事例を書かない）
* (2) の規則の案: 「見た目を直す指示で、受け入れた値（ページの長さ・高さ・サイズなど）が既にあるときは、その値を止まる条件に写す（超えたときの扱いが無いと、マージ後に判断待ちが残る）」
* (3) の規則の案: 「`git diff --stat` で生成物が +10% を超えて大きくなる直しは、プレビューの指示の段階で計測させる（マージの指示で初めて測ると、止まったときの往復が増える）」。今ある「計測を伴う指示は…」の項の近くに置く
* (1) の規則の案: 「表の数字の列は `font-variant-numeric: tabular-nums` で等幅にし、列の最小幅は `ch` で決める。等幅にしない列は `em` で決め、幅の広い数字でも折り返さないことを確かめる」。`style.css` には既に `tabular-nums` の指定が3箇所ある（行は実物で確かめる）
* 追記先の今の内容を読み、同じ項目があれば置き換える。文体は追記先に合わせる
* `python3 -m unittest discover -s scripts/tests` を通す（sitemap を読むテストがあれば通ることを確かめる）
* #227 に G5-07 の解き方（件数を消した）をコメントする。SWP-03 が G5-07 を #227 にコメントしているので、そのコメントへの返事の形でよい
* マージで入る見込みの差分は、上の4つの文書・`sitemap.xml`・`sitemap-pages.xml`・docs/logs/・docs/decisions/ だけ。CSS・HTML・JS は変えない
* 使う skill は無い

手順

1. 追記先の4つの文書と2つの sitemap の今の内容を読み、容量を測る（`wc -c`。作業ブランチの上で）
2. 上の前提のとおり足す・直す。`docs/instruction-template.md` の足した後の大きさを書く
3. CLAUDE.md「ブランチ運用」の「マージの手順」のとおり、push の直前に再 fetch し、祖先を確かめてから `git push origin work/1011-swp-hnd:cloudflare` する。マージ後、本番の `sitemap-pages.xml` を取得して冒頭コメントが新しい版であることを確かめる（待つのは15分まで）。#227 にコメントする（末尾に `Chat-Ref: CHAT-1011-SWP-18`）
4. 上の「決定」を `docs/decisions/site-review.md` に足す
5. 作業ブランチの片付けは docs/notes/cloud-sessions.md「ブランチの削除」と docs/notes/branch-operations.md「ブランチを削除するとき」に従う（自動の削除の対象ならそれに任せてよい）

止まる条件

* `docs/instruction-template.md` が、足した後に警告域（14,336 バイト）を超える（足さずに、超える分を書いて止まる）
* マージの対象の差分が、上の見込みの範囲を超える（CSS・HTML・JS・sitemap のコメント以外が変わる）
* 未マージのブランチが、この指示で変える行・節と同じところを変えている、または取り込みで衝突する（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-SWP-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-SWP-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1011-SWP-18"` に該当なし。識別子 SWP は同じチャットの SWP-01〜17 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1011-swp-hnd origin/cloudflare`（上流の設定は外した）。


### 手順1 読む・測る

- 作業ブランチの上で `wc -c`: `docs/instruction-template.md` 13,546、`docs/notes/design.md` 15,432、`docs/notes/handover-archive-2026.md` 89,089、`docs/decisions/site-review.md` 11,563、`sitemap.xml` 1,133、`sitemap-pages.xml` 3,710 バイト。
- 未マージのブランチ（`work/1009-nen`・`work/1009-swp-526`・`work/1011-abm`・`work/1011-swp-nav`）は、この指示の6ファイルをどれも変えていない。
- `style.css` の `tabular-nums` は、前提の「3箇所」ではなく **9箇所**（18・1314・2255・2789・3336・4148・4222・4244・4301 行）。規則の書き方には影響しないため、design.md には「9 箇所」と書いた。
- 追記先に同じ項目は無かった（雛形の「計測を伴う指示は…」の項はあったので、その子の項目として足した）。

### 手順2 足す・直す

- `docs/instruction-template.md`: (3) を「計測を伴う指示は、環境・回数・判定を最初の指示で決めて書く」の子の項目の最後に、(2) を「issue を変えない読むだけの指示でも…」の項の前に、前提の案の文のとおり1行ずつ足した。**足した後 14,013 バイト**（警告域 14,336 バイトの内）。
- `docs/notes/design.md`「4. 部品」の表: 「表の数字の列（全般）」の行を「表の数字の列（`rh_results_detail`）」の行の直前に足した（`tabular-nums` で等幅・最小幅は `ch`、等幅にしない列は `em` で決めて幅の広い数字でも折り返さないことを確かめる）。`rh_results_detail` の行は消さず、着順の `calc(3.2em + 8px)` に「数字を等幅〈`tabular-nums`〉にしていないため `ch` でなく `em` で決めている」を書き添えた。
- `sitemap.xml`・`sitemap-pages.xml` の冒頭コメント: 「配下の4ファイル」「静的HTML25ページ」「38枚」「27ページ中25件」「この2ファイル」を、件数を持たない言い方にした（「件数は書かない。書くと古くなる」を添えた）。`<loc>`・`<lastmod>` は変えていない（`git diff` はコメントの行だけ）。2ファイルとも XML として読めることを確かめた。
- `docs/notes/handover-archive-2026.md`: 「2026-09-28 の整理で外した記述（#297）」の後に「2026-10-11 横断レビュー（#524〜#526）の振り返りで規則にした事例」の節を足し、SWP-08・SWP-11・SWP-16 の経緯を1行ずつ書いた。
- 決定を `docs/decisions/site-review.md`（2026-10-11〈CHAT-1011-SWP-18〉）に足した。
- `python3 -m unittest discover -s scripts/tests` は通った（sitemap を読むテストは `test_regenerate.py` が仮のファイルで使うだけ）。

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1011-swp-hnd
- ログ: https://github.com/retroeater/mj/blob/work/1011-swp-hnd/docs/logs/CHAT-1011-SWP-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-swp-hnd
- 確認用URL: なし
- マージ: 未
- issue: #227
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c5e3ebdb）: https://github.com/retroeater/mj-logs/tree/main/guide/c5e3ebdb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5e3ebdb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5e3ebdb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5e3ebdb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5e3ebdb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5e3ebdb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5e3ebdb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
