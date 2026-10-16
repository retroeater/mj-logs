# CHAT-1017-SKR-16

- 着手日時: 2026-10-17
- 対象issue: #549、#581
- ブランチ: work/1017-skr-rec
- 着手時HEAD: f81b95961

## 指示

【Claude作成】Claude Code 向け指示：最強戦の X 告知の取りやめの記録と、SKR の振り返りで決めた比較ページの作り方・スクリーンショットの置き場所の規則（文書と issue だけ）
Chat-Ref: CHAT-1017-SKR-16
マージ: ドキュメントのみ（docs/notes/・docs/decisions/・ログ・issue のコメント）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（新しいセッションでも、SKR-15 の続きでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-skr-rec の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-skr-rec を使う。リモートに無ければ origin/cloudflare から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
最強戦の公開（#549、2026-10-16）の後に平野さんが決めた「X の告知の取りやめ」を記録する。あわせて、SKR の振り返り（2026-10-16〜17、Fable 5.1 のレビュー済み）で決めた2つの規則を文書に足す。コードは変えない。

### 決定（2026-10-16〜17、平野さん）
1. 最強戦のリニューアルの X 告知は取りやめる（鳳凰戦の告知と時期が重なるため）。告知動画（CHAT-1016-SKR-05 で確定した `saikyo-promo-b.mp4`、平野さんが保存済み）は、別の機会にアピールするときに使う。2026-10-15（CHAT-1015-SKR-01）の #549 の公開の順番の「(5) 平野さんが X に投稿する」を置き換える
2. #549 のチェックリストの「X と LINE のカード（og:image が ABEMA の作品の画像で出るか）」は OGP の話なので、OGR（#581、OGP 画像の横断レビュー）で扱う。#549 では扱わない
3. Code が確かめのために撮ったスクリーンショットは、チャット側が見られるようにリポジトリ（作業ブランチの docs/logs/ の横）か issue に置いてよい（チャット側が Chrome を使えない間、プレビューを文字でしか確かめられなかったため）
4. 見た目の比較ページでは、見た目の分かれ目（置き場所・形・色など）を Code の「自然な方」に委ねず、分かれ目ごとに案を並べる。印（ピル・帯など）がどの要素のものかが問われる形では、上下・左右の要素との空き（px）を測って表に書く（SKR-10 でピルの中・外を Code に委ねて外になり、iPhone で見ると上下どちらのカードの結果か迷う形になった。SKR-13 で置き場所を5案並べ、空きを px で測って決め直した）

### 前提（チャット側。平野さんの決定ではない）
- 決定 1・2 は `docs/decisions/saikyo.md` に新しい節（2026-10-17、CHAT-1017-SKR-16）として足し、CHAT-1015-SKR-01 の節の「(5) 平野さんが X に投稿する」の行の末尾に「→ 置き換え: 2026-10-17（CHAT-1017-SKR-16）」を付ける（同ファイルの既存の書き方に合わせる）
- 決定 3 の書き場所の案: `docs/notes/chat-side-operations.md`「確認用 URL」の節（プレビューの URL をログ・issue に書かない規則〈#363〉がある所）に、「撮った画像は置いてよい。URL とは別」と一句足す。置き方は要確認: docs/logs/ の横に置くなら `docs/logs/img/<Chat-Ref>/` のような1か所に決め、mj-logs の `sync-from-mj.yml`（#440・#298）が画像も写すか確かめる。写さないなら、issue にコメントで貼る方を主にする（issue の画像は GitHub が保管する）。どちらを主にしたかと理由をログに書き、chat-side-operations.md には選んだ方だけを書く。純増した分は chat-side-operations.md の冒頭の規則に従い、前後のサイズを測ってログに書く
- 決定 4 の書き場所の案: `docs/notes/design.md`「7. チャット側の決め方」（比較ページの作り方がある所）に規則と理由の一句で足す。2026-10-14 の DSN の振り返りの「見比べたい分かれ目は図か比較ページで並べてから聞く」と同じ趣旨なので、その行があれば拡張し、無ければ足す
- 決定 1〜4 は、ガイド文（docs/notes/）と decisions に書く。知見の層の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 比較ページでは分かれ目を Code に委ねず案を並べ、所属が問われる印は空きを px で測る | ガイド文（docs/notes/design.md） | チャット側の指示の書き方で、テスト・hook では確かめられない |
| Code のスクリーンショットはリポジトリか issue に置いてよい | ガイド文（docs/notes/chat-side-operations.md） | 置き場所の取り決めで、仕組みにするものではない |

