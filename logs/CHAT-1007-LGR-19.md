# CHAT-1007-LGR-19

- 着手日時: 2026-10-07
- 対象issue: なし（同じ論点の issue を検索してから決める）
- ブランチ: work/1007-lgr
- 着手時HEAD: 187e5246

## 指示

【Claude作成】Claude Code 向け指示：申送り。チャット側が本番のページを読むときはクエリを付ける、という規則を docs/notes/chat-side-operations.md に書き残す（先に配信側の問題でないことを切り分ける。変更は docs だけ） Chat-Ref: CHAT-1007-LGR-19 マージ: ドキュメントのみ（docs/ 配下）なので、止まる条件に当たらなければ完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1007-lgr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-lgr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-lgr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 変更の範囲: docs/（docs/notes・docs/decisions・docs/logs を含む）。ページ・スクリプト・ワークフロー・Cloudflare の設定は変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
チャット側（claude.ai）が本番のページを読んだとき、クエリを付けない URL で古い版が返ることがあった。平野さんが「申送り」としたので、機械的に守れる規則として文書に書き残す。あわせて、告知動画の文言についての平野さんの判断を記録する。
決定（2026-10-07、平野さん）

* チャット側の振り返りの「私が本番ページを読むとき、古い版が返ることがある」は、申送りにする
* 告知動画のテロップと投稿文の案の「全リーグ」は、（完了済みの）全リーグに対応しているので問題なし。修正は不要

前提（チャット側。平野さんの決定ではない）

* チャット側が見たこと（2026-10-07。ページを読む道具ごしで、HTTP の応答ヘッダは見ていない）:
   * 13:25 ごろ（JST）: クエリ無しの https://ryoei.pro/houou_race.html が、12:05 ごろの改名（CHAT-1007-LGR-12）より前の内容（title「リーグ別成績推移 | 鳳凰戦 | ryoei.pro」）で返った。`?v=2`・`?v=3` を付けると新しい内容だった
   * 同じ時に、クエリ無しの https://ryoei.pro/jpml_links.html が、title が「リンク」だけで meta description も og のタグも無い古い内容で返った。`?v=2` では今の内容だった
   * ほかの25ページは、クエリの有無で同じ内容だった
   * 17:23 ごろには、2ページともクエリ無しで新しい内容が返った
* 原因は未確認。チャット側の読む道具が古い版を持っていたのか、配信の側（Cloudflare）がクエリ無しの URL に古い版を返していたのかは、チャット側からは分けられなかった。docs/notes/cloudflare.md には、HTML は `public, max-age=0, must-revalidate` で毎回再検証、とある
* 先に切り分ける（規則を書く前に行う）: このセッションから、今日変わったページ（houou_leagues.html・ouka_leagues.html〈CHAT-1007-LGR-15〉、houou_race.html、`llms.txt`）と jpml_links.html を、クエリ無しとクエリ付きで取り、本文（title・説明文）が同じかと、応答ヘッダ（`cache-control`・`cf-cache-status`・`age`）を比べてログに書く。同じなら「配信の側では再現しなかった。チャット側の読む道具の側の見込み」と書いて進める。クエリ無しのほうが古ければ、配信の問題なので、規則を書かずに止まって報告する
* 書く規則の案（文面は writing-for-agents で整える。規則と理由の一句だけ）: チャット側が本番のページ・ファイル（ryoei.pro）を読むときは、URL に `?v=<未使用の値>` を付ける（クエリ無しの URL は、読む道具が古い版を返すことがある）。変更の直後の確かめでは必ず付ける
* 書く場所の案: docs/notes/chat-side-operations.md「読み方」。表の「本番の見え方」の行か、「作業ログの読み方」の「一度読んだ URL は古い版が返ることがある」の項目に統合する（同じ趣旨の記述は足さずに置き換え・拡張する）。この文書は容量の上限がある（警告26KB／失敗28KB）ので、先に今の大きさと残りを測り、規則だけを書く。上の事例（日時とページ名）は docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」に書く
* 受け手側（Claude Code）の本番の確かめは、すでにクエリを付けて行っている（CHAT-1007-LGR-12・CHAT-1007-LGR-15 のログ）。受け手側の規則がどこかに書いてあるか（CLAUDE.md・docs/notes/cloudflare.md）を確かめ、書いてあればその場所を報告に書く（書き足さない）
* 決定は docs/decisions/ の合う分野のファイルに書く（動画の文言は houou.md、申送りは運用の分野）
* 使う skill: 文書の文面は writing-for-agents を使う

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。論点は「本番のページを読むと古い版が返る（キャッシュ）」と「チャット側の読み方の規則」
2. 切り分け。前提のページをクエリ無しとクエリ付きで取り、本文と応答ヘッダを比べてログに書く
3. 文書に書く。docs/notes/chat-side-operations.md の今の内容と大きさを確かめてから規則を入れ、事例を archive に書き、決定を記録して、cloudflare へマージする

止まる条件

* 同じ論点の open issue がある
* 切り分けで、クエリ無しの URL のほうが古い内容を返した（どのページか、ヘッダの値を書いて止まる。規則は書かない）
* docs/notes/chat-side-operations.md が容量の警告の線を超える（超える前の案と大きさを書いて止まる）
* 追記先の今の記述が規則と矛盾していて、どちらが正か判断が要る（同じ趣旨なら止めず、置き換え・拡張して、どう処理したかを書く）
* 変更が「変更の範囲」の外に及ぶ
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。ほかのセッションの push と重なって拒否されたときは、取り込み直して差分を確かめ、もう一度だけ行ってよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。`## 経過` に、切り分けの結果（ページごとの本文の一致とヘッダの値）、書いた規則の文面と場所、文書の大きさ（前と後）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-LGR-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-LGR-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1007-LGR-19"` は0件（同じチャットの LGR の続き）
- ブランチ: origin/work/1007-lgr はマージ済み。ローカルの work/1007-lgr は origin/cloudflare の祖先なので `git checkout work/1007-lgr` のうえ `git merge --ff-only origin/cloudflare`（317c70a0..187e5246）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている

### 手順1（同じ論点の issue）

- 全 issue（516件、open・closed）の題を「キャッシュ・cache・古い版・古い内容・クエリ・チャット側・読み方・max-age・再検証・stale」で検索。open の #156（エッジキャッシュの ETag の置き換わりの確認、データ更新後）と #460（検索のクエリの集計）は別の論点。チャット側が本番を読むと古い版が返る、チャット側の読み方の規則の issue は無い（closed の #360 はトークンの削減、#454 は mj-logs の古い版、#258・#123 は Cache-Control の設定）

### 手順2（切り分け、2026-10-07 17:37 JST ごろ、Claude Code から curl）

| ページ | クエリ無し | `?v=<乱数>` | 本文 | リポジトリとの一致 |
|---|---|---|---|---|
| houou_race.html | title「順位変動 \| 鳳凰戦 \| ryoei.pro」 | 同じ | 一致（md5 36d3d9be） | 一致 |
| houou_leagues.html | title「リーグ推移 \| 鳳凰戦 \| ryoei.pro」 | 同じ | 一致（8f38fd01） | 一致 |
| ouka_leagues.html | title「リーグ推移 \| 女流桜花 \| ryoei.pro」 | 同じ | 一致（8f176fa4） | 一致 |
| jpml_links.html | title「リンク \| 日本プロ麻雀連盟 \| ryoei.pro」、meta description あり | 同じ | 一致（b0d6f6fb） | 一致 |
| llms.txt | 今の内容 | 同じ | 一致（fd6b83cd） | 一致 |

- 応答ヘッダは5件ともクエリの有無で同じ: `cache-control: public, max-age=0, must-revalidate`、`cf-cache-status: HIT`、`age` は無し（`llms.txt` だけ `etag` あり）
- 配信の側では再現しなかった。チャット側の読む道具の側の見込み。裏付け: チャット側が見た jpml_links.html の「title が『リンク』だけで meta description も og も無い」版は、2026-09-09 の acb1621c（#5）より前のもので、4週間前の版。デプロイの切り替わりの間に配信の側が返す版ではない
- 止まる条件（クエリ無しのほうが古い）には当たらないので、規則を書いた

### 手順3（文書）

- docs/notes/chat-side-operations.md「読み方」の表の「本番の見え方」の行に、規則を足した（新しい項目でなく、既存の行を広げた。「作業ログの読み方」の「一度読んだ URL は古い版が返ることがある」は mj-logs のログの URL の話なので、そちらは変えていない）。足した文:
  「**チャット側が本番（ryoei.pro）のページ・ファイルを読むときは、URL に `?v=<未使用の値>` を付ける。変更の直後の確かめでは必ず付ける**（クエリ無しの URL は、読む道具が古い版を返すことがある）」
- 大きさ: 24,035 → 24,306 バイト（+271。警告 26,624 まで 2,318 残る）。CLAUDE.md 25,941・handover.md 23,564 は変えていない。check_asset_limits・unittest OK
- 事例（日時・ページ名・切り分けの結果）は docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」に小見出し「「読み方」の表（本番の見え方）」で足した
- 受け手側（Claude Code）が本番を確かめるときにクエリを付ける規則は、CLAUDE.md・docs/notes/cloudflare.md に書いていない（CLAUDE.md「作業ログ」節は「ログ（公開）」の URL の `?v=<SHA>`、docs/notes/ogp.md と docs/new-page-checklist.md は X のカードの `?x=<未使用の数字>` だけ）。指示のとおり書き足していない
- 決定: docs/decisions/operations.md に申送りの1行、docs/decisions/houou.md に「全リーグ」の文言は修正不要の1行（どちらも新しい節「2026-10-07（CHAT-1007-LGR-19）」）

### マージの前の取り込み

- origin/cloudflare の取り込みで docs/decisions/operations.md が衝突した（ほかのセッションの CHAT-1005-RVW-21 の節と、この指示の節が、どちらもファイルの末尾に同じ日付で足されていた）。両方の追記が両立するので、cloudflare にある RVW-21 の節を先に、この指示の節を後に置いて解いた（d367823a）。解いた後の該当箇所:

```
## 2026-10-07（CHAT-1005-RVW-21）

- 起動の間隔は W1': Worker `mj-scheduler` の cron を毎分にし、…（実装3はマージまで進めてよい）
- `actions/status.md` は「書き出した時刻」の行のほかに変わりが無ければ書き出さない（…）

## 2026-10-07（CHAT-1007-LGR-19）

- チャット側の振り返りの「本番のページを読むとき、古い版が返ることがある」は、申送りにする（…）
```

- 取り込み後の cloudflare との差分は docs/ の5ファイル（decisions/houou.md・decisions/operations.md・logs/CHAT-1007-LGR-19.md・notes/chat-side-operations.md・notes/handover-archive-2026.md）だけ

## 報告

- 状態: 完了
- ブランチ: work/1007-lgr（cloudflare へマージ済み、削除していない）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1007-LGR-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-lgr
- 確認用URL: なし（docs のみ）
- マージ: 済（このログを入れたコミットを、そのまま cloudflare へ push した。`git log -1 origin/cloudflare -- docs/logs/CHAT-1007-LGR-19.md`）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 69ea17e3）: https://github.com/retroeater/mj-logs/tree/main/guide/69ea17e3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/69ea17e3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/69ea17e3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/69ea17e3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/69ea17e3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/69ea17e3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/69ea17e3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
