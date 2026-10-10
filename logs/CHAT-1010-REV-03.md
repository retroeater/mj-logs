# CHAT-1010-REV-03

- 着手日時: 2026-10-10
- 対象issue: #492
- ブランチ: work/1010-rev
- 着手時HEAD: db5444b2

## 指示

【Claude作成】Claude Code 向け指示：CLAUDE.md を圧縮する（場面限定の手順を docs/notes/ へ移し、入口の規則と参照だけを残す）。判断待ちで止まる Chat-Ref: CHAT-1010-REV-03 マージ: 判断待ちで止まる（整理後の全文をチャット側が読み比べてから、マージの指示を出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rev の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rev を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rev origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CLAUDE.md は CHAT-1009-REV-01・REV-02 の整理でほとんど減らなかった（25,941 → 26,084 バイト）。最終目標の 20KB 前後（CLAUDE.md「更新ルール」、#492）に向けて、CLAUDE.md だけを圧縮する。
決定（2026-09-29、平野さん）

* 「レビュー」は、容量制限のある文書を包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。文書の変更は判断待ちで止め、整理後の全文をログに貼らせて読み比べてからマージする

決定（2026-10-09、平野さん）

* REV-01・REV-02 のマージの後に、CLAUDE.md だけを圧縮する回を別の指示で出す（この指示）

前提（チャット側。平野さんの決定ではない）

* 目安は 20KB 前後（−6KB 程度）。届かなくてよいが、届かない理由（残した規則の種類）を報告に書く
* 方針: CLAUDE.md は毎セッションの起動時に読まれる。毎回の作業で守る規則と「いつ・どこを読むか」の入口は CLAUDE.md に残し、特定の場面でだけ使う手順・コマンド・理由の説明・事例は移す。移し先は既存の `docs/notes/`（branch-operations.md・cloud-sessions.md・static-generation.md・cloudflare.md 等）と `docs/logs/_template.md`。退避先には上限を置かない（CLAUDE.md「更新ルール」）
* 移す・縮める候補（チャット側が guide/77f81735 の CLAUDE.md で数えた節の大きさの順。実物で確かめる）:
   * 「作業ログ」（約4.7KB）: 最終報告の URL の `?v=` と raw の URL での確かめ・15分の写し待ち、節の書き換えの探し方、ログの寿命、`[sync-logs]` の目印の説明 → `docs/logs/_template.md`・branch-operations.md「作業ログの寿命」へ寄せ、CLAUDE.md は「1指示1ファイル・ログ先行 push・`## 報告` の10項目を省かない・決定を docs/decisions へ・詳細は _template.md」程度に
   * 「ブランチ運用」（約4.3KB）: worktree の作り方、マージの手順のコマンド、生成物の衝突の解き方、ワークフロー変更時、ブランチ削除 → branch-operations.md の該当節へ。CLAUDE.md には「cloudflare へ直接 push しない・承認が無ければマージしない・マージ直前の祖先確認・未マージのブランチを消さない・指示文がこの節と食い違えばこの節に従う」と各節への参照を残す
   * 「Chat-Ref」（約3.8KB）: 受け手の確認の項目の重複（0章ゲート・識別子の重複確認・同じ Chat-Ref の確認）を1つにまとめ、コマンドは branch-operations.md「Chat-Ref の着手前の確認」へ
   * 「方針」（約3.7KB）: 「判断・作業の原則」の理由の説明・issue 番号の経緯、「禁止事項」の理由（参照先にある）
   * 「概要」（約3.1KB）: 構成とデータの流れの詳細は static-generation.md・cloudflare.md の参照に
   * 「更新ルール」（約2.5KB）: 「規約が守られないときは書き方を疑う」は書き手（チャット側）向けなので chat-side-operations.md か archive へ。上限の数値と「上げるのは平野さんの判断」は残す
* 消してはならないもの: 規則そのもの（移すのはよい）、禁止事項の項目、止まる条件、issue 番号。移した規則は、移し先で文言を変えずに残す。迷ったら CLAUDE.md に残して報告に書く
* 移し先の文書が他の未マージのブランチで変わっていることがある（手順1で確かめる）

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492・#292・#301 など）、着手中のコメントがあれば止まって報告する。`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、CLAUDE.md と移し先の文書を変えているものを一覧にしてログに書く（行が重なるものは移し先を変えるか、その部分を移さずに報告に書く）
2. 前提の方針で CLAUDE.md を圧縮し、移した記述を移し先に入れる。対応表（CLAUDE.md の項目 → 処理〈残す／縮める／移す: 移し先の節〉→ 移し先での位置）を経過に書く。CLAUDE.md の各節を参照している他の文書（`grep -rn 'CLAUDE.md「' docs .github scripts .claude`）の節名が切れないことを確かめ、切れるものは直す
3. 前後のサイズ（CLAUDE.md と移し先の文書、`wc -c`）を報告に書き、整理後の CLAUDE.md の全文と、移し先の文書の差分を経過に貼る。`python3 scripts/check_asset_limits.py` と `python3 -m unittest discover -s scripts/tests` を通す

止まる条件