- issue のコメント（各コメントの末尾に `Chat-Ref: CHAT-1017-SKR-16`）:
  - #549（閉じたまま）: X の告知は取りやめ（決定 1）。チェックリストの「X と LINE のカード」は #581 で扱う（決定 2）。本文のチェックリストを直せるなら、その2項目に取り消し線か「→ 取りやめ」「→ #581」を付ける
  - #581: 「最強戦の卓・対局のページを X・LINE に貼ったときのカードの見え方（og:image が出るか）の確認は、#549 からこちらへ移した」と足す。#581 に着手中のコメント（未解除）があれば本文は触らずコメントだけにする

## 手順
1. 同じ論点の issue（「スクリーンショット」「画像の置き場所」「比較ページ」）を、クローズ済みも含めて検索し、同じ論点の open issue があれば止まって報告する。追記先（saikyo.md・chat-side-operations.md「確認用 URL」・design.md「7.」）の今の内容を読む。`git branch -r --no-merged origin/cloudflare` の各ブランチがこれらの節を変えていないか確かめる。mj-logs の `sync-from-mj.yml` が画像を写すかを確かめる
2. 上の前提のとおり書く。issue にコメントする
3. ログを仕上げ、cloudflare へ入れる

## 止まる条件
- 同じ論点の open issue がある
- 追記先の今の内容が上の決定と矛盾し、どちらが正か判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい）
- chat-side-operations.md が警告域（26KB）を超える
- 未マージの work/ ブランチが同じ節を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く。両立する衝突〈追記どうし〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-SKR-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-SKR-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- `git log --all --grep=CHAT-1017-SKR-16` は0件。作業ブランチはローカル・リモートとも無し → `git checkout -b work/1017-skr-rec origin/cloudflare`（f81b95961）
- このセッションは SKR-05〜15 と同じセッション

### 手順1 確かめ

- 同じ論点の issue: GitHub MCP の `search_issues`（「スクリーンショット 画像の置き場所 比較ページ」、クローズ済みを含む）で #165（open、新サイトで OGP 画像をページ別に出し分けるか）だけ。別の論点なので止まらない
- 未マージの work/ ブランチ: `docs/notes/chat-side-operations.md`・`docs/notes/design.md`・`docs/decisions/saikyo.md` を変えているものは無い
- 追記先の今の内容:
  - `docs/decisions/saikyo.md`: 「(5) 平野さんが X に投稿する」の行は **CHAT-1015-SKR-02 の節**にあった（指示の前提・決定1 は「CHAT-1015-SKR-01 の節」と書いているが、SKR-01 の節には無い。SKR-02 の節の「#549 の公開の順番」の行で、行は1つだけなので取り違えは無い）。その行の末尾に「→ 置き換え: 2026-10-17（CHAT-1017-SKR-16）」を付け、新しい節の決定1 には「2026-10-15〈CHAT-1015-SKR-02〉の…を置き換える」と実物の節を書いた
  - `docs/notes/design.md`「7.」: 「見比べたい分かれ目は図か比較ページで並べてから聞く」の行は無かった（同じ趣旨は1行目の「見た目は文章で往復せず実物で見比べる」）。新しい行として足した
  - `docs/notes/chat-side-operations.md`「確認用 URL」: プレビューの URL の扱いと見た目の確かめ方の一文。画像の置き場所の記述は無い。矛盾は無い
