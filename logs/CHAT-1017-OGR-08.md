# CHAT-1017-OGR-08

- 着手日時: 2026-10-17
- 対象issue: #581
- ブランチ: work/1017-ogr
- 着手時HEAD: c859c26c8

## 指示

【Claude作成】Claude Code 向け指示：最強戦の卓・対局のページの og:title から日付を外す（#581）
Chat-Ref: CHAT-1017-OGR-08
マージ: 承認済み（チャットで）。ただし「止まる条件」の差分の範囲を外れたら止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-ogr の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

## 目的
最強戦の卓のページの og:title が長く、X のカードの帯で「…麻雀最…」と切れる（#581 の残りの課題、2026-10-16 に平野さんが X で確認）。卓・対局のページの og:title から日付を外して短くする。あわせて、#549 から #581 に移した「対局のページのカードの見え方の確認」に使う本番の og:title をログに残す。

### 決定（2026-10-17、平野さん）
- 最強戦の卓・対局のページの og:title から日付「（YYYY年M月D日）」を外す。例: 卓「ベスト32 A卓 | 女流プレミアトーナメント 華麗な技 | 麻雀最強戦2019 | ryoei.pro」、対局「女流プレミアトーナメント 華麗な技 | 麻雀最強戦2019 | ryoei.pro」（「ページ | 親 | 区分 | ryoei.pro」の並び。title/ の og:title と同じ考え方）
- 変えるのは og:title だけ。`<title>`（検索結果に出る文）・h1・パンくず・共有ボタンの文言は今のまま（日付を残す）
- 同じ年度に同じ名前の対局は無い（平野さん）。日付を外しても og:title は重ならない
- 年度のページ・トップの og:title は変えない
- プレビューを見ずにマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- 2026-10-17 にチャット側が origin/cloudflare の `scripts/generate_saikyo_pages.py` を読んだ範囲: 卓のページは `title=f"{table.heading} | {title} | {EVENT_NAME}{year} | ryoei.pro"`、対局のページは `title=f"{title} | {EVENT_NAME}{year} | ryoei.pro"`（`title` は `match_title()` で「対局名（日付）」）。og:title を別に決めていないため `<title>` と同じ文になっている。`PageMeta.og_title` に日付を除いた文を渡す見込み（要確認）
- 卓のページは 643件（CHAT-1016-OGR-05・06 のログ）。対局のページの件数は手順1で数える
- SKR 系列のブランチが最強戦の生成スクリプトを触っていることがある

## 手順
1. 卓・対局のページの og:title を、日付を除いた形で `PageMeta.og_title` に渡す（`match.name` を使う・`match_title()` から日付を外す、など実物に合わせてよい）。再生成し、テストを通す。卓・対局のページの件数、og:title が同じ文になるページの組が無いこと（年度をまたがない範囲で重複0）、og:title が長い順に5件をログに書く
2. 記録: #581 に上の「決定」の5点（2026-10-17）をコメントで書く。docs/decisions/saikyo.md に足し、docs/notes/saikyo-page-design.md・docs/notes/ogp.md に og:title の形の記述があれば直す（追記先の今の内容を読み、矛盾していて判断が要るときだけ止まる）
3. cloudflare へマージする。デプロイの後（本番の HTML が新しくなったことを確かめてから）、本番の `saikyo/2019/20190223-s1-a.html`（卓）と、その対局のページ、ほかの年度の卓1つの og:title・`<title>` を `?v=<未使用の値>` 付きで取得してログに引用する。対局のページの URL は最終報告にも書く（平野さんが X・LINE で見るため）。待ちの上限を超えたら未確認の項目に回してよい

## 止まる条件
- 差分（docs/ を除く）が、卓・対局のページの og:title の行（件数をログに書く）と、生成スクリプト・テストの変更の範囲を外れる。生成のたびに入るシートの変化で説明できる差分は、内容をログに書いて進めてよい。説明できなければ止まる
- `<title>`・h1・パンくず・共有ボタンの文言が変わる差分が出た
- og:title が同じ文になるページの組がある（一覧を書いて止まる）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が、`generate_saikyo_pages.py` の卓・対局のページの `PageMeta` を作る同じ行・同じ関数を変えている、または取り込みで衝突する
- マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係なら原因を報告に書いたうえで残りの手順を進めてよい。自分の変更で落ちると分かっているテストは直してよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（マージ後の自動の再生成〈regenerate-page.yml〉で変わる範囲も、上の差分の範囲で見る）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-OGR-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-OGR-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `CHAT-1017-OGR-08` のコミット: 無し（`OGR` は同じセッションの続き）
- 作業ブランチ: ローカル・リモートとも `work/1017-ogr` はマージ済み。`git merge --ff-only origin/cloudflare`（9d9b16c51 → c859c26c8）
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有る
- 重なり: 未マージの `work/` で `generate_saikyo_pages.py` を変えているものは無い