* 手順1で同じ論点の進行中の作業（着手中のコメントがある等）が見つかった
* 規則を消さないと減らせない箇所がある（消さずに報告に書く。止まるのはその箇所だけで、ほかは進める）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしないので通常は起きない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は「判断待ち」
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-REV-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-REV-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1010-REV-03` のコミットなし。`REV` は同じセッションの REV-01・REV-02 だけ
- 作業ブランチ: リモート・ローカルとも `work/1010-rev` が無いため `git checkout -b work/1010-rev origin/cloudflare`
- 0章: 「指示」欄の最後の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている（貼られた文面では冒頭の行の改行が失われていた。内容は欠けていない）

### 手順1

- issue: #492（open。コメント2件はどちらもこのセッションの REV-01・REV-02 のもの）・#292（open、0件）・#301（open、0件）。他セッションの着手中のコメントは無い
- `git branch -r --no-merged origin/cloudflare` のうち CLAUDE.md・移し先（docs/notes/・docs/logs/_template.md・docs/instruction-template.md）を変えているもの:

| ブランチ | 変えているファイル |
|---|---|
| origin/work/1008-hou | `docs/notes/houou-top.md`・`docs/notes/static-generation.md`（3行） |
| origin/work/1009-swp-526・origin/work/1010-xap | なし |

  static-generation.md は移し先にしなかった（重なりを避けるため）。CLAUDE.md・branch-operations.md・_template.md・chat-side-operations.md・cloud-sessions.md・handover-archive-2026.md と重なるブランチは無い

### 手順2: 参照されている CLAUDE.md の節名

`CLAUDE.md` を含む行の「…」を集めると（docs/logs を除く）、参照されている見出しは「作業ログ」18・「ブランチ運用」16・「CLAUDE.md / handover.md の更新ルール」8・「Chat-Ref」7・「判断・作業の原則」7・「構成」4・「データの流れ」3・「issueの着手ルール」3・「方針」2（ほかに「禁止事項」「メンテナンス用スクリプト」）。
**見出しは1つも変えていない**（前後の `grep '^#' CLAUDE.md` が一致）。句の参照は chat-side の「未マージのブランチは削除しない」（CLAUDE.md に残る）と、cloud-sessions.md の「CLAUDE.md「ブランチ運用」の「作業ブランチも削除する」」（元から CLAUDE.md に無い句で、branch-operations.md にある）。後者を branch-operations.md「作業ディレクトリの分離（Codespace）」への参照に直した

### 手順2: 対応表

| CLAUDE.md の項目 | 処理 | 移し先での位置 |
|---|---|---|
| 概要「引き継ぎ」 | 縮める | — |
| 「構成」本番反映 | 縮める（「コードから追えない」を外した） | archive「2026-10-10 の圧縮で CLAUDE.md から外した記述」 |
| 「データの流れ」 | 縮める（1・2項目を統合） | — |
| 「メンテナンス用スクリプト」 | 縮める | — |
| 「判断・作業の原則」本番の HTML とブラウザ | 縮める（規則を先、理由を括弧に） | — |
| 「判断・作業の原則」タスクは Issues | 移す（同じ文書内） | 「issueの着手ルール」の最後の行に統合 |
| 「禁止事項」各項目 | 残す。理由の文だけ縮める（参照先は残す） | 外した理由の文は archive |
| 「ブランチ運用」Codespace の worktree | 縮める（規則は残し、手順は参照） | branch-operations.md「作業ディレクトリの分離（Codespace）」（既存） |
| 「ブランチ運用」マージの承認と副項目 3つ | 縮める（規則の要旨は残す） | branch-operations.md「マージするとき」（新設。元の文言のまま） |
| 「ブランチ運用」マージの手順 | 縮める（コマンドと祖先確認は残す） | 同「マージするとき」（元の文言のまま、理由の括弧を含む） |
| 「ブランチ運用」ワークフロー変更 | 縮める | branch-operations.md「ワークフローを変更したとき」（既存） |
| 「ブランチ運用」生成物の衝突 | 縮める（止まる条件の要旨は残す） | branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」（元の文言のまま。「決まりは CLAUDE.md」の参照を置き換えた） |
| 「ブランチ運用」ブランチ削除・#176 | 残す（語を縮める） | — |
| 「Chat-Ref」受け手の確認3項目（同じ Chat-Ref・識別子・0章ゲート） | 1つの箇条（止まる条件3つ）にまとめた | コマンドは branch-operations.md「Chat-Ref の着手前の確認」（既存） |
| 「Chat-Ref」到達確認のコマンド | 移す | branch-operations.md「同じ Chat-Ref と雛形の行」（新設） |
| 「Chat-Ref」雛形の行の確認の理由 | 移す | 同上 |
| 「Chat-Ref」報告の最後の行 | 「作業ログ」の最終報告に統合 | 括弧（複数の指示）は _template.md「ターミナルへ返す最終報告」 |
| 「作業ログ」`[sync-logs]` | 残す | — |
| 「作業ログ」節の書き換えの探し方 | 縮める（規則は残す） | _template.md（理由と手順を元の文言のまま） |
| 「作業ログ」最終報告の `?v=`・raw の確かめ・15分・URL の直後 | 縮める（「mj-logs で今回の版を確かめてから書く」は残す） | _template.md「ターミナルへ返す最終報告」（元の文言のまま） |
| 「作業ログ」ログの寿命 | 参照にした | branch-operations.md「作業ログの寿命」「書き方」（既存。同じ規則がある） |
| 「issueの着手ルール」 | 縮める（理由の文だけ archive） | archive |
| 「コミットのルール」 | 縮める（理由の文だけ archive） | archive |
| 「更新ルール」規約が守られないとき | 移す | chat-side-operations.md「Claude Code とのやり取り」（元の文言のまま） |
| 「更新ルール」上限・最終目標・Chat-Ref を書かない | 残す | — |

- 消さないと減らせない規則は無かった（消した規則は無い）
- 20KB に届かなかった理由（22,098 バイト）: 残したのは、毎回の作業で守る規則（ログ先行 push・`## 報告`・最終報告の形・トレーラ）、止まる条件（Chat-Ref の3つの確認・マージの条件・衝突・ブランチの削除）、禁止事項の全項目、上限の数値。これ以上は規則の文言そのものを削ることになるため止めた

### 手順3: サイズ（バイト、`wc -c`）