- 画像の置き場所:
  - mj-logs の写し（`sync-from-mj.yml` → mj の `scripts/sync_all_logs.py`）が写すのは `docs/logs/CHAT-*.md`（`LOG_RE`）とガイド文書だけで、**画像は写らない**
  - **issue のコメントへの画像の添付は、GitHub の REST API・MCP に手段が無く、Claude Code からはできない**（画像の添付はブラウザの画面からだけ）
  - リポジトリには前例がある: 作業ブランチの `docs/logs/<Chat-Ref>/` に PNG を置いたログが既にある（`docs/logs/` の .md 以外のファイルが 310 件、例 `docs/logs/CHAT-0916-TT-11/index-390.png`）。`docs/` は `.assetsignore` で配信の対象外
  - → **主はリポジトリの作業ブランチの `docs/logs/<Chat-Ref>/`**（指示の案の `docs/logs/img/<Chat-Ref>/` ではなく、前例と同じ形にした）。チャット側は mj の REST・clone で読む（mj-logs には写らない）。issue は Code から画像を貼れないので主にしない。chat-side-operations.md にはこの方だけを書いた
  - 気づいたこと（直していない）: `cleanup-logs.yml`（`scripts/cleanup_logs.py` の `LOG_GLOB = "CHAT-*.md"`）はログの .md だけを消し、`docs/logs/<Chat-Ref>/` の画像は残る（今 39MB のうち大半が画像）

### 手順2 書いたもの（8ce82e0f6）

- `docs/decisions/saikyo.md`: 2026-10-17（CHAT-1017-SKR-16）の節（決定1・2）。SKR-02 の節の (5) の行に「→ 置き換え」
- `docs/decisions/operations.md`: 2026-10-17（CHAT-1017-SKR-16）の節（決定3・4）
- `docs/notes/design.md`「7.」: 「見た目の分かれ目は Claude Code の『自然な方』に委ねず、分かれ目ごとに案を並べる。所属が問われる印は上下・左右との空き（px）を測らせて表に書かせる」（理由に SKR-10・SKR-13 の一句）を足した。あわせて chat-side-operations.md から移した「見た目を変える指示では、マージの可否を聞く前に 375px 相当の明・暗で見て気になる所を先に挙げる」の行を足した
- `docs/notes/chat-side-operations.md`「確認用 URL」: 「**Code が撮った画像は URL と別で、作業ブランチの `docs/logs/<Chat-Ref>/` に置いてよい**（mj-logs には写らないので REST で読む）」を足し、見た目の確かめ方の一文を design.md「7.」へ移した（「場面ごとに読む文書」の表に design.md「7.」の行があるので参照は足していない）。サイズ: **21,973 → 21,932 バイト**（41 バイト減。上限 28,672 まで 6,740、警告域 26,624 まで 4,692）。純増は無い
- テスト 全件 OK
- issue（各コメントの末尾に `Chat-Ref: CHAT-1017-SKR-16`）:
  - #549（閉じたまま）: 決定1・2 のコメント。本文のチェックリストの「X と LINE の投稿画面でカード表示を確かめる」と、2026-10-16 の残りのチェックリスト（CHAT-1016-SKR-08 のコメント）の「X と LINE のカード」に、取り消し線と「→ #581 へ移した（OGP の確認、2026-10-17）」を付けた（書き換える直前に `updated_at` を取り直した）。「X に投稿する」の項目は本文・チェックリストには無かった（公開の順番は docs/decisions にだけある）
  - #581: 着手中のコメント（未解除）は無い。本文の論点 (e) の下に「最強戦の卓・対局のページを X・LINE に貼ったときのカードの見え方の確認は、#549 からこちらへ移した」の1行を足し、同じ内容をコメントした

## 報告

- 状態: 完了
- ブランチ: work/1017-skr-rec
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1017-SKR-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-skr-rec
- 確認用URL: なし
- マージ: 済（ドキュメントのみ）
- issue: #549（コメント・本文とチェックリストの直し）、#581（本文に1行・コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c51d8345）: https://github.com/retroeater/mj-logs/tree/main/guide/c51d8345

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c51d8345/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c51d8345.md