### 前提の確かめと実装

- 前提どおり: `write_year_sub_pages()`（`generate_saikyo_pages.py`）の対局 `PageMeta(title=f"{title} | {EVENT_NAME}{year} | ryoei.pro")`・卓 `PageMeta(title=f"{table.heading} | {title} | …")`。`title` は `match_title()`（「対局名（日付）」）で、og_title は渡していなかった
- 対局は `og_title=f"{match.name} | {EVENT_NAME}{year} | ryoei.pro"`、卓は `og_title=f"{table.heading} | {match.name} | {EVENT_NAME}{year} | ryoei.pro"` を足した（`match.name` は日付を含まない対局名）。`<title>`・description・og:image:alt は日付を含むまま
- テスト: `python3 -m unittest discover -s scripts/tests` OK（og:title の期待値を持つ最強戦のテストは無く、直していない）

### 再生成と差分

- `python3 scripts/regenerate.py saikyo_pages`: 成功
- 差分（docs/ を除く）: og:title の行だけが **862ページ**（卓 643・対局 219）。年度・トップは変化なし。`<title>`・h1・パンくず・ほかの行は変わらない。シートの変化による差分は無し。スクリプト1つ
- 重複: 卓・対局 862ページの og:title に同じ文は**無い**（全年度で重複0）
- og:title が長い順の5件（字数）:

| 字数 | 種類 | ページ | og:title |
|---|---|---|---|
| 66 | 卓 | saikyo/2020/20200906-s1-a.html | ベスト8 A卓 \| 女流プロ代表決定戦 タイトルホルダーvsMリーガー最強の女流決戦 \| 麻雀最強戦2020 \| ryoei.pro |
| 66 | 卓 | saikyo/2020/20200906-s1-b.html | ベスト8 B卓 \| 女流プロ代表決定戦 タイトルホルダーvsMリーガー最強の女流決戦 \| 麻雀最強戦2020 \| ryoei.pro |
| 61 | 卓 | saikyo/2020/20200906-s2.html | 決勝 \| 女流プロ代表決定戦 タイトルホルダーvsMリーガー最強の女流決戦 \| 麻雀最強戦2020 \| ryoei.pro |
| 60 | 卓 | saikyo/2017/20170520-s1-a.html | ベスト32 A卓 \| 女流プレミアトーナメント代表決定戦 真・女王決定戦 \| 麻雀最強戦2017 \| ryoei.pro |
| 60 | 卓 | saikyo/2017/20170520-s1-b.html | ベスト32 B卓 \| 女流プレミアトーナメント代表決定戦 真・女王決定戦 \| 麻雀最強戦2017 \| ryoei.pro |

- 例: `saikyo/2019/20190223-s1-a.html` 「ベスト32 A卓 | 女流プレミアトーナメント 華麗な技（2019年2月23日） | 麻雀最強戦2019 | ryoei.pro」→「ベスト32 A卓 | 女流プレミアトーナメント 華麗な技 | 麻雀最強戦2019 | ryoei.pro」

### 記録

- #581 に決定の5点をコメントした
- docs/decisions/saikyo.md に OGR-08 の節を足した。docs/notes/saikyo-page-design.md の新しい年度・対局・卓のページの節（「題名と説明はページごと」）に og:title の形を書いた。
  同じファイルの「OGP は年度ページ共通のままで、対局ごとの出し分けはしない」（`?match=` の共有の説明、SK-19）は旧年度ページの対局の共有ボタンの話で、今回の対局のページとは別なので変えていない。docs/notes/ogp.md には最強戦の og:title の形の記述が無い

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 95555949）: https://github.com/retroeater/mj-logs/tree/main/guide/95555949

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13c870a9.md