| 文書 | 前（db5444b2） | 後（3e7b4cb0） | 差 |
|---|---:|---:|---:|
| CLAUDE.md | 26,084 | 22,098 | −3,986 |
| docs/notes/branch-operations.md | 20,552 | 22,635 | +2,083 |
| docs/logs/_template.md | 5,470 | 6,866 | +1,396 |
| docs/notes/chat-side-operations.md | 24,828 | 25,184 | +356（警告域 26,624 の外） |
| docs/notes/cloud-sessions.md | 14,648 | 14,703 | +55 |
| docs/notes/handover-archive-2026.md | 84,239 | 86,261 | +2,022（節1つ） |

- `python3 scripts/check_asset_limits.py` OK、`python3 -m unittest discover -s scripts/tests` OK
- 決定の記録: この指示の「決定」2件は `docs/decisions/operations.md` に既にある（2026-09-29 と 2026-10-09〈CHAT-1009-REV-02〉）。足していない


### 整理後の CLAUDE.md の全文（3e7b4cb0）

````markdown
# CLAUDE.md

# ryoei.pro

## 概要

日本プロ麻雀連盟の選手データベースを含む個人サイト。

### 引き継ぎ

経緯・現状・次にやることは docs/handover.md（文脈が要るときはまず読む）。
**起動時に読み込んだこのファイルは古いことがある**（確かめ方: docs/notes/branch-operations.md「起動時に読み込んだ CLAUDE.md が古くないか確かめる（Codespace）」）。

### 構成
- 静的HTML。ビルド工程なし。ページの一覧はdocs/notes/static-generation.md「ページの一覧」（件数の正は`python3 scripts/regenerate.py --list`）
- `llms.txt`（AIクローラー向けのページ索引）は手書きの静的ファイル1枚で、生成スクリプトは持たない（#161）
- Cloudflare Workersの静的アセットとして配信（`wrangler.jsonc`、assets.directory は `./`）
- **本番反映は Cloudflare Workers Builds（ダッシュボードのGit連携）が `cloudflare` への push を検知して行う**（`chore: regenerate ...` も含む。
  Actionsにデプロイのジョブは無い、#169。設定はダッシュボード側。docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」）
- Bootstrap 5.3.8 をローカル配信（assets/vendor）。CDNは使わない
- ページ本体（例: `jpml_pros.html`）とロジック（同名の `.js`）は分ける。ページ末尾で navbar.js を読み込んで共通ナビを描画する
- **手書きHTMLを新規に追加する前に**docs/notes/static-generation.md「navbar.js と検索欄」を読む（hrefはルート相対〈#162〉、`data-search="off"`〈#163〉）
- skill（`.claude/skills/`、plugin は使わない）と git の hook（`.claude/hooks/`）の導入・入れ直しはdocs/notes/skills.md

### データの流れ
- 選手データ・成績データはすべてGoogleスプレッドシートが正本。ビルド時生成のページは`scripts/generate_<ページ名>.py`が読んで焼き込む
  （型ごとのページと仕組みはdocs/notes/static-generation.md「ページの一覧」「現行の仕組み」）
- **型Cの選手選択リストに退会済みの選手が出ないのは正しい挙動**（同「ページ側のJS」、#168）。Google Charts依存の6ページは旧方式（#7）
- 選手のプロフィール画像はX(pbs.twimg.com)など外部ドメインに依存し、リンク切れしやすい

### メンテナンス用スクリプト（scripts/）
- `scripts/`は`.assetsignore`で公開対象外。説明・使い方はdocs/notes/static-generation.md「メンテナンス用スクリプトの詳細」
- ページの再生成は`python3 scripts/regenerate.py <ページ名>`（`all`で全ページ、`--list`で対象一覧、`--changed`で変更ファイルから判定）

## 方針

### 応答について
- 日本語で応答すること

### 判断・作業の原則
- **セッションから到達できない領域（Cloudflareダッシュボード、ブラウザでの本番の見え方など）の状態を、到達できないことを根拠に「無い」と結論づけない。**
  確かめられる手段（check-runs、issueの検索など）を試し、残りは平野さんに確認する（#169）。平野さんの目視申告値は
  「申告値ではこうなっている。ここからは検証できない」と書く（#312）。文書の「できない」を実測の代わりにしない（環境は変わる）
- **check-runsの成功だけで「本番反映を確認した」と報告しない**（「本番のHTML」と「ブラウザでの見え方」は確認できる範囲が違う。docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」）
- **修正の検証は、先に「修正前のコードでも通らないか」を確かめる**（#310）。実データに依存する検証は、実施時点で前提を確かめる
- 外部ドメインへの依存を増やさない（CSP導入を予定しているため）
- `.assetsignore` に開発用ファイルを列挙。公開対象を増やさない。新しいディレクトリ・ファイルは公開してよいか確認し、公開しないものは追加する（#133）
- ビルド・lint の自動化コマンドはなし。HTML/JSの変更はブラウザで直接確認する
- 外部の状態を待つ待機は上限15分（超えたら状態を書き「未確認の項目」に回して進む。自分のコマンドの実行は対象外）。共有の定数・関数を変えるときは参照を洗い出し、
  マージ前に全ページを再生成して差分を確かめる（docs/notes/static-generation.md「ワークフローを手動実行するとき」「生成スクリプトの構成」）
- テストは`scripts/tests/`（unittest、標準ライブラリのみ）。実行は`python3 -m unittest discover -s scripts/tests`。週次の`check-meibo.yml`も実行する

### 禁止事項（理由は参照先）
- `gh-pages` ブランチに触らない（docs/handover.md「gh-pages ブランチは触らない」）
- `CLOUDFLARE_API_TOKEN` をGitHub Secretに登録しない。到達できても `wrangler deploy` しない（二重デプロイ。docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」）
- `_redirects` 先頭の `/  /index.html  200` を消さない（トップページが404になる。docs/notes/cloudflare.md「配信設定: html_handling・_redirects・canonical・_headers」）
- `wrangler dev` は必ず `npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state` で起動する（素だと無限リロード。docs/notes/cloudflare.md「ローカル確認（wrangler dev）」）
- assets/vendor 配下に `sourceMappingURL` コメントを残さない（#99）。更新時はファイルのパスを変える（30日キャッシュ、#92）
- sitemap の lastmod を手で書き換えない（`scripts/update_sitemap_lastmod.py --from-git`が導出する。docs/notes/sitemap-lastmod.md、#265）
- `git stash` を使わない
- 未コミットの変更がある作業ツリーで、`git checkout HEAD -- .`・`git reset --hard`・`git clean` などまとめて消すコマンドを使わない
  （docs/notes/branch-operations.md「未コミットの変更を戻すとき」）

## ブランチ運用

複数セッションが並行編集するため、セッションごとに作業ブランチと作業ディレクトリを分ける（#198）。場面ごとの手順はdocs/notes/branch-operations.md。
**チャット側の指示文がこの節と食い違う（`cloudflare`上での直接作業など古い前提を含む）ときは、指示文には従わずこの節に従うこと**（#205）。

- **`cloudflare`: 統合・デプロイ専用。セッションはここへ直接pushしない**（マージ＝本番反映）
- **`work/<識別子>`: セッションの作業ブランチ。** 識別子は Chat-Ref から取る（`CHAT-0913-QMX-02`なら`work/0913-qmx`）。指示文に指定が無くても切る。
  複数issueを1ブランチで扱ってよいが、作業に関係しない独立した変更（ルール追記・ドキュメントのみの修正等）は別ブランチに分ける
- **Codespace: `/workspaces/mj`は全セッションが共有する。`origin/cloudflare`を明示して作った worktree の中で作業し、`/workspaces/mj`自身では`git checkout`/`git switch`を行わない**
  （手順・片付けは同「作業ディレクトリの分離（Codespace）」）。**クラウドセッション（`/workspaces/mj`・worktree・`gh`が無い）の読み替えはdocs/notes/cloud-sessions.md**
- **成果物の`cloudflare`へのマージは、指示文に「マージ: 承認済み（チャットで）」があるときだけ行う。** 無ければ完了を報告し、判断待ちで止まる。
  承認は処理中に求めない。ドキュメントのみの変更は完了報告のうえマージしてよい。承認済みでも、確認が1つでも通らない・止まる条件に当たった・
  前提が崩れたときはマージせずに報告する（同「マージするとき」）
- **マージは`git push origin <作業ブランチ>:cloudflare`で行い、push直前に再fetchして`git merge-base --is-ancestor origin/cloudflare HEAD`を確かめる**（同「マージするとき」）
- **`.github/workflows/`を追加・変更する作業では、着手時に同「ワークフローを変更したとき」を読み、マージ前に作業ブランチで手動実行して確かめる。実行できなければ報告して判断を仰ぐ**
- 長期間マージされないブランチは、定期的に`cloudflare`を取り込んで乖離を小さく保つ
- **`origin/cloudflare`の取り込みで衝突したら、同「生成物を含む作業ブランチを取り込む・マージするとき」に従う**（生成されたページだけなら取り込んだ後のスクリプトで生成し直して解く。
  それ以外が衝突したら止まる。指示文が解き方を書いている衝突はそのとおりに解き、該当箇所をログに引用する）
- **ブランチを削除する前に同「ブランチを削除するとき」を読む。読むまで削除しない。** 未マージのブランチは削除しない。削除直前の先頭SHAを記録に残す（#207）
- **このブランチ運用ルールに反した作業（自他を問わない）は#176にコメントで記録する**（記録対象と書式は#176の「スコープ変更」節）

## Chat-Ref

チャット（claude.ai）で作った指示文に付く識別子（`CHAT-MMDD-XXX-nn`: 発行日・チャットセッション識別子・連番）。新しい識別子は英大文字3文字。
既存の識別子（2文字、`A`・`DOC`・`K7`・`W2` など）は有効なままで、重ねない（#474）。チャット側の規則はdocs/notes/chat-side-operations.md。

受け取る側（Claude Code）:

- **着手前の確認**（コマンドと項目はdocs/notes/branch-operations.md「Chat-Ref の着手前の確認」）。当たれば着手せず報告して止まる:
  - 同じChat-Refのコミットがある（`git log --all --grep="<Chat-Ref>"`。同じ指示文が再度貼られることがあり、追記は冪等でないため）。issue操作のみの作業は対象issueの既存コメントも見る。再開も同じ確認による
  - そのセッションの最初の指示（撤回の欠番があれば`02`以降）で、`XXX`が他のセッションで使われている（全ブランチのコミットと`docs/logs/`の履歴。`MMDD`が違っても重複させない）
  - 指示文に実装が含まれ、0章ゲート（対象issue・文書サイズ・他セッションの作業・未マージブランチとの重なり、#265・#336）に食い違いや重なりがある
- **指示文に書かれた事実はチャット側が会話の記憶から書いたもので、誤っていることがある**（#319）。前提と実物が食い違ったら、指示に合わせて手を入れず、中断して報告する
- **指示文の記述同士が食い違ったときは、個別の指定より前提・ルールの側を優先し、その旨を報告する**
- **着手時に、指示文の冒頭に docs/instruction-template.md の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）が揃っているかを確かめ、
  無い行があれば止まらずに、その行の名前をログの経過と `## 報告` の「判断が必要なこと」に書く**
- **実行しない判断をした指示も、その旨を平野さんに伝える**（黙って落とすと「貼り忘れ」と区別がつかない）
- **コミットメッセージの末尾にはトレーラをこの並びで入れる**（`Chat-Ref`はChat-Refを含む指示のときだけ。`Claude-Session`は常に付ける。
  **モデル名は例を写さず、そのセッションで実際に動作しているもの**）:

  ```
  Chat-Ref: CHAT-MMDD-XXX-nn
  Co-Authored-By: <モデル名> <noreply@anthropic.com>
  Claude-Session: https://claude.ai/code/session_<ID>
  ```
- **コミットを伴わない指示（issueの起票・編集のみ、確認のみ）では、関係するissueのコメント末尾に`Chat-Ref:`行を書く**
- 撤回されたChat-Refの番号は欠番とし、再利用しない

## 作業ログ

指示1件ごとの経過と報告を残す（全セッション共通）。書き方・最終報告の形は`docs/logs/_template.md`、寿命はdocs/notes/branch-operations.md「作業ログの寿命」。
`docs/logs/`は公開されないが、push したログとガイド文書は public の`retroeater/mj-logs`に写る（mj-logs の`sync-from-mj.yml`、#440・#298）。
**ログに人の個人情報（氏名と結びついた属性など）・鍵やトークンの値・非公開の URL（Claude Code のセッション URL は可）を書かない。**
プレビューの URL（Workers Builds の別名 URL など）も非公開として扱い、ターミナルへの最終報告にだけ書く。

- **1指示につき1ファイル。パスは`docs/logs/<Chat-Ref>.md`**
- **ログのpushは、調査・計測・実装より先に行う最初の手順とする。** Chat-Refの重複確認の直後に、ヘッダと`## 指示`だけのログを
  単独でコミットしてpushし、それが済むまで他の作業（読み込み・計測・編集・ブランチ操作）を始めない（issueの着手コメントだけは同時でよい）。
  以降は節目ごとに追記してpushし、完了時に仕上げてpushする（セッションが失われても記録が残るように）
- **着手時と、作業を終える（完了・判断待ち・中断）最後のpushのコミットメッセージ本文に`[sync-logs]`を入れる。**
  途中の節目のpushには付けない（今の写しは目印を見ないが、規則の削除は #298 の実装4の後半）
- 構成は ヘッダ・`## 指示`〈貼られた指示文をそのまま〉・`## 経過`〈詳細はすべてここ〉・`## 報告`（`_template.md`をコピーして使う）。
  **`## 報告`はログの末尾に置き、作業の最後に更新してpushする。** チャット側はこの節だけを読むため、**10項目を省かず、該当が無ければ「なし」と書く**。
  状態の書き方（「完了」の条件・` / 続き: CHAT-…`）は`_template.md`と同「作業ログの寿命」
- **ログの節を書き換えるときは、`## 指示`欄より後ろの見出しを相手にし（`## 報告`はファイルの最後の一致）、push 前に`## 指示`欄が変わっていないことを確かめる**（理由は`_template.md`）
- ログは作業ブランチにだけpushし、成果物と別のコミットにする。`cloudflare`へは指示の最後のマージ1回で成果物と一緒に入れる（マージの結果を書く docs/logs のみの追いのpushは可）
- 後の指示で使うスクリプト・中間データの置き場所は docs/notes/session-network.md「作業ファイルの置き場所」（scratchpad は再起動で消える）
- **ターミナルへ返す最終報告は、状態・ログのURL・ブランチ・（あれば）確認用・ログ（公開）・Chat-Ref の行だけにする**（形と書き方は`_template.md`「ターミナルへ返す最終報告」）。
  最後の行は`Chat-Ref: CHAT-MMDD-XXX-nn`、その直前は「ログ（公開）」の行（mj-logs で今回の版が返ることを確かめてから書く）。判断が必要なこと・エラーを含め、詳細はログの`## 報告`に書く
- **例外として、次の2つはログに届かないためターミナルに内容を書く:** 作業途中で平野さんに質問して止まるとき／pushに失敗したとき
- **指示の完了時（完了・判断待ち・中断の最後の push）に、その指示の「決定」節と作業中の平野さんの回答（grill を含む）を
  `docs/decisions/<分野>.md` に足す**（ログと同じコミットでよい。書き方は`docs/decisions/README.md`）

## issueの着手ルール
- **issueに着手したら、コードを触る前にそのissueへ「着手中」のコメントを残す**（並行するセッションから着手状況を知る唯一の手段）。
  セッションのURL（`Claude-Session` と同じ）を含める。取得できない場合は Chat-Ref の `XXX` で代替してよい
- **着手する前に、そのissueに他セッションの着手中コメントが無いか確認する。** あれば着手せず、ユーザーに確認する（#157）
- **issueを新規作成する前に、同じ主題のissueをクローズ済みも含めて検索する**（`gh issue list --state all --search "<キーワード>"`）。
  **issue の起票やページ・機能を作る指示では、着手の前に同じ目的の issue と未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチの
  コミットの件名とログ）を確かめ、見つかったら作る前に止まって報告する**（0章ゲートは触るファイルの重なりを見るもので、目的の重なりは拾えない）
- **作業を中断・放棄したときも、その旨をコメントに残す**（着手中のまま放置されると、他セッションが着手を見送り続ける）
- **issueをクローズするときは「状況:」ラベル（待ち/対応中/保留）を外す**（#112）
- タスクはGitHub Issuesで管理し、Projects のボードは使わない。優先順位と状況は issue の本文・ラベル・期日で表す

## 新サイト送りの issue（親 #296）
- **issue を新サイト送りにする・取り消す前に #296 の本文「新サイト送りにするとき・やめるとき」を読む。**
  コメント「親: #296」と sub-issue の登録は片方だけにしない

## コード規約
- 着手中のissueと無関係なコードは触らない。自分が書いた・変更した箇所以外にコメントを足さない。変更行数は最小にする
- 繰り返し使う値・意味のある値（列番号、画像サイズ、URL、閾値）は定数にする。一度きりで自明な値はインラインでよい
- ネストを深くしない。早期return / continueを使う
- JSのif文は1行でも必ず`{}`を付ける
- コメントは「何を・なぜ」を短く。「どうやって」はコードに語らせる
- 人が読む文章（コメント、コミットメッセージ、応答）は最小限の語数で。賞賛・相槌は不要
- スプレッドシートのファイルは「ブック」と書く（「冊」で数えない）

## コミットのルール
- コミット前に `git status` / `git diff --stat` を確認し、**着手中のissueと無関係なファイル・ハンクを含めない。**
  複数の変更が混ざっていたらissueごとに分けてコミットする（#157）
- コミットメッセージ: 件名は50字目安（72字上限）・命令形・末尾ピリオドなし、空行を挟んで本文に「何を・なぜ」。
  既存の`chore:`等の接頭辞形式を維持し、トレーラは「Chat-Ref」節のとおり入れる
- **文章（ログ・issue本文・コメント・コミットメッセージ）をシェル経由で書かない**（引用符なしのヒアドキュメントや`"..."`ではバッククォートと`$`が展開される）。
  ファイルは Write / Edit で書く。ヒアドキュメントは必ず引用符付き（`<<'EOF'`、変数が要っても外さない）。issue の本文・コメントは `--body-file`、コミットメッセージは `git commit -F - <<'EOF'` で渡す

## CLAUDE.md / handover.md の更新ルール
- ページの移行・追加・削除を行ったときは、同じコミットで docs/notes/static-generation.md「ページの一覧」の件数・ページ列挙と`llms.txt`（手書き、#161）を更新する。
  **新しいページは未公開で入れ、`llms.txt`・navbar・サイトマップには公開の issue で載せる**（docs/new-page-checklist.md、#243）。
  CLAUDE.md にはページを列挙しない。型が増えたときだけ「データの流れ」を直す（#136）
- **docs/handover.md は「現状・ルール・次にやること」のみを書く。** 実装の詳細は issue のコメントか `docs/notes/<topic>.md` へ、handover には結論1〜2行と参照だけ
- 記述を更新するときは古い記述を消して置き換える（追記型にしない）。同じ内容を2箇所に書かず、片方は参照にする
- handover.md / CLAUDE.md をissueやコメントから参照するときは、行番号ではなく節・項目の見出しで書く
- 「最終更新」は日付＋直近の変更3行以内。外した行は archive へ移さず消してよい
- 上限はファイルごとに **CLAUDE.md 32KB（警告域30KB）・handover.md 28KB（警告域26KB）・
  docs/notes/chat-side-operations.md 28KB（警告域26KB）**（`assets-check.yml`、1KB=1024バイト。**作業する worktree の上で測る**）。
  警告域に近づいたら、足す前に `docs/notes/` へ移す。**警告が出たら、上限を上げずに3文書とも整理する**（同じ趣旨の記述をまとめ、
  事例は `handover-archive-2026.md` へ、場面限定の手順は `docs/notes/` へ。退避先には上限を置かない、#421）。
  上限は下げる方向にだけ動かし、上げるのは平野さんの判断。最終目標は CLAUDE.md 20KB・handover.md 24KB 前後（#292・#301 が進んだ時点で見直す、#492）
- **この3文書には出典としてのChat-Refを書かない**（ログは定期削除で消え、行き先の無い参照になる。issue番号は書いてよい）
````

### 移し先の文書の差分（db5444b2..3e7b4cb0）

````diff
diff --git a/docs/logs/_template.md b/docs/logs/_template.md
index d2a19069..45534fe9 100644
--- a/docs/logs/_template.md
+++ b/docs/logs/_template.md
@@ -27,11 +27,22 @@ CLAUDE.md「作業ログ」節が正。ここには**書く時点で読めば足
 
 **URL の直後には全角文字を続けない**（改行か半角空白で区切る）。続く文字まで URL とみなされ、クリックすると404になる（BD-01）。
 
+**ログの節（`## 報告`など）を書き換えるときは、`## 指示`欄より後ろの行頭の見出しを相手にする（`## 報告`はファイルの最後の一致）。**
+指示文にも同じ見出しが出てくるため、最初の一致で探すと指示欄から後ろが消える。書いた後、`## 指示`欄が変わっていないことを確かめてからpushする（CLAUDE.md「作業ログ」から移した）。
+
 **`## 報告` に `Chat-Ref:` の行は置かない。** どの指示の報告かは「ログ:」の URL に含まれる Chat-Ref で見分ける（#419 (b)）。
 `Chat-Ref:` を書くのは、コミットのトレーラ・issue のコメントの末尾・**ターミナルへの最終報告の最後の行**の3か所。
 
 ## ターミナルへ返す最終報告
 
+CLAUDE.md「作業ログ」「Chat-Ref」から移した規則:
+
+- 判断が必要なこと・エラーを含め、詳細はログの`## 報告`に書く。**URL の直後に文字を続けない**（続く文字まで URL とみなされる）
+- **「ログ（公開）」の URL の末尾には`?v=<最後に push したログを含む mj のコミットの短い SHA>`を付け**（チャット側は一度読んだ URL で古い版を受け取る）、
+  **mj-logs の raw の URL（`https://raw.githubusercontent.com/retroeater/mj-logs/main/logs/<Chat-Ref>.md`）で今回の版が返ることを確かめてから書く。**
+  15分待っても写らなければ、URL を書かずに「ログ（公開）: 写し待ち（理由）」と書く
+- Chat-Ref付きの指示への報告は、最後の行に`Chat-Ref: CHAT-MMDD-XXX-nn`を、その直前の行に公開ログの URL を書く（複数の指示に答える場合は全て書く）
+
 2行目の URL は `## 報告` の「ログ」と同じもの。未マージなら
 `https://github.com/retroeater/mj/blob/<実際に使ったブランチ>/docs/logs/<Chat-Ref>.md`、マージ後なら `blob/cloudflare/` 以下
 （平野さんがログの場所を探さずに済むように、CHAT-0918-HT-05）。
diff --git a/docs/notes/branch-operations.md b/docs/notes/branch-operations.md
index c4dfd2d1..c9927cef 100644
--- a/docs/notes/branch-operations.md
+++ b/docs/notes/branch-operations.md
@@ -40,6 +40,11 @@ CLAUDE.md「ブランチ運用」「Chat-Ref」「作業ログ」から、特定
 
 チャット側も mj-logs で確かめるが、写るのは #440 以降に push されたログだけなので、受け手側のこの確認は省かない。
 
+### 同じ Chat-Ref と雛形の行（CLAUDE.md「Chat-Ref」から移した）
+
+- 到達確認は`git log --all --grep="CHAT-MMDD-XXX-nn" --oneline`
+- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けをログに書くのは、チャット側の写し漏らしを知らせるため
+
 ### 作業を再開するとき
 
 止まった作業を再開するときも、同じ Chat-Ref のコミットの有無を `git log --all --grep="<Chat-Ref>"` で確かめる。
@@ -107,7 +112,8 @@ CLAUDE.md「ブランチ運用」「Chat-Ref」「作業ログ」から、特定
   生成物をコミットしなければ衝突は起きないが、プレビューが本番と同じ中身になり確認に使えない（CHAT-0918-SX-09 で実際にそうなった）。
   そのため cloudflare の自動再生成と同じファイルを両側で変えることになり、`origin/cloudflare` の取り込みで衝突しうる
   （SX-10 で `saikyo/` の12ファイル。SX-11・SX-15 の取り込みは衝突なし、#384・#406）
-  - 衝突した生成物は、どちらの版も選ばず、取り込んだ後のスクリプトで生成し直して解く（決まりは CLAUDE.md「ブランチ運用」）。
+  - **`origin/cloudflare`の取り込みで生成されたページだけが衝突したら、どちらの版も選ばず取り込んだ後のスクリプトで生成し直して解き、双方の変更が残っていることをログに書く。
+    それ以外（生成スクリプト・CSS・JS・データ・設定など）が衝突したら止まる。** 指示文が解き方を書いている衝突は、そのとおりに解き、解いた後の該当箇所をログに引用する（入口は CLAUDE.md「ブランチ運用」）
     衝突の印を消すには cloudflare 側を採ってから（`git checkout --theirs -- <ファイル>` → `git add`）再生成で上書きする。
     再生成の後、採った側の中身が残っていない（全ファイルが今回の生成物になっている）ことと、双方の変更（自分の変更と、cloudflare 側で入ったシートの変更など）が残っていることを確かめ、ログに書く
     （CHAT-0924-TQ-18・TQ-26・TQ-27 で `title/` の生成物だけが衝突し、この形で解いた）
@@ -120,6 +126,17 @@ CLAUDE.md「ブランチ運用」「Chat-Ref」「作業ログ」から、特定
   最強戦の年度ページと `saikyo_results.html` に SX-07 で貼った対局日が出た（自動再生成 9b6d459）。
   マージの指示を書く側・受ける側とも、再生成で変わる範囲（対象ページと、前回の再生成以後のシートの変更）を先に見積もる
 
+## マージするとき（CLAUDE.md「ブランチ運用」から移した）
+
+- 成果物の`cloudflare`へのマージは、指示文に「マージ: 承認済み（チャットで）」があるときだけ行う。無ければ完了を報告し、判断待ちで止まる。
+  承認は処理中に求めない（hook は確認を出さない）。
+  - ドキュメントのみの変更（CLAUDE.md、docs/配下、README等）は、完了を報告したうえでセッションがマージしてよい
+  - 承認済みでも、指示文の確認が1つでも通らない・止まる条件に当たった・前提が崩れた（指示文の想定と実物が違う等）ときは、マージせずに報告する
+  - `docs/`配下のみの変更では Workers Builds が走らず check-run も出ない（#171）。`docs/`外のドキュメントを含むpushではデプロイが1回走る（表示は変わらない）
+- **マージの手順:** 作業ブランチから`git push origin <作業ブランチ>:cloudflare`とし、cloudflareはチェックアウトしない。
+  **push直前に必ず再fetchし、`git merge-base --is-ancestor origin/cloudflare HEAD`で push 先が自分のHEADの祖先であることを確認すること**
+  （他セッションのfetchで`origin/cloudflare`が進むため、取り込み時点を前提にすると他セッションのコミットを巻き戻す）
+
 ## 未コミットの変更を戻すとき（2026-09-27）
 
 生成物の差分を確かめたあとなどに作業ツリーを戻すときは、**戻すファイル・ディレクトリを名指しする**
diff --git a/docs/notes/chat-side-operations.md b/docs/notes/chat-side-operations.md
index 9ff7d3b6..865ca023 100644
--- a/docs/notes/chat-side-operations.md
+++ b/docs/notes/chat-side-operations.md
@@ -60,6 +60,8 @@
   別のブランチや許可ルールで回避させない（Code 側は `docs/notes/cloud-sessions.md`「作業ブランチの用意」）
 - **平野さんが「申送り」と言ったら**、振り返りの知見のうち**機械的に確かめられる手順（仕組み・検査・issue）に落とせるものだけ**を
   issue や資料に書き残す指示文を作る（抽象的な心得は書かない）。**追記先の残り容量を測らせ、規則だけを書かせる**（事例は archive へ）
+- **規約が守られないときは、内容ではなく書き方を疑うこと。** 手順の1つとして並べた規約より、他の作業との順序
+  （「〜より先に行う最初の手順」）で書いた規約のほうが守られる（CLAUDE.md「作業ログ」節の着手時の push。CLAUDE.md「更新ルール」から移した）
 
 ## 指示文を書くときの注意
 
diff --git a/docs/notes/cloud-sessions.md b/docs/notes/cloud-sessions.md
index 5d1be475..11827eed 100644
--- a/docs/notes/cloud-sessions.md
+++ b/docs/notes/cloud-sessions.md
@@ -68,7 +68,7 @@ Chat-Ref の確認・`git branch --merged` が誤る。**これらの判定の
 
 セッションの git プロキシがブランチの削除を拒否する（`git push origin --delete` が HTTP 403）。削除はしない。
 マージ済みの `work/*` は `delete-merged-branches.yml` が毎日、先頭が24時間より前のものを削除する（削除の記録もワークフローの出力に残る）。
-CLAUDE.md「ブランチ運用」の「作業ブランチも削除する」は、このワークフローに任せることで満たす。
+docs/notes/branch-operations.md「作業ディレクトリの分離（Codespace）」の「作業ブランチも…削除する」は、このワークフローに任せることで満たす。
 `claude/*` は対象外（#440）。
 
 ## ネットワーク
diff --git a/docs/notes/handover-archive-2026.md b/docs/notes/handover-archive-2026.md
index 2ca9cd2b..ad62bfd2 100644
--- a/docs/notes/handover-archive-2026.md
+++ b/docs/notes/handover-archive-2026.md
@@ -849,6 +849,20 @@ docs/handover.md:
 - 5章「現行サイトで小さく作れるもの」の済んだこと: 平野さんの決定（2026-10-05・06）で #277 → #388 の順に作る。#277 は入口の年の切り替えとして 2026-10-09 に済み、閉じた。#377（辞書のカテゴリ）に続き、Mリーグのカテゴリ追加・Gboard 形式・ページの作り直しも済み（#515・#522、閉じた）
 - 「最終更新: 2026-10-07」の3行（作業ログの書き方の規則と自動削除の判定〈#513〉、予約実行を Worker から起動する作り〈#504〉、Actions の実行結果の書き出し〈#498〉）は、10-09 までの変更に置き換えた
 
+### 2026-10-10 の圧縮で CLAUDE.md から外した記述（#492）
+
+規則は CLAUDE.md に残すか、`docs/notes/branch-operations.md`「マージするとき」・「生成物を含む作業ブランチを取り込む・マージするとき」・「同じ Chat-Ref と雛形の行」、
+`docs/logs/_template.md`「ターミナルへ返す最終報告」、`docs/notes/chat-side-operations.md`「Claude Code とのやり取り」へ文言を変えずに移した。ここには、移さずに外した理由・補足の文言を残す。
+
+- 「構成」: 本番反映の設定は「ダッシュボード側にありコードから追えない」
+- 「禁止事項」: `_redirects` の先頭行を消すとトップページが404になるのは「`html_handling: "none"` のため」。`sourceMappingURL` を残さないのは「`.map`を同梱しないため404になる」。パスを変えるのは「ブラウザに30日キャッシュが残る」ため
+- 「ブランチ運用」: Codespace の worktree の規則の「**`origin/cloudflare`を明示してworktreeを作り、その中で作業する。**」の手順・片付けは同「作業ディレクトリの分離（Codespace）」にある
+- 「Chat-Ref」: 識別子の重複確認を省かないのは「チャット側の確認は見える範囲が狭いため」
+- 「issueの着手ルール」: セッション URL が取得できない場合の例「デスクトップアプリ等」。クローズ時に「状況:」ラベルを外す理由「残るとクローズ済みなのに未対応・保留中に見える」
+- 「コミットのルール」: 無関係なハンクを混ぜない理由「混入すると `git blame` / `git log -- <file>` が無関係なissueを指す」。シェル経由で書かない理由の「コマンドの出力が混入する」
+- 「更新ルール」: 見出しで参照する理由「行番号はすぐずれる」。置き換えの規則の「「→その後こうした」という追記型にしない」
+
+
 ## Open issue の棚卸し（INV、2026-10-02〜03）
 
 Open 221件を4段で再編成した。INV-01 で全件の本文・コメントを読んで分類表（A 完了・B 重複・C 相反・D 一部残り・E 範囲・F 期限）だけを出し、
````

## 報告

- 状態: 判断待ち / 続き: CHAT-1010-REV-04
- ブランチ: work/1010-rev
- ログ: https://github.com/retroeater/mj/blob/work/1010-rev/docs/logs/CHAT-1010-REV-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rev
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち。成果物は 3e7b4cb0）
- issue: #492
- 判断が必要なこと:
  - マージの可否（整理後の CLAUDE.md の全文と移し先の差分は `## 経過`）。CLAUDE.md 26,084 → 22,098（−3,986）。移し先: branch-operations.md +2,083・_template.md +1,396・chat-side-operations.md +356（25,184、警告域の外）・cloud-sessions.md +55・handover-archive-2026.md +2,022
  - 20KB に届かなかった（理由は `## 経過`「対応表」の後）。さらに減らすなら、規則の文言を縮めるか、毎回守る規則（作業ログ・Chat-Ref の確認）の一部を _template.md・branch-operations.md へ移して入口を1行にするかの判断が要る
  - 移したのは場面限定の手順と理由だけで、規則は CLAUDE.md に要旨を残した。そのため「マージするとき」「生成物の衝突」は CLAUDE.md と branch-operations.md の両方に要旨と全文がある（同じ内容を2箇所に書かない規則との兼ね合い）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e22d14e5）: https://github.com/retroeater/mj-logs/tree/main/guide/e22d14e5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e22d14e5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e22d14e5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e22d14e5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e22d14e5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e22d14e5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e22d14e5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
