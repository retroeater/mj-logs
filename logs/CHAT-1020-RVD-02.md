# CHAT-1020-RVD-02

- 着手日時: 2026-10-20
- 対象issue: #492
- ブランチ: work/1020-rvd
- 着手時HEAD: 70c0cc5f3270eca6482841381655d7235df6fde2

## 指示

【Claude作成】Claude Code 向け指示：ガイド4文書から Codespace 前提の記述を外す（RVD-01 の続き、同じ作業ブランチ、判断待ちで止まる） Chat-Ref: CHAT-1020-RVD-02 マージ: 判断待ちで止まる（RVD-01 と合わせて整理後の全文をチャット側が読み比べてから、マージの指示を出す） 貼る時機: CHAT-1020-RVD-01 の報告（判断待ち）の後、同じセッションに貼る 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-rvd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1020-rvd を続けて使う（RVD-01 の整理の上に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1020-RVD-01 のログの `## 報告` が「判断待ち」で仕上がっていることを確かめ、そうでなければ何もせず止まる。

目的
Claude Code の実行環境はクラウドセッションだけになったので、容量制限のある4文書から Codespace を前提にした記述を外し、サイズを減らす（#492。RVD-01 の続き）。
決定（2026-10-20、平野さん）

* Code の実行環境は 2026-10-14 からクラウドセッションのみで、Codespace は使っていない。CLAUDE.md・docs/handover.md・docs/instruction-template.md・docs/notes/chat-side-operations.md の Codespace の記述（worktree の手順、Codespace 用の「作業ブランチ」の文面）は外してよい

前提（チャット側。平野さんの決定ではない）
チャット側が guide/e18c2138 を `grep -i "codespace\|worktree\|/workspaces/mj"` で見つけた箇所（RVD-01 の整理で行が動いていることがある。実物で確かめる）:

* CLAUDE.md「ブランチ運用」の「Codespace: `/workspaces/mj`は全セッションが共有する。…worktree の中で作業し…」の項とその続き（「クラウドセッション（…）の読み替えはdocs/notes/cloud-sessions.md」）: Codespace の部分を外し、「クラウドセッションで実行する。手順は docs/notes/cloud-sessions.md」の趣旨の1行にする。「複数セッションが並行編集するため、セッションごとに作業ブランチと作業ディレクトリを分ける（#198）」の「作業ディレクトリ」が Codespace だけの話なら外す
* CLAUDE.md「引き継ぎ」の「起動時に読み込んだこのファイルは古いことがある（確かめ方: …（Codespace））」: クラウドセッションでも起こるか（起動時の CLAUDE.md が origin/cloudflare の最新と違いうるか）を docs/notes/cloud-sessions.md で確かめ、起こらないなら外し、起こるなら参照先をクラウドの手順に直す
* CLAUDE.md「更新ルール」の「作業する worktree の上で測る」: 「作業ブランチの上で測る」に
* docs/handover.md 3章「Claude Code の実行環境は Codespace（…）かクラウドセッション（…）」: 「クラウドセッション（`docs/notes/cloud-sessions.md`）」だけに。6章の表の `docs/notes/session-network.md`・`docs/notes/branch-operations.md` の行は、Codespace だけの内容（Rebuild・gh の認証・worktree の分離）を「旧 Codespace の記録」と分かる書き方にする（文書自体は消さない）
* docs/instruction-template.md「作業ブランチ」の行の文面3つのうち「Codespace で既存の作業ブランチを続ける」の1つを外し、残る2つ（クラウドで新しく作る・未マージを続ける）はそのまま。冒頭の「式の向きが2つで逆なので」は残る2つで成り立つかを確かめて直す。雛形の「共通手順」の行の「〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉」は、CLAUDE.md の直しと整合する短い形（「〈手順は docs/notes/cloud-sessions.md〉」など）にする。この共通手順の行は多くの指示文が写すため、直した後の文面を報告に書く（チャット側が以後の指示文で使う）
* docs/notes/chat-side-operations.md: 「確認用 URL」の「ビルドが走らなかったときは Codespace の確認用 URL（…）」は外す（クラウドの代替があれば書き換える）。「場面ごとに読む文書」の表の「マージ・ブランチ操作・`/workspaces/mj` の操作を含む指示」から `/workspaces/mj` を外す。同表の「Code に環境に依存する操作…」の行の `docs/notes/session-network.md`（Codespace）はクラウドの文書だけにする。「読み方」の表の「PC では Chrome でも読める」は Codespace と無関係なので残す
* docs/notes/cloud-sessions.md が CLAUDE.md の「読み替え」として書かれている箇所（「`/workspaces/mj` と読み替える」など）は、CLAUDE.md から元の記述が消えると意味が通らなくなる。cloud-sessions.md の該当の節を読み替えでない手順として書き直す（容量の上限は無い）
* 外した記述は `docs/notes/handover-archive-2026.md` の「2026-10-20 の整理で4文書から外した記述（#492）」の節（RVD-01 で作った）に「Codespace 前提の記述」として足す

手順

1. RVD-01 と同じ論点の確認は済んでいるため、`git branch -r --no-merged origin/cloudflare` の各ブランチのうち4文書・cloud-sessions.md を変えているものだけを確かめ直し、ログに書く（重なっても止まらない）。#492 の着手中のコメントは RVD-01 のものがあるため足さない
2. 前提の箇所を実物で確かめて直し、grep で4文書に Codespace・worktree・`/workspaces/mj` が残っていないことを確かめる（残すものは理由を報告に書く）。対応表（箇所 → 処理）を経過に書く
3. 4文書の前後のサイズ（バイト、RVD-01 の後 → この指示の後）を報告に書き、整理後の4文書の全文と cloud-sessions.md・archive の差分を経過に貼る。`python3 scripts/check_asset_limits.py` と `python3 -m unittest discover -s scripts/tests` を通す。RVD-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1020-RVD-02` を足す

止まる条件

* 0章で RVD-01 の報告が判断待ちで仕上がっていない
* Codespace の記述を外すと規則（クラウドでも要る手順）が失われる箇所がある（外さずに報告に書く。止まるのはその箇所だけ）
* 取り込みで、両立しない衝突が出た（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の「判断が必要なこと」に、マージの可否と、直した後の「共通手順」の行の文面を書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-RVD-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-RVD-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0章: ログの「指示」欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致した。CHAT-1020-RVD-01 の `## 報告` は「状態: 判断待ち」で仕上がっている（70c0cc5f3）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある。ただし貼られた指示文では、題の行から「作業ブランチ」の行までが改行の無い1行につながって届いた（中身は欠けていない。「指示」欄にはそのまま写した）
- Chat-Ref の重複: `git log --all --grep="CHAT-1020-RVD-02"` は0件。最初の指示ではないため `XXX` の確認は対象外
- 作業ブランチ: ローカル・リモートの work/1020-rvd はどちらも 70c0cc5f3 で、`git merge-base --is-ancestor origin/cloudflare HEAD` は真（取り込みは不要、衝突なし）
- 起動時の CLAUDE.md: このセッションは再開で、起動時に読み込まれた CLAUDE.md は RVD-01 の後の作業ブランチの版（70c0cc5f3）。`origin/cloudflare` は着手時から CLAUDE.md を変えていない

### 手順1: 未マージのブランチ

`git branch -r --no-merged origin/cloudflare` の8本のうち、4文書・cloud-sessions.md を変えているのは origin/work/1020-rvd（このブランチ）だけ。ほかの7本（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-lvp・1018-log-367-migrate・1020-plg）は対象のファイルを変えていない。

### 手順2: 対応表

| 箇所 | 確かめた事実 | 処理 |
|---|---|---|
| CLAUDE.md「ブランチ運用」の Codespace の項とクラウドの読み替えの参照 | 2行の項 | 「**Code はクラウドセッションで実行する。ブランチの用意・`gh` の代わり（GitHub MCP）などの手順はdocs/notes/cloud-sessions.md**」の1行にした |
| CLAUDE.md「ブランチ運用」冒頭の「作業ブランチと作業ディレクトリを分ける（#198）」 | 作業ディレクトリの分離は Codespace の共有の作業ツリーの話（branch-operations.md「作業ディレクトリの分離（Codespace）」）。クラウドはセッションごとにクローンが別 | 「作業ディレクトリ」を外した |
| CLAUDE.md「引き継ぎ」の起動時の CLAUDE.md | クラウドでも起こる。CLAUDE.md は起動時のクローンから読み込まれ、続けて使うセッションではその後に origin/cloudflare が変わりうる（このセッションも再開で、起動時の版は作業ブランチのもの）。cloud-sessions.md には手順が無かった | cloud-sessions.md に「起動時に読み込んだ CLAUDE.md が古くないか確かめる」を作り（`git log --oneline HEAD..origin/cloudflare -- CLAUDE.md` に1件でもあれば `git show origin/cloudflare:CLAUDE.md` で読む）、CLAUDE.md の参照先をそこへ直した |
| CLAUDE.md「更新ルール」の「作業する worktree の上で測る」 | — | 「作業ブランチの上で測る」にした |
| CLAUDE.md「作業ログ」の作業ファイルの置き場所（session-network.md「作業ファイルの置き場所」） | 参照先は `/workspaces/` 以下に置く Codespace の手順で、クラウドでは当てはまらない（コンテナは回収される）。規則（後で使うものは消えない所へ、ログに置き場所を書く）はクラウドでも要る | cloud-sessions.md に「作業ファイルの置き場所」を作り（作業ブランチにコミットして push する、か使い捨てとログに書く）、参照先を直した（前提に無い箇所。grep の語には当たらないが Codespace 前提だったため） |
| handover 3章「実行環境は Codespace（…）かクラウドセッション（…）」 | — | 「Claude Code の実行環境はクラウドセッション（`docs/notes/cloud-sessions.md`）。」にした |
| handover 2章「セッションからの到達は `docs/notes/session-network.md`（#328）」 | session-network.md の到達の実測は Codespace のもの。クラウドの許可ドメインは cloud-sessions.md「ネットワーク」 | 参照先を cloud-sessions.md「ネットワーク」にした（前提に無い箇所） |
| handover 6章 session-network.md・branch-operations.md の行 | session-network.md の節: Rebuild・2026-09-14〜19 の到達の実測・Codespace の確認用 URL・gh の認証・User-Agent・シートの行番号・作業ファイルの置き場所。branch-operations.md の「（Codespace）」の節2つと「`/workspaces/mj` を pull するとき」 | 今も使う内容と「旧 Codespace の記録」の節を分けて書いた。文書は消していない。branch-operations.md の冒頭にも同じ注記を1行足した |
| handover 6章 cloud-sessions.md の行 | 「CLAUDE.md の読み替え」と書いていた | 「クラウドセッション（Claude Code の実行環境）の手順（起動時の CLAUDE.md の確かめ・作業ファイルの置き場所・…）」にした |
| instruction-template.md「作業ブランチ」の文面3つ | — | 「Codespace で既存の作業ブランチを続ける」を外した。残る2つは変えていない。「式の向きが2つで逆なので」は残る2つ（新しく作る: `is-ancestor origin/work/<識別子> origin/cloudflare`、続ける: `is-ancestor origin/cloudflare HEAD`）でも向きが逆のため、そのまま |
| instruction-template.md 雛形の「共通手順」の行 | — | 「〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉」を「〈手順は docs/notes/cloud-sessions.md〉」にした（文面は `## 報告`） |
| chat-side「確認用 URL」の Codespace の確認用 URL | クラウドの代わりの手段は無い（ビルドが走らなかったときの書き方は `_template.md` の「確認用URL」の行にある） | 外した |
| chat-side「場面ごとに読む文書」の2行 | — | 「`/workspaces/mj` の操作」と「`docs/notes/session-network.md`（Codespace）」を外した |
| cloud-sessions.md の読み替えの記述 | 題と冒頭が「読み替え」、「始め方」に `/workspaces/mj`・worktree の pull・片付けが当てはまらない旨、「ブランチの削除」に branch-operations.md の Codespace の節を満たす旨 | 題を「クラウドセッション（Claude Code on the web）の手順」にし、冒頭・2か所を読み替えでない文にした。「CLAUDE.md の `gh` のコマンドは、同じ操作の MCP ツールに読み替える」は残した（CLAUDE.md に `gh issue list`・`gh issue close` が残るため） |

- 4文書の grep（`codespace|worktree|/workspaces/mj`、大文字小文字を区別しない）で残ったのは handover 6章の2行だけで、どちらも「旧 Codespace の記録」と示すための語（前提の指定どおり）
- 規則が失われる箇所: 無い。Codespace の節のうちクラウドでも要る規則（作業ファイルの置き場所・起動時の CLAUDE.md の確かめ・単独のコマンドでのブランチ操作・`checkout -B` の不使用）は cloud-sessions.md にある（後の2つは前からある）
- 外した記述は archive「2026-10-20 の整理で4文書から外した記述（#492）」に「Codespace 前提の記述」として足した
- 成果物のコミット: 6de55b38a

### 手順3: サイズと検査

| 文書 | RVD-01 の後（70c0cc5f3） | この指示の後（6de55b38a） | 差 |
|---|---:|---:|---:|
| CLAUDE.md | 23,446 | 23,129 | −317 |
| docs/handover.md | 21,990 | 22,108 | +118（6章の2行の注記） |
| docs/notes/chat-side-operations.md | 22,240 | 21,980 | −260 |
| docs/instruction-template.md | 12,455 | 11,978 | −477 |
| docs/notes/cloud-sessions.md | 16,601 | 17,658 | +1,057（上限なし） |
| docs/notes/branch-operations.md | 23,190 | 23,359 | +169（上限なし） |
| docs/notes/handover-archive-2026.md | 104,661 | 107,864 | +3,203（上限なし） |

着手時の cloudflare（03eef856d）からの通算: CLAUDE.md 24,859 → 23,129、handover.md 22,937 → 22,108、chat-side-operations.md 22,349 → 21,980、instruction-template.md 14,325 → 11,978。

- `python3 scripts/check_asset_limits.py`: すべて OK
- `python3 -m unittest discover -s scripts/tests`: Ran 1457 tests、OK
- RVD-01 のログの `## 報告` の状態を「判断待ち / 続き: CHAT-1020-RVD-02」にした

### 整理後の4文書の全文（6de55b38a）

各文書を `~~~~` の囲みで貼る。

#### CLAUDE.md

~~~~markdown
# CLAUDE.md

# ryoei.pro

## 概要

日本プロ麻雀連盟の選手データベースを含む個人サイト。

### 引き継ぎ

経緯・現状・次にやることは docs/handover.md（文脈が要るときはまず読む）。
**起動時に読み込んだこのファイルは古いことがある**（確かめ方: docs/notes/cloud-sessions.md「起動時に読み込んだ CLAUDE.md が古くないか確かめる」）。

### 構成
- 静的HTML。ビルド工程なし。ページの一覧はdocs/notes/static-generation.md「ページの一覧」（件数の正は`python3 scripts/regenerate.py --list`）
- `llms.txt`（AIクローラー向けのページ索引）は手書きの静的ファイル1枚で、生成スクリプトは持たない（#161）
- Cloudflare Workersの静的アセットとして配信（`wrangler.jsonc`、assets.directory は `./`）
- **本番反映は Cloudflare Workers Builds（ダッシュボードのGit連携）が `cloudflare` への push を検知して行う**（`chore: regenerate ...` も含む。
  Actionsにデプロイのジョブは無い、#169。docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」）
- Bootstrap 5.3.8 をローカル配信（assets/vendor）。CDNは使わない
- ページ本体（例: `jpml_pros.html`）とロジック（同名の `.js`）は分ける。ページ末尾で navbar.js を読み込んで共通ナビを描画する
- **手書きHTMLを新規に追加する前に**docs/notes/static-generation.md「navbar.js と検索欄」を読む（hrefはルート相対〈#162〉、`data-search="off"`〈#163〉）
- skill（`.claude/skills/`）と git の hook（`.claude/hooks/`）の導入・入れ直し、claude.ai のアカウントの plugin の扱いはdocs/notes/skills.md

### データの流れ
- 選手データ・成績データはすべてGoogleスプレッドシートが正本。ビルド時生成のページは`scripts/generate_<ページ名>.py`が読んで焼き込む
  （型ごとのページと仕組みはdocs/notes/static-generation.md「ページの一覧」「現行の仕組み」）
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
- 外部ドメインへの依存を増やさない（CSP〈`_headers`〉の許可を足すことになるため）
- `.assetsignore` に開発用ファイルを列挙。公開対象を増やさない。新しいディレクトリ・ファイルは公開してよいか確認し、公開しないものは追加する（#133）
- ビルド・lint の自動化コマンドはなし。HTML/JSの変更はブラウザで直接確認する
- 外部の状態を待つ待機は上限15分（超えたら状態を書き「未確認の項目」に回して進む。自分のコマンドの実行は対象外）。共有の定数・関数を変えるときは参照を洗い出し、
  マージ前に全ページを再生成して差分を確かめる（docs/notes/github-actions.md「ワークフローを手動実行するとき」、docs/notes/static-generation.md「生成スクリプトの構成」）
- テストは`scripts/tests/`（unittest、標準ライブラリのみ）。実行は`python3 -m unittest discover -s scripts/tests`。週次の`check-meibo.yml`も実行する。
  `workers/<名前>/`のコード・テスト・生成物（`data.json`など）を変えたときは`node --test 'workers/<名前>/test/*.test.mjs'`も回す（CI では回っていない）

### 禁止事項（理由は参照先）
- `gh-pages` ブランチに触らない（docs/handover.md「gh-pages ブランチは触らない」）
- `CLOUDFLARE_API_TOKEN` をGitHub Secretに登録しない。到達できても `wrangler deploy` しない（二重デプロイ。docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」）
- `_redirects` 先頭の `/  /index.html  200` を消さない（トップページが404になる。docs/notes/cloudflare.md「配信設定: html_handling・_redirects・canonical・_headers」）
- `wrangler dev` は必ず `npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state` で起動する（素だと無限リロード。docs/notes/cloudflare.md「ローカル確認（wrangler dev）」）
- assets/vendor 配下に `sourceMappingURL` コメントを残さない（#99）。更新時はファイルのパスを変える（30日キャッシュ、#92）
- sitemap の lastmod を手で書き換えない（`scripts/update_sitemap_lastmod.py --from-git`が導出する。docs/notes/sitemap-lastmod.md、#265）
- `git stash`・`reset --hard`・`clean`・`checkout .` は mj-git-guard が止める（docs/notes/skills.md「git の hook」。戻し方は docs/notes/branch-operations.md「未コミットの変更を戻すとき」）

## ブランチ運用

複数セッションが並行編集するため、セッションごとに作業ブランチを分ける（#198）。場面ごとの手順はdocs/notes/branch-operations.md。
**チャット側の指示文がこの節と食い違う（`cloudflare`上での直接作業など古い前提を含む）ときは、指示文には従わずこの節に従うこと**（#205）。

- **`cloudflare`: 統合・デプロイ専用。セッションはここへ直接pushしない**（マージ＝本番反映）
- **`work/<識別子>`: セッションの作業ブランチ。** 識別子は Chat-Ref から取る（`CHAT-0913-QMX-02`なら`work/0913-qmx`）。指示文に指定が無くても切る。
  複数issueを1ブランチで扱ってよいが、作業に関係しない独立した変更（ルール追記・ドキュメントのみの修正等）は別ブランチに分ける
- **Code はクラウドセッションで実行する。ブランチの用意・`gh` の代わり（GitHub MCP）などの手順はdocs/notes/cloud-sessions.md**
- **成果物の`cloudflare`へのマージは、指示文に「マージ: 承認済み（チャットで）」があるときだけ行う。** 無ければ完了を報告し、判断待ちで止まる。
  承認は処理中に求めない。ドキュメントのみの変更は完了報告のうえマージしてよい。承認済みでも、確認が1つでも通らない・止まる条件に当たった・
  前提が崩れたときはマージせずに報告する（docs/notes/branch-operations.md「マージするとき」）
- **マージは`git push origin <作業ブランチ>:cloudflare`で行い、push直前に再fetchして`git merge-base --is-ancestor origin/cloudflare HEAD`を確かめる**（docs/notes/branch-operations.md「マージするとき」）
- **`.github/workflows/`を追加・変更する作業では、着手時にdocs/notes/github-actions.md「ワークフローを変更したとき」を読み、マージ前に作業ブランチで手動実行して確かめる。実行できなければ報告して判断を仰ぐ**
- 長期間マージされないブランチは、定期的に`cloudflare`を取り込んで乖離を小さく保つ
- **`origin/cloudflare`の取り込みで衝突したら、docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」に従う**（生成されたページだけなら取り込んだ後のスクリプトで生成し直して解く。
  `docs/decisions/*.md`・`docs/logs/` の末尾への追記どうしは両方を残して解く。それ以外が衝突したら止まる。指示文が解き方を書いている衝突はそのとおりに解き、該当箇所をログに引用する）
- **ブランチを削除する前にdocs/notes/branch-operations.md「ブランチを削除するとき」を読む。読むまで削除しない。** 未マージのブランチは削除しない。削除直前の先頭SHAを記録に残す（#207）
- **このブランチ運用ルールに反した作業（自他を問わない）は#176にコメントで記録する**（記録対象と書式は#176の「スコープ変更」節）

## Chat-Ref

チャット（claude.ai）で作った指示文に付く識別子（`CHAT-MMDD-XXX-nn`: 発行日・チャットセッション識別子・連番）。新しい識別子は英大文字3文字。
既存の識別子（2文字、`A`・`DOC`・`K7`・`W2` など）は有効なままで、重ねない（#474）。チャット側の規則はdocs/notes/chat-side-operations.md。

受け取る側（Claude Code）:

- **着手前の確認**: 同じ Chat-Ref のコミット／`XXX` の重複（最初の指示）／0章ゲート（実装を含む指示、#265・#336）の3点。当たれば着手せず報告して止まる（細目は docs/notes/branch-operations.md「Chat-Ref の着手前の確認」）
- **指示文に書かれた事実はチャット側が会話の記憶から書いたもので、誤っていることがある**（#319）。前提と実物が食い違ったら、指示に合わせて手を入れず、中断して報告する
- **指示文の記述同士が食い違ったときは、個別の指定より前提・ルールの側を優先し、その旨を報告する**
- **ガイド文（CLAUDE.md・handover.md・docs/notes/ のチャット側の文書）に規則を足す指示に「知見 → 選んだ層 → 理由」の表が無ければ、着手せず報告して止まる**（センサーを先に検討させるため、#492）
- **着手時に、指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順、docs/instruction-template.md）の欠けを確かめ、欠けた行の名前を止まらずにログの経過と `## 報告` の「判断が必要なこと」に書く**
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
- 構成は ヘッダ・`## 指示`〈貼られた指示文をそのまま〉・`## 経過`〈詳細はすべてここ〉・`## 報告`（`_template.md`をコピー）。
  **`## 報告`はログの末尾に置き、作業の最後に更新してpushする。** チャット側はこの節だけを読むため、**10項目を省かず、該当が無ければ「なし」と書く**。
  各項目（予約実行の初回の日時を含む）・状態の書き方（「完了」の条件・` / 続き: CHAT-…`）は`_template.md`とdocs/notes/branch-operations.md「作業ログの寿命」
- **ログの節を書き換えるときは、`## 指示`欄より後ろの見出しを相手にし（`## 報告`はファイルの最後の一致）、push 前に`## 指示`欄が変わっていないことを確かめる**（理由は`_template.md`）
- ログは作業ブランチにだけpushし、成果物と別のコミットにする。`cloudflare`へは指示の最後のマージ1回で成果物と一緒に入れる（マージの結果を書く docs/logs のみの追いのpushは可）
- 後の指示で使うスクリプト・中間データの置き場所は docs/notes/cloud-sessions.md「作業ファイルの置き場所」（scratchpad とコンテナは消える）
- **ターミナルへ返す最終報告は`_template.md`「ターミナルへ返す最終報告」の形**（最後の行は`Chat-Ref: CHAT-MMDD-XXX-nn`、直前は「ログ（公開）」の行。詳細はログの`## 報告`）。
  **質問して止まるとき・pushに失敗したときだけ、内容をターミナルに書く**（ログに届かないため）
- **指示の完了時（完了・判断待ち・中断の最後の push）に、その指示の「決定」節と作業中の平野さんの回答（grill を含む）を
  `docs/decisions/<分野>.md` に足す**（ログと同じコミットでよい。書き方は`docs/decisions/README.md`）

## issueの着手ルール
- **issueに着手したら、コードを触る前にそのissueへ「着手中」のコメントを残す**（並行するセッションから着手状況を知る唯一の手段）。
  セッションのURL（`Claude-Session` と同じ。取得できなければ Chat-Ref の `XXX`）を含める
- **着手する前に、そのissueに他セッションの着手中コメントが無いか確認する。** あれば着手せず、ユーザーに確認する（#157）
- **issueを新規作成する前に、同じ主題のissueをクローズ済みも含めて検索する**（`gh issue list --state all --search "<キーワード>"`）。
  **issue の起票やページ・機能を作る指示では、着手の前に同じ目的の issue と未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチの
  コミットの件名とログ）を確かめ、見つかったら作る前に止まって報告する**（0章ゲートは触るファイルの重なりを見るもので、目的の重なりは拾えない）
- **作業を中断・放棄したときも、その旨をコメントに残す**（着手中のまま放置されると、他セッションが着手を見送り続ける）
- **issueをクローズするときは「状況:」ラベル（待ち/対応中/保留）を外す**（#112）
- タスク管理は docs/handover.md「タスク管理」（期日はカレンダー「ryoei.pro」）

## 新サイト送りの issue（親 #296）
- **issue を新サイト送りにする・取り消す前に #296 の本文「新サイト送りにするとき・やめるとき」を読む。**
  コメント「親: #296」と sub-issue の登録は片方だけにしない

## コード規約
- 着手中のissueと無関係なコードは触らない。自分が書いた・変更した箇所以外にコメントを足さない。変更行数は最小にする
- 書く前に、既存の形（同じ型のページ・`scripts/`の共通関数・定数・生成の仕組み）で足りるか確かめ、あれば使う。
  実装が1つだけの抽象化・「後のため」の雛形・変わらない値の設定化は作らない（増えた形が次の作業の読む量になる）
- 新しい依存（npm・pipのパッケージ、CDN、外部API、新しいワークフロー）は足さない。要るときは足さずに止まって報告する（判断は平野さん）
- 上限を分かって選んだ簡略化（素朴な走査・固定値・一部の場合だけの対応など）には、`簡略化:`のコメントで上限と次の一手を1行残す
  （`grep 簡略化:`で台帳にするため語を変えない）
- 分岐・ループ・解析（文字列・日付・HTMLの読み取り）・金銭・セキュリティに関わるロジックを足す・変えるときは、検査を1本残す
  （置き場所と実行は「判断・作業の原則」のテストの行）。自明な一行には要らない
- 繰り返し使う値・意味のある値（列番号、画像サイズ、URL、閾値）は定数にする。一度きりで自明な値はインラインでよい
- ネストを深くしない。早期return / continueを使う
- JSのif文は1行でも必ず`{}`を付ける
- コメントは「何を・なぜ」を短く。「どうやって」はコードに語らせる
- 人が読む文章（コメント、コミットメッセージ、応答）は最小限の語数で。賞賛・相槌は不要
- スプレッドシートのファイルは「スプレッドシート」、タブは「シート」と書く。ほかの語（ワークフロー・定期実行・本実行など）も docs/notes/glossary.md に従う（新しく書く文から）

## コミットのルール
- コミット前に `git status` / `git diff --stat` を確認し、**着手中のissueと無関係なファイル・ハンクを含めない。**
  複数の変更が混ざっていたらissueごとに分けてコミットする（#157）
- コミットメッセージ: 件名は50字目安（72字上限）・命令形・末尾ピリオドなし、空行を挟んで本文に「何を・なぜ」。
  既存の`chore:`等の接頭辞形式を維持し、トレーラは「Chat-Ref」節のとおり入れる
- **コミットメッセージ（件名・本文）に閉じるキーワード（close・fix・resolve とその活用形、大文字・コロン付きも含む）の直後に `#番号` を書かない**
  （cloudflare に入ると issue が自動で閉じる。#298・#538 が誤って閉じた。閉じるのは `gh issue close`）
- **文章（ログ・issue本文・コメント・コミットメッセージ）をシェル経由で書かない**（`$`・バッククォートが展開される）。ファイルは Write / Edit、
  コミットメッセージは `git commit -F - <<'EOF'`（引用符付き）。渡し方の細目は docs/notes/branch-operations.md「文章をコマンドに渡すとき」

## CLAUDE.md / handover.md の更新ルール
- ページの移行・追加・削除を行ったときは、同じコミットで docs/notes/static-generation.md「ページの一覧」の件数・ページ列挙と`llms.txt`（手書き、#161）を更新する。
  **新しいページは未公開で入れ、`llms.txt`・navbar・サイトマップには公開の issue で載せる**（docs/new-page-checklist.md、#243）。
  CLAUDE.md にはページを列挙しない。型が増えたときだけ「データの流れ」を直す（#136）
- **docs/handover.md は「現状・ルール・次にやること」のみを書く。** 期日（日付）は書かない（カレンダー「ryoei.pro」が正。チャット側が作る）。 実装の詳細は issue のコメントか `docs/notes/<topic>.md` へ、handover には結論1〜2行と参照だけ
- 記述を更新するときは古い記述を消して置き換える（追記型にしない）。同じ内容を2箇所に書かず、片方は参照にする
- **テスト・hook・ワークフローで確かめるようにした規則は、同じコミットでガイド文から消し、参照だけ残す**（二重に持つと片方が古くなる、#492）
- handover.md / CLAUDE.md をissueやコメントから参照するときは、行番号ではなく節・項目の見出しで書く
- 「最終更新」は日付＋直近の変更3行以内。外した行は archive へ移さず消してよい
- 上限はファイルごとに **CLAUDE.md・handover.md・docs/notes/chat-side-operations.md 各28KB（警告域26KB）・docs/instruction-template.md 16KB（警告域14KB）**
  （`check-assets.yml`、1KB=1024バイト。**作業ブランチの上で測る**）。
  警告域に近づいたら、足す前に `docs/notes/` へ移す。**警告が出たら、上限を上げずに4文書とも整理する**（同じ趣旨の記述をまとめ、
  事例は `docs/notes/handover-archive-2026.md` へ、場面限定の手順は `docs/notes/` へ。退避先には上限を置かない、#421）。
  上限は下げる方向にだけ動かし、上げるのは平野さんの判断。最終目標は CLAUDE.md 20KB・handover.md 24KB 前後（#292・#301 が進んだ時点で見直す、#492）
- **CLAUDE.md・handover.md・chat-side-operations.md には出典としてのChat-Refを書かない**（ログは定期削除で消え、行き先の無い参照になる。issue番号は書いてよい）
~~~~

#### docs/handover.md

~~~~markdown
# 引継ぎメモ

新しい会話でこのプロジェクトを再開するときに、最初に読む文書。
**このファイルを読めば、それまでの経緯を知らなくても作業を再開できる**ことを目的にしている。

**この文書は現状・ルール・次にやることだけを書く。** 実装記録は `docs/notes/`、issue単位の経緯は GitHub Issues、過去の履歴は
`docs/notes/handover-archive-2026.md`。容量の上限と退避方法は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」。

最終更新: 2026-10-20

- **4文書の整理（#492）: CSP の導入済み（`_headers`）に合わせて4章「外部ドメインへの依存を増やさない」を直した**
- **5章の表から閉じた #9・#523・#425・#366 を外した**
- **3章「タスク管理」を CLAUDE.md「issueの着手ルール」の参照先にした**

---

## 0. 新しい会話の始め方

会話開始時に読むのは `docs/handover.md` のみ（平野さんが毎回定型文を貼る前提にしない）。
**マージ・ブランチ操作・ルール追記を行う（指示する）前と会話が長くなったときは、CLAUDE.md の該当節（「ブランチ運用」「Chat-Ref」「CLAUDE.md / handover.md の更新ルール」）も読み直す**（並行セッションが途中でも更新する、#313）。

**チャット側（claude.ai）は `docs/notes/chat-side-operations.md` を読む**（#294）。

**大きな作業の区切りごとに新しい会話を始める**とよい（長い会話は1回あたりのコストが上がる）。

---

## 1. このプロジェクトは何か

平野良栄（日本プロ麻雀連盟のプロ雀士・理事）の個人サイト `ryoei.pro` の改善。

中心となるコンテンツは**日本プロ麻雀連盟のプロ雀士1,100名超のデータベース**と、
鳳凰戦・女流桜花などの成績記録。

### 経緯

GitHub Pages からの移行の相談に始まり、Cloudflare への移行の中で50件以上の改善を行った。
2026年9月9日にドメイン切替が完了し、**現在は Cloudflare Workers で配信中**。

---

## 2. いまの構成

### 配信

| 項目 | 内容 |
|---|---|
| リポジトリ | `retroeater/mj`。**private**（#211） |
| 本番 | Cloudflare Workers（静的アセット配信）。`cloudflare` ブランチ |
| ドメイン | `ryoei.pro` / `www.ryoei.pro`。DNS・レジストラともCloudflare |
| 旧環境 | GitHub Pages。**2026-09-21 に無効化済み**（#84）。`gh-pages` ブランチは履歴として残す |
| ビルド | **なし**。静的ファイルをそのまま配信する |
| プラン | **Cloudflare Pro**（2026年9月9日〜）。$25/月 |

`wrangler.jsonc` の `assets.directory` がリポジトリ全体（`./`）を指すため、
公開したくないファイルは `.assetsignore` に列挙している（追加時のルールは CLAUDE.md「方針」、#133）。

`html_handling`・`_redirects` の先頭行の規則は CLAUDE.md「禁止事項」。canonical は付けない（#113）。例外は `wayhome/` の個別ページで、URL変種を持たないため canonical を持つ（#162）。
`_headers` はセキュリティヘッダ（CSP を含む）とキャッシュ制御（#92）。設定値と経緯は `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」

**本番反映**は CLAUDE.md「構成」（ゲートは #170）。設定値・確認範囲は `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」、セッションからの到達は `docs/notes/cloud-sessions.md`「ネットワーク」（#328）。

### ページ構成

系統は `index.html`（テンプレート由来）・ビルド時生成（型A/A'/D と、`wayhome/`・`saikyo/`・`title/`・`live/`・`books/`・`houou/`・`ouka/`・`jwrc/` などのサブディレクトリ）・
静的なページ の3つ。**件数の正は `python3 scripts/regenerate.py --list`。**
ページの一覧（ページ数・生成スクリプト・公開状態）は `docs/notes/static-generation.md`「ページの一覧」。
**`books/`は2026-09-22開発凍結（自動生成・自動取得を停止、本番はそのまま残す）。`docs/notes/books-freeze.md`。**

### データの流れ

選手データや成績はすべて**Googleスプレッドシート**にある（生成スクリプトが読むブックは7つ。ほかに連盟員名簿のブック1つを `generate_jpml_pros.py`〈英字の姓名・所属、#536〉・`check_meibo.py`・`sync_birthday_calendar.py` が読む）。

- ビルド時生成のページの仕組みは CLAUDE.md「データの流れ」。出力がディレクトリになるページの出力先の正は `scripts/regenerate.py` の `OUTPUT_OVERRIDES`
- `jpml_pros`のYouTubeアイコンだけはYouTube Data APIから取る（#3。取得条件は `docs/notes/static-generation.md`「生成スクリプトの構成（lib/page.py）」）
- `saikyo_pages`は生成する環境で選手写真の結果が揺らぐ。**本番はActionsの生成が正**（`docs/notes/saikyo-page-design.md`「7. 選手写真の更新」）

**スプレッドシートを直しただけでは、ビルド時生成のページには反映されない。** 即時に反映したいときは
`regenerate-page.yml` を`workflow_dispatch`で手動実行する（`target_page`にページ名、または`all`）。
週次（毎週月曜05:37 JST、#103）で`all`が走る。最強戦（saikyo/）は毎朝、写真のリンク切れ検知の後にも再生成する（`regenerate-saikyo.yml`、#537）。セッション内でも `python3 scripts/regenerate.py <ページ名>` で生成できる。

### 自動化

ワークフローの一覧（実行の契機と内容）は `docs/notes/github-actions.md`「ワークフローの一覧」。正は `.github/workflows/`。
**手動実行の前に同「ワークフローを手動実行するとき」を読む**（古い作業ブランチから実行すると、そのブランチの状態で生成物や常設issueが上書きされる）。

---

## 3. 作業の進め方

### 役割分担

| 場所 | 担当する作業 |
|---|---|
| **Claudeとのチャット** | 設計の相談、調査、原因の切り分け、実装案・指示文の作成 |
| **Claude Code** | ファイルの編集、issue操作、コミット・push。コミットの到達確認・差分・マージ判定も行い、結果をログで報告する |

Claude Code の実行環境はクラウドセッション（`docs/notes/cloud-sessions.md`）。
Chat-Ref・並行作業の規則は CLAUDE.md「Chat-Ref」「ブランチ運用」、決まるまでの経緯は archive。

### skill と hook

skill は `.claude/skills/`（`/grill-me`・`/grill-with-docs` など）、git の危険な操作を止める hook は `.claude/hooks/mj-git-guard.py` に置く。
hook の判定は deny（実行させない）だけで、確認（ask）は出さない。入れ方・更新・判定一覧は `docs/notes/skills.md`。

### タスク管理

**GitHub Issues** で管理する（優先順位と状況は本文・ラベル。Projects のボードは使わない。期日はカレンダー「ryoei.pro」〈5章〉。着手中コメントなどの規則は CLAUDE.md「issueの着手ルール」、#157）。

- ラベルは3系統。コロンの後に半角空白が入る（例: `分野: 整理・保守`。正は`gh label list`）:
  `状況:`（対応中、保留、待ち。未着手はラベルなし）、
  `分野:`（SEO/AIO、パフォーマンス、自動化、セキュリティ、整理・保守、インフラ、UI/UX）、
  `対象:`（ページ名。jpml_pros、index、全ページ など）
- 完了分もcloseした状態で残す（判断の経緯を後から追えるように）。エクスポートファイルは廃止（#210）
- **新サイト全体の親 issue は #296（sub-issue の一覧も #296）。** #101 はトップページの作り直しに限る。新サイト送りの手順は #296 の本文
  「新サイト送りにするとき・やめるとき」
- ユーザ登録（サーバ側のアカウント）が前提の機能は #394（保留）に blocked by で依存させる
- 月次の手作業（AI言及・Core Web Vitals・デバイス比率・robots.txt差分・Cloudflare の設定と記録の照合）は
  #304 に集約し、実施ごとにコメントを残す

---

## 4. 押さえておくべき方針

### 現行サイトに作り込みすぎない

**新サイトを別途新規構築する方針**が決まっている（`docs/new-site-design.md`）。
現行サイトはいずれ役目を終えるため、大きな投資は避ける。これまでの判断:

- 構造化データ（#13）は現行サイトでは見送り、新サイトで対応
- 共有ボタン: 新サイトの選手個別ページは #82（新サイト送り）。現行サイトは live/ に共通の部品（#409。`scripts/lib/share.py`・`assets/share.js`・`style.css`）。title/・wayhome/（帰り道）・saikyo/ の共有は外した（saikyo/ の対局ごとの共有は #549 で廃止）。見直しは #530。books/ は凍結中で旧実装
- Astroへの移行（#20/#21）は新サイト構築時に判断。現行サイトの残作業はPythonで進める

**新サイトの第一弾は index.html（#101）。** 他ページと構造が独立し依存が最も少ないため（Astro の試作対象も index.html に変え、#20 はクローズ）。

ボトルネックは #21（Astroへの移行を検討する）の判断。
ここが保留のままだと新サイトの着手ができない。

### 外部ドメインへの依存を増やさない

方針は CLAUDE.md「判断・作業の原則」。許可の正は `_headers` の CSP（外部の `script-src` は Cloudflare Web Analytics の `static.cloudflareinsights.com` だけで、送信先は自ドメインの `/cdn-cgi/rum`。`img-src` は `https:`）。
残る外部依存は画像12ドメインと `image.p-c2-x.abema-tv.com`（最強戦の ABEMA のサムネイル・og:image、#502・#549）。詳細は `docs/notes/handover-archive-2026.md`「外部ドメイン依存の詳細」と
`docs/notes/site-findings.md`（画像ドメインの実測）。

### 表の色とアクセシビリティ

`.mj-table` の文字は WCAG AAA（7:1）、並べ替えボタンのフォーカス枠は 1.4.11（3:1）。縞と見出しの背景は `#f2f2f2`（#345。AAA の上限は `#e4e4e4`、境界はリンク色 `#14459b`）。
新サイトでも引き継ぐ。値と理由は `style.css` のコメント、経緯は `docs/notes/site-findings.md`「リンクの配色」。

### gh-pages ブランチは触らない

移行前の GitHub Pages の内容を、移行前の実装の記録として凍結したまま残す（更新しない）。GitHub Pages は無効化済みで配信されていない（#84）。

---

## 5. 次にやること

**次の会話の順番（2026-10-09）:** (1) 待ち: #388（平野さんが「映画」のタブを作ってから）・#389（平野さんの校正した書籍の一覧の整備）。
(2) 期日待ちはカレンダーの順。決定は `docs/decisions/features.md`・`operations.md`。

**期日のあるタスクはカレンダー「ryoei.pro」が正（件名【R#番号】…、作るのはチャット側）。handover に日付は書かない。**

GitHub Issues（Open）に全件あるが、着手可能な主なものは以下。

| # | 内容 | 備考 |
|---|---|---|
| #486 | Bing の Recommendations | 残りは #486 の本文。title の長さ（短いページの共通の末尾）は #5 |
| #550 | アクセス解析の導入の検討（利用者の画面幅・Cloudflare Pro の要否） | `docs/notes/design.md` の最小の画面幅（360px、2026-10-18）を見直す材料。外部ドメインへの依存（4章）に注意 |
| #4 | SentryでJSエラー検知 | 外部サービスの登録が必要 |
| #96 | カレンダーの参照・更新を自動化 | スコープ未定。決めるべき項目が4つある |
| #390 | 道場部ゲストの書き込み済みの月の自動更新（本番で未稼働。サービスアカウントの削除の権限も未確認） | 随時: 差し替えで #426 に「自動で更新しました」か「自動では更新していません」が出たら、カレンダーの当日以降が画像と合っているかを確かめる |
| #186 | アクセシビリティの実機での通し確認 | 静的レビュー（#178〜#185）の残り。チェックリストは `docs/notes/a11y-manual-check.md`。**Lighthouse のスコアを到達点として扱わない**（根拠は #186） |
| #408 | タイトル戦の対局日を確定させる | `YYYY-XX-XX` の大半は決勝ライブが無く【2】【3】から直せない（外の資料が要る）。書式は `docs/notes/title-pages.md`「日付列の書式」 |
| #490 | /live 層2の残りの規則と掲載範囲 | 残りは U1〜U4（ライブの無い組の対局日・紅龍戦のステージの並び・件数の少ない列・「プレイヤー解説：」）・候補の判別・公開版の掲載・複数卓・達人戦／昇龍戦／鳳匠戦の扱い（済んだものと件数は #490 の本文） |
| #475 | /live の未登録の名前 | 常設（増減の日だけコメント。`docs/notes/live-channel-write.md`「7. 毎日の取り込み」）。実在の人は「連盟プロ以外」、誤記は「別名」に `訂正` で平野さんが登録し、読み違いは層2の規則で直す（#490） |
| — | 現行サイトで小さく作れるもの | 次は #388・#389（上の「待ち」）。#378 は新サイト（#296）送り。未決: #365・#367 のデータを誰がいつ入力するか。決定は `docs/decisions/features.md` |

---

## 6. 関連文書

完了済み作業の実装記録・調査結果は `docs/notes/` にある（handover には結論と参照先だけ）。`docs/notes/` 以下は下の一覧が正（`ls docs/notes/` にあって載っていないものは、開く場面を1行で足す）。

| ファイル | 開く場面 |
|---|---|
| `CLAUDE.md` | 作業のルール（ブランチ運用・Chat-Ref・作業ログ）。Claude Code がセッション開始時に読む |
| `docs/instruction-template.md` | チャット側が指示文を書くときの雛形 |
| `docs/new-page-checklist.md` | 新しいページを作る・公開する前（未公開で入れ、公開は別 issue、#243） |
| `docs/decisions/` | 平野さんの決定の記録（分野ごと。書き方は `README.md`） |
| `docs/new-site-design.md` | 新サイト（#296）の設計。中断中で再開手順まである |
| `docs/astro-migration-study.md` | 新サイトのスタック（#21）の判断 |
| `docs/lighthouse-baseline.md` | パフォーマンスの改善前後の比較（ページ別スコアの基準値） |
| `docs/gsc/` | Search Console の数値（#142） |
| `docs/notes/cloudflare.md` | Cloudflare の設定・配信（`_headers`・`_redirects`・`wrangler dev`）・本番反映と確認範囲（チャット側の check-run の確かめ方を含む）・設定の照合（Chrome） |
| `docs/notes/redirects.md` | 転送（リダイレクト）の一覧と四半期の棚卸しの手順・流入の測り方（#568）。旧 URL の 301 やダッシュボードのルールを足す・消すとき |
| `docs/notes/session-network.md` | シートの行番号の取り方、`ryoei.pro` の User-Agent。外部への到達・gh の認証・Rebuild・Codespace の確認用 URL・作業ファイルの置き場所の節は旧 Codespace の記録 |
| `docs/notes/cloud-sessions.md` | クラウドセッション（Claude Code の実行環境）の手順（起動時の CLAUDE.md の確かめ・作業ファイルの置き場所・ブランチの用意・GitHub MCP・ネットワーク・プレビュー） |
| `docs/notes/chat-side-operations.md` | チャット側が指示文を書く前（ログの読み方・場面ごとに読む文書の表もここ） |
| `docs/notes/glossary.md` | 用語集（チャット側・Claude Code・docs で使う語。ワークフロー・定期実行・本実行・スプレッドシートなど） |
| `docs/notes/chat-routines.md` | チャット側が「レビュー」「振り返り」「申送り」を行う前。指示の種類ごとの確かめ・期日とカレンダー・外部サービスの設定を頼むとき・ほかのセッションへの共有 |
| `docs/notes/chrome-reading.md` | チャット側が PC で Claude for Chrome を使い mj を直接読む前 |
| `docs/notes/skills.md` | skill の追加・更新、git の hook の判定を変える・確かめる前（`.claude/` を変える指示を書く前も） |
| `docs/notes/branch-operations.md` | ブランチの削除・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md）。「（Codespace）」の節と `/workspaces/mj` の pull は旧 Codespace の記録 |
| `docs/notes/static-generation.md` | ページの一覧・生成スクリプト・ページ側のJS・メンテナンス用スクリプト |
| `docs/notes/github-actions.md` | GitHub Actions のワークフローの一覧・`regenerate-page.yml`・手動実行と変更の前・チャット側の扱い（実行結果の確かめ方・失敗通知） |
| `docs/notes/sitemap-lastmod.md` | sitemap の lastmod（#265） |
| `docs/notes/scheduler-worker.md` | 予約実行を起動する Worker（`mj-scheduler`、#504）。起動の表の直し方・通知（#506）・トークンの期限と差し替え・ダッシュボードの設定と作り直す手順・未確認の点 |
| `docs/notes/dojo-guest-calendar.md` | 道場部ゲストの告知画像の取り込みと平野さん側の設定（#390） |
| `docs/notes/ogp.md` | OGP 画像・`og:title`・SNS のカード表示 |
| `docs/notes/page-announcement.md` | 新しいページの X での告知の型（投稿文・告知動画・進め方） |
| `docs/notes/video-wayhome.md` | 「帰り道」の一覧・個別ページ、新しい回の追加 |
| `docs/notes/video-mtsuku.md` | 「Mつく」の一覧 `/mtsuku/`・各話（帰り道と同じ形、2026-10-15 に公開、#570） |
| `docs/notes/video-study.md` | 「女流勉強会」の一覧 `/study/`・各話（帰り道・Mつくと同じ形、未公開、#575・公開は #576） |
| `docs/notes/video-exam.md` | 「プロテスト」の一覧 `/exam/`・各話（女流勉強会と同じ形、2026-10-19 に公開、#613） |
| `docs/notes/site-findings.md` | 現行サイトの実測値（パフォーマンス・画像ドメイン・SEO・配色） |
| `docs/notes/design.md` | 見た目の既決の値（色・文字・寸法・部品・濃色の例外）。ページや部品を作る・直す前。チャット側の決め方（比較ページ・見本・コントラスト比） |
| `docs/notes/a11y-manual-check.md` | アクセシビリティの実機確認（#186） |
| `docs/notes/mj-lead.md` | 表・グラフのあるページの説明文（`.mj-lead`、#312） |
| `docs/notes/saikyo-page-design.md` | 最強戦（`saikyo/`）と選手写真の更新 |
| `docs/notes/abema-book.md` | ABEMA ブック（最強戦の卓ごとの ABEMA の回、#425）。サムネイル・「▶ 再生」の元データを直す・確かめる前 |
| `docs/notes/title-pages.md` | タイトル戦の新構成（`title/`、#222） |
| `docs/notes/houou-race.md` | 順位変動（`houou/race/`・`ouka/race/`・`jwrc/race/` の土台、#507） |
| `docs/notes/houou-top.md` | 鳳凰戦の新ページ `houou/` の仕様（トップ＋検索・ランキング・リーグ推移・順位変動、#518） |
| `docs/notes/ouka-top.md` | 女流桜花の新ページ `ouka/` の houou/ との違い（#519。公開は #591） |
| `docs/notes/jwrc-top.md` | WRC リーグの新ページ `jwrc/` の houou/・ouka/ との違い（#594。公開は #599） |
| `docs/notes/wrcr-top.md` | WRC-R リーグの新ページ `wrcr/` の jwrc/ との違い（#366。公開は #611） |
| `docs/notes/league-pages-compare.md` | houou/・ouka/・jwrc/・wrcr/ の大会の差（意図した差と、公開でそろえる差）。大会の設定や共通の部品を変える前 |
| `docs/notes/ask-worker.md` | データ・コンシェルジュの Worker（`mj-ask`、#542） |
| `docs/notes/live-page-design.md` | 放送対局ページ（`live/`、#346）。掲載範囲の拡大は「3-5」（残りは #490） |
| `docs/notes/birthday-calendar.md` | 誕生日カレンダーの同期（#379） |
| `docs/notes/yotei-sheet.md` | 連盟の予定表の取り込みと放送対局の公開カレンダーへの同期（#448） |
| `docs/notes/live-channel-write.md` | /live の3層（【1】【2】【3】）の書き込み・毎日の取り込み・平野さんの入力の手順（#438）。チャット側が /live の指示を書く前 |
| `docs/notes/sns-book.md` | SNS ブック（選手の SNS の ID・画像の URL、#514）。ID の直し・写真の検知 |
| `docs/notes/books-freeze.md` | **書籍ページの開発凍結（2026-09-22、#97）。決定・凍結時点の状態・楽天データの期限・再開手順** |
| `docs/notes/books-calendar.md` | 書籍の発売日のカレンダー同期の仕組み（#97、凍結時点の記録） |
| `docs/notes/books-covers.md` | 書籍の書影（楽天ブックス書籍検索API）の仕組みと規約（#97、凍結時点の記録） |
| `docs/notes/decisions-2026-09-13-review.md` | #219〜#289 の重複・統合の判断 |
| `docs/notes/handover-archive-2026.md` | 過去の事故・経緯（作業の前提にはしない） |
| `docs/notes/security.md` | セキュリティ点検の観点・道具・手順（結果は #598〈closed〉、残りは #619） |
| `docs/notes/sheets-reader.md` | スプレッドシートを非公開にするための読み取り用サービスアカウントの用意（#557） |
~~~~

#### docs/notes/chat-side-operations.md

~~~~markdown
# チャット側（claude.ai）の運用

チャット側がログ・ガイド文書を読む手順と、Claude Code へ渡す指示文を書くときの注意。受け手側（Claude Code）の規則は CLAUDE.md が正。
**上限あり**（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。ここには毎回の指示に効く規則だけを置き、場面限定の手順は下の「場面ごとに読む文書」の表の文書へ。
足すときは既存項目への統合・置き換えで、規則と理由の一句だけを書く。**純増したら、足した分と同じ量を docs/notes/ の各文書へ移す**（`check-assets.yml` が警告する、#492）。事例は `docs/notes/handover-archive-2026.md`「docs/notes/chat-side-operations.md から」へ。

## 読み方

**主な読み方は mj-logs（public、スマホ・PC 共通）。** mj は private（#211）。チャットのセッションに読み取りでつなげば `git clone` と REST（`gh api`、GraphQL は不可）で読める（セッションごと。書き込みは Code）。
会話の始めに、プロジェクトのメモリーの preferences も読む（文書に無い個人の指定はそこにある）。

| 確認したいこと | 手段 |
| --- | --- |
| 作業ログ（`docs/logs/`）・ガイド文書 | mj-logs（下の「作業ログの読み方」） |
| Actions の実行結果 | `docs/notes/github-actions.md`「チャット側」 |
| Workers Builds の成否 | `docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」の「チャット側が成否を確かめるとき」 |
| issue・コミット・ブランチ | つないだチャットは REST、ほかは Claude Code に `gh`（クラウドは GitHub MCP）/ `git` で確かめさせ、ログに書かせる。PC では Chrome でも読める。コミット履歴を独立に検証するときは `docs/notes/handover-archive-2026.md`「コミット履歴の bundle による検証」（#211） |
| 本番の見え方 | 平野さんの目視か、Claude Code の headless Chrome。Code 側で確認できる範囲は `docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」。**チャット側が本番（ryoei.pro）のページ・ファイルを読むときは URL に `?v=<未使用の値>` を付ける（変更の直後の確かめでは必ず）**（クエリ無しでは古い版が返ることがある）。チャット側から本番を読む手段は WebFetch と Chrome（`curl` は通らない）。**「届かない・読めない」と書く前に両方を試す** |

### 作業ログの読み方

- **サンドボックスに `git clone --depth 1 https://github.com/retroeater/mj-logs.git` で取得して読める（読む前に毎回 `git pull`）。**
  最新のガイドは `guide/HISTORY` の最後の行の SHA の `guide/<SHA>/`、使用済みの識別子の一覧は `chat-ids/HISTORY` の最後の行の版。取得できるかはチャットごとに違う
- **平野さんが送るのは指示の完了報告（最終報告の最後の2行）だけ**（どのログの報告かの指定）。単発の確認でも作業ログに書かせる。
  **新しい会話の始めには送らない**: 取得できたチャットは `logs/`・`chat-ids/`・`guide/HISTORY` の最新を自分で読む（話題の前のログは Chat-Ref 系列〈メモリー等の控え〉か `logs/` の新しい順で探す）
- **取得できないチャットでだけ、読める最新の「ログ（公開）」の行を頼み、その URL をそのまま使う**（組み立てた URL は開けず、一度読んだ URL は古い版が返る。末尾の `?v=<SHA>` を変えない）。
  ガイド文書はそのログの末尾のリンク（`guide/<SHA>/`）から読む。読めない・途中の版に見えるときは `## 報告` 節だけを貼ってもらう。
  **末尾のリンク（`guide/<SHA>/`・`chat-ids/`）が読めないときは、指示文を書く前にその URL を送ってもらう**（読まずに書くと雛形や handover と食い違う）
- **ログは push のたびに数分以内に写る**（`docs/notes/cloud-sessions.md`「作業ログ」、#298）。
  **最終報告が出ているはずなのに `## 報告` が仕上がっていない・404 なら、再開の指示を作る前に、Claude Code の画面の最後に最終報告が出ているかを平野さんに確かめてもらう**（#454）。
  出ていれば写しの漏れ（`## 報告` を貼ってもらう）、出ていなければセッションが止まっている
- **申告をそのまま信じない。** ログ・ブランチ・issue に実行の痕跡があるかを先に確かめる
- **ガイド文書は、ログを読んだその場で必要なもの（マージを伴う指示なら CLAUDE.md「ブランチ運用」）を読む**（後の手番では同じ URL を開けなくなることがある）。
  **ガイドの版（`guide/<SHA>`）が前に読んだ版と違えば、指示文を作る前に `docs/instruction-template.md` を読み直す**
- **ブランチ名は最終報告の「ブランチ:」の行か比較 URL から取り、推測で組み立てない（指示文でも）。** mj-logs にログが無いときは、
  Claude Code に `git log --all --grep=<Chat-Ref> --oneline` で到達を確かめさせ、ブランチ名とログの URL を返させる
- 貼られた `## 報告` には Chat-Ref の行が無い。「ログ:」の URL の Chat-Ref とブランチ名で見分け、見分けられなければ読む前に平野さんに確かめる
- **ログは `## 報告` だけで足りる。** 全体を貼ってもらうのは「報告だけでは判断できない」と明示して頼んだときに限る
- ログの「指示」欄の末尾が、送った指示文の末尾と一致しているかを確かめる

### 確認用 URL

**プレビュー URL はログに無く、最終報告の「確認用:」の行にだけある**（非公開扱い）。チャット側はブランチ名の別名 URL（`docs/notes/cloudflare.md`「work/ ブランチのプレビュー」、#363）を自分で開く。別名は古い版を返すことがあるので `?v=<作業ブランチの HEAD の SHA>` を付け、ログの「確認用URL」の印が出ていることを確かめる。
**Code が撮った画像は URL と別で、作業ブランチの `docs/logs/<Chat-Ref>/` に置いてよい**（mj-logs には写らないので REST で読む）。

## セッション識別子の確認

- 識別子の形は CLAUDE.md「Chat-Ref」
- **最初の識別子は、読んだログの末尾の「使用済みの Chat-Ref 識別子」の一覧に無いものを選ぶ**（`MMDD` を問わない。#440 より前も載る）。
  一覧は写した時点のもので、以後に始まったセッションは載らないことがある。**本命は受け手側の確認**（CLAUDE.md「Chat-Ref」節）

## Claude Code とのやり取り

- **返答の最後に、「未完了の指示」（貼った・実行中・判断待ちで、完了報告をまだ受けていない Chat-Ref の全件）と、平野さんの手作業の残りを一覧で出す。未完了が1件以上あるときだけ書き、0件なら書かない**

## 指示文を書くときの注意

雛形は `docs/instruction-template.md`。
**Claude が作成した下書き（Claude Code に貼る文面など）には、先頭行を「【Claude作成】…」にするなど Claude 作成と分かる見出しを付ける**（平野さんの発言と区別するため）。

### 場面ごとに読む文書

指示文を書く前に、当たる場面の文書を読む（場面限定の手順はここに書かず、表の文書に置く）。

| 場面 | 読む文書 |
| --- | --- |
| PC で Claude for Chrome を使う | `docs/notes/chrome-reading.md`（先に読む） |
| 平野さんが「レビュー」「振り返り」「申送り」と言った | `docs/notes/chat-routines.md` の同名の節 |
| 最初の指示・マージ・クローズ・複数の指示にまたがる作業・起票や方針や文書整理・インフラ・他のセッションが変えている領域・一覧の行の形を決める・シートを用意した・移した・消したと言われた・シートの行や予定を指す・計測・試運転・本番へ書き込むワークフローの試し・データ更新後の生成 | `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」 |
| マージ・ブランチ操作を含む指示 | CLAUDE.md「ブランチ運用」（**必ず読む**、#313） |
| ワークフローを変える指示、Actions の実行結果・失敗通知 | `docs/notes/github-actions.md`「チャット側」 |
| /live の【3】・`update-live-channel.yml` に触れる | `docs/notes/live-channel-write.md`「チャット側の指示」 |
| `.claude/` の hook・settings を変える | `docs/notes/skills.md`「git の hook（mj-git-guard）」 |
| 見た目を決める（比較ページ・見本・コントラスト比） | `docs/notes/design.md`「7. チャット側の決め方」 |
| 期日・カレンダー・確認の予約 | `docs/notes/chat-routines.md`「期日とカレンダー」 |
| 外部サービスの設定を頼む・外部の API を使う実装 | `docs/notes/chat-routines.md`「外部サービスの設定を頼むとき」 |
| ほかのチャットへの共有、共有文を受け取った | `docs/notes/chat-routines.md`「ほかのセッションへの共有」 |
| Code に環境に依存する操作（`gh`・ネットワーク・ブランチの削除など）をさせる | `docs/notes/cloud-sessions.md` |
| Claude Code が分類器に拒否されて止まった | `docs/notes/cloud-sessions.md`「作業ブランチの用意」 |

### 書く前に実物で確かめる

- **事実は実物で確かめたものだけを断定する**（会話の記憶・前回の報告・推測から書かない）。「目的」「前提」の事実は、そのチャットで実物を読んだもの以外に「（要確認）」を付けて Claude Code に確かめさせ、
  怪しいものは「〜を確かめ、食い違えば止まる」の形にする。issue 番号・ページ数は洗い出しのログにあるものだけ使う（#486）。確かめた場所を正しく書き、見ていない出典を書かない。解釈を事実として書かせない。
  **issue の状態（Open/Closed・本文・コメント）を確かめられないまま、その issue の作業の指示を書かない**（読めなければ Code に確かめさせてログに書かせてから書く）
- **触れる領域の資料を読んでから書く**（CLAUDE.md・handover.md に追記・変更するときは追記先の節の今の内容、ある領域に触れるときはその領域の `docs/notes/` の最近の変更。場面ごとの文書は上の表）
- **仮説を確かめるときは、当たり外れを一発で分ける確認を先に行い、仮説が正しい前提での準備はその後にする**
- **平野さんの手作業（画面を見るだけ等）が安価で結論を左右するなら、記録・文書の指示より先に頼む**
- **平野さんに判断を求める前提（「A を直せば B も不要になる」等）や、シートの値を変えるよう頼む前の今の値は、先に Claude Code に照合させるか「未確認」と明記する**（誤った前提で判断をもらうと決定が誤る）

### 平野さんの判断とマージの許可

**平野さんは指示文を読まずに貼る**（添え書きの確認は読み落とされ、未決のことが判断として記録される）。次はこの前提による。

- **「平野さんの決定」の欄には、実際に判断をもらった内容だけを書く。** 現状維持も明示が無ければ「前提」の欄へ（欄は `docs/instruction-template.md`）。
  **チャット本文にも同じ内容を書き、日付は判断した日にする**（Chat-Ref の日付ではない）
- **平野さんの決定は、ログの末尾のリンク「docs/decisions/README.md」から分野のファイルを読んで確かめ、**指示文の「決定」節はそれと食い違わないように書く（置き換えるときはその旨を書く）
- **平野さんに選択肢を出す前に docs/decisions/ の分野のファイルを読み、選択肢の説明に平野さんが言っていない仕様を足さない。** 足すなら「チャット側の案」と分けて書く（選択肢に混ぜると、選んだだけで決定として記録される）
- **指示文を作る前にマージの可否を平野さんに確かめ、「マージ:」の行に「承認済み（チャットで）」か「判断待ちで止まる」を書く**（Code は処理中に承認を求めない）。
  **マージの可否は、ほかの問いと分けて単独で聞く。別の問いの答え（「進む」など）から承認を推さない**（推すと、承認されていない変更までマージされる）。
  プレビューを見て決める変更は「判断待ちで止まる」。「承認済み」はプレビューの確認が済んだことをチャットで確かめてから書く（「貼った＝見た」とみなさない）。承認済みでも、マージを止める確認の基準は「止まる条件」に書く。
  **調査だけの指示（変わるのがログだけ）は「ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい」にする**（判断待ちで止めると、マージだけの指示が後で要る）。判断の要る点は「判断が必要なこと」に書かせ、続きは作業ブランチを origin/cloudflare から作り直して始める。**どの指示でも、完了条件にログの状態（完了・判断待ち）を書かない**（状態は CLAUDE.md「作業ログ」節に従い Code が決める。書くと指定とルールの食い違いを毎回判定させる）
- **「判断待ち」で止めた報告を確認した返答に、マージ用の指示文を添える**（口頭の「マージしてよい」を前提にしない）。同じ作業ブランチ・マージで動く自動処理の見込み・止めた指示のログの状態の直し・作業ブランチの片付けを含める。
  「承認済み（チャットで。この指示文を貼ることが承認）」としてよいのは、(1) 止めた報告のマージだけで新しい変更を含まない (2) 何がどう変わるか（件数だけでなく中身、コードなら影響の範囲）をチャットに示した (3) origin/cloudflare を取り込んだ後の再検証（テスト・模擬など）を条件に書いた、のすべてを満たすときだけ（報告を読んだうえの承認で、「貼った＝見た」とは別）。満たさなければチャットで「よい」をもらってから書く。
  **片付けは、関係のない検証（ワークフローの実行結果など）の成否に条件づけない。**
  **「マージしてよい」にスクリーンショットが添付されているときは、マージの指示文を書く前に全部見て、見つけた崩れは指示文の「決定」に書く**
- **告知の期間（告知動画の初版の指示に着手してから投稿完了まで。page-announcement.md「進め方」）に、告知に挙げるページ・機能を変える指示では、「マージ:」の行に「告知の期間中の変更を承認（<日付>、平野さん）」と書く。** 書いていなければ Code はマージせずに止まる
- **`.claude/` の hook・settings を変える指示は平野さんの手作業を前提に組み、「マージ:」は「判断待ちで止まる」**（Code は試験と文書まで。手順は上の表）
- **クローズの時点で残る作業は別の issue に起票する**（平野さんの決定。クローズの指示に書く）
- **「添付した」と書く前に、ファイルを提示し終えたことを確かめる**
- **公開など取り消しにくい段階の指示は、作る前に実施の時期を平野さんに確かめる**
- **報告の「〜は読み取れない」「〜は不明」を平野さんに伝える前に、handover と突き合わせる**

### 指示文の書き方・渡し方

- **語は docs/notes/glossary.md に従う**（新しく書く文から）
- **作業ブランチ名は書く**（文面は `docs/instruction-template.md`「作業ブランチ」の行。既存ブランチを続けるときも）。未マージのブランチとの重なりは名指しせず、
  `git branch -r --no-merged origin/cloudflare` の一覧から確かめさせる（書いた後に増えたブランチを見落とさない）
- **コミットSHAを固定して書かない。** 土台や比較対象は「その時点の`origin/cloudflare`」と書く。既存の特定コミットの参照は書いてよい
- 未マージの試作ブランチを捨てさせるときは、CLAUDE.md「未マージのブランチは削除しない」の例外であることを明記する
- **貼った指示文が途中で切れて届くことがある。** 0章に途中切れの確認の一文を入れ、最後に確認文を置く（文面は `docs/instruction-template.md`）
- **指示文はログの「指示」欄にそのまま写り、public の mj-logs に出る。** CLAUDE.md「作業ログ」節がログに書かないとするもの（非公開の URL、シートの ID・URL など）は指示文にも書かず、ブックは `scripts/` の定数名などで呼ぶ
- **設定の読み込みやセッションの状態に左右される検証は、チャット本文で「新しいセッションで貼る」と伝え、0章で「このセッションが新しく始めたものか」をログに書かせる**
- **チャットの添付ファイル（CSV 等）は Code から読めないことがある。** チャット側で集計し、表にして指示文に入れる
- **他のチャットへの伝言（返信を含む）は .md の添付で渡す。** チャット本文は要点2〜3行と平野さんに頼む判断・手作業だけ（本文に書くと画面が流れる）
- **1本の指示は3項目程度まで**（途中で止まったときに何が済んだか分かるように）
- **配った後の指示を直すときは、先に平野さんがもう貼ったかを確かめる。** 貼った後なら新しい番号で出し直す
- **並行する指示の中で、ほかの実行中の指示の進み具合に触れない。** 結果が要るなら「`<Chat-Ref>` のログの `## 報告` を読み、完了していなければ止まる」の形にする
- **後の実行の結果（毎朝の実行・別の指示）を見込みに使う指示は、その結果が出てから作る。** 前もって作るときは「貼る時機」に前提を書き、
  見込みは「`<Chat-Ref>` のログの `## 報告` に従う」の形にして数を写さない
- **同じチャットから、前の指示の完了を待たずに次の指示を出すときは、作業ブランチを分ける**（`work/<MMDD>-<識別子>-<短い名前>` など）。
  平野さんには「1つのセッションに貼る指示は1つ」と伝える
- **既定モデルは Opus 5.5。別モデルは指示文の手前のチャット本文で伝える**（指示文に「推奨モデル」欄は入れない）。定型作業に Sonnet 5.5 を使うかは後日平野さんと相談する

### 止まる条件と検証の指定

- **「差分が N 行ならマージ」のような止める条件は、先に変更対象の参照箇所を調べさせてから決める**（容量の増減も実測を報告させる）
- **マージ・止まる条件は、見込みで説明できる差分の範囲として書く。** 生成物を含む変更は差分を種類に分け、許す種類と確かめ方（変わった画像 URL は HTTP 200 など）を書き、ほかが出たら止める（再生成のたびにシートの変化が混ざる）。
  許す行は焼き付けず、比べる相手（前の TSV・ログ）を指して想定外の行を全部書かせる（決定が変わると無効になる）。
  「承認済み」の条件は「決定とシートの変化で説明できる差分だけ（見込み: ○○）」の形にする（表示の一部の列挙では、そこから決まる並びが外れる）。
  見込みと比べる条件は許すずれを数で書き（無いと進むかを受け手が決める）、見込みの数は直前のログのどの表のどの数から計算したかを書く（推測の幅は表と食い違う）
- **止まる条件で数値の増減を見るときは、別名の寄せなど正しい理由で動く場合を想定して書く。** 外の通信（HEAD・API）で動く数は、同じ実行をもう1回行い、それでも外れたら止まる、の形にする
- **スプレッドシートを読む指示には行数の止まる条件を入れる**（想定と違う・読み直すたびに変わるなら件数を書いて止まる。大量の貼り替えの後は貼り終えたことを伝えてから読ませる。`docs/notes/static-generation.md`「生成を止める条件の設計」）
- **「通知が出ること」を確認に書く前に、その通知が出る条件を確かめる**
- **ログの計測値や事実を issue に書かせるときは、要約を渡さず「どのログのどの節から引用するか」を指定する。** 逆に issue の本文などをログに引用させるときは、置き場所を `## 経過` と指定する（最終報告は状態の行だけで、そこにだけ書かせるとログに残らない）
~~~~

#### docs/instruction-template.md

~~~~markdown
# 指示文テンプレート（チャット側 → Claude Code）

チャット側（claude.ai）が Claude Code へ渡す指示文の骨組み（#294）。受け手側のルールは CLAUDE.md が正で、ここには複製しない。
作業ブランチ名を書くこと・SHA を固定しないこと・「決定」と「前提」の欄の分け方・0章の途中切れの確認・見込みと比べる止まる条件の規則は `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」「平野さんの判断とマージの許可」「止まる条件と検証の指定」が正。ここには行の文面と書き方だけを置く。

- 「作業ブランチ」の行の文面（式の向きが2つで逆なので、そのまま写す）:
  - クラウドセッションで新しく作る: 「作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる」
  - クラウドセッションで未マージの作業を続ける: 「作業ブランチ: クラウドセッションで実行する。未マージの work/<識別子> を続けて使う（〜のため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）」。`git checkout -b work/<識別子> origin/work/<識別子>` は書かない（同じセッションを続けるとローカルに同名のブランチがあり `-b` が失敗する）
- 未マージの作業ブランチを続ける指示とマージの指示には、取り込みで生成物でない文書が衝突したときの扱いを書く。別のセッションが同じ文書を変えていそうなとき（一覧の表・追記の続く文書）は「両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる」と書く。「また衝突したら止まる」とは書かない（同じ形の衝突のたびに1往復かかる）
- 定型部分は「共通手順」1行にまとめ、個々の手順を文章で書き直さない（写し間違いと途中切れを減らす）
- **共通手順の行の手前に「貼る時機:」の行を置く。** 前提（ほかの指示の完了・毎朝の実行の後など）があれば書いて0章で確かめさせ（満たされなければ何もせず止まる）、無ければ「いつでも」（前提の指示より先に貼られると番号を1つ使う）。別のセッション（並行）に貼らせるのは、同時に動かすときだけ書く（前の指示の完了後に貼るなら同じセッションでよい）
- 運用ルールの変更を含む指示は、手順1を「同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する」に固定する（チャット側が検索できないことがあるため）
- 既存の文書への追記を含む指示は、追記先の今の内容を必ず読ませる（読ませないと古い記述と新しい記述が両方残る）。止まる条件は次のとおり書き分ける
  - **止まるのは、追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要るとき**
  - **同じ趣旨の記述があるだけなら止めず、既存の記述を置き換え・拡張してよい**（処理を報告に書かせる）
- 計測・試運転・本番へ書き込むワークフローの試し・データ更新後の生成を含む指示の書き方は `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」
- 見た目を直す指示で、受け入れた値（ページの長さ・高さ・サイズなど）が既にあるときは、その値を止まる条件に写す（超えたときの扱いが無いと、マージ後に判断待ちが残る）
- issue を変えない読むだけの指示でも着手中のコメントは書かせ、「本文・状態・ラベルは変えない」と書き分ける（「issue を変えない」だけだと CLAUDE.md の着手中コメントと食い違う）
- 「変更は docs のみ」「docs/logs のみ」と範囲を書くときは、`docs/decisions/` も含めて書く（受け手は完了時に決定を足すため、範囲から外れると指示とルールが食い違う）
- 依存（blocked by）は「A は B を待つ」の文で書き、矢印（→）は使わない（向きが読み手によって逆になる）
- 報告の項目は数で書かず「CLAUDE.md『作業ログ』節のとおり」と指す（項目が増えたとき、引き写した指示文が古い数を運ぶ）
- **数を報告させるときは、名前の一覧も書かせる。後で照合の基準にする数は「略」にさせない（表を全件で書かせる）**（残りを後で探すことになる）
- 完了条件の Chat-Ref の行は、ターミナルへの最終報告の最後の行を指す（ログの `## 報告` には無く、「ログ:」の URL で見分ける、#419 (b)）
- 送る前に指示文を直したときは番号を進めて直す前の番号を欠番にし、次の指示文の目的欄に「〜は送る前に差し替えたため欠番」と書く（直す前の版が渡っても番号の違いで分かる）。差し替えた指示文を渡すチャットの本文に、欠番の番号と貼る順も書く。貼った後に直すときは `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」
- 待機の上限15分と、共有の定数・関数を変えるときの全ページ再生成は CLAUDE.md「判断・作業の原則」が受け手に課す。指示文には例外だけ書く
- 新しいページ・メニューを作る指示に公開（noindex を外す・navbar・サイトマップ・`llms.txt`）を含めない（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」、`docs/new-page-checklist.md`、#243）
- **未マージの `work/` ブランチとの重なりで止まる条件は、ファイル単位でなく「同じ行・同じ関数を変えている、または取り込みで衝突する」の形で書く**（行が離れた変更で止まると往復が増える、#277）
- **対象（ページ・ファイル）が複数ある指示では、重なりを「重なる対象だけ外して残りを進めてよい（外したものと理由を報告に書く）。全部が重なるときだけ止まる」の形で書く**（1つの重なりで全体が止まると往復が増える）
- **マージ後の check-run の失敗で止める条件は、「今回の変更による失敗」と「無関係な失敗」を分けて書く。** 無関係なら原因を報告に書いたうえで残りの手順（本番の確かめ・issue のクローズ）を進めてよい、とする。自分の変更で落ちると分かっているテストは直してよい、と書く（#277）
- **続きの指示（前の指示の判断待ち・中断への回答、再開）には、前のログの `## 報告` の状態の末尾に ` / 続き: <この指示の Chat-Ref>` を足す手順を入れる。** 続き先が完了・取り下げになると前のログが自動で削除される（`docs/notes/branch-operations.md`「作業ログの寿命」）

```
【Claude作成】Claude Code 向け指示：（題。何をするかを1行で）
（題の行は必ず入れる。平野さんの発言と区別するため）
Chat-Ref: CHAT-MMDD-XXX-nn
マージ: 承認済み（チャットで）／判断待ちで止まる／ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉（どれかを残す。docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」）
貼る時機: いつでも／<前提>の後（例: CHAT-MMDD-XXX-nn の完了の後、毎朝の取り込み〈update-live-channel、04:00 JST の Worker からの起動〉の後）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
（0章のこの一文は必ず入れる。途中切れの確認のため）
（「貼る時機」の前提は0章で確かめる。条件はログの状態でなく結果〈マージ済み等〉で書く。プレビューで止まる指示は「判断待ち」で終わり「完了」にならない）

## 目的
（何のために何を変えるか。1〜2行）

### 決定（YYYY-MM-DD、平野さん）
- （チャットで平野さんが明示したことだけ。現状維持を含め、明示が無ければ下の「前提」へ）

### 前提（チャット側。平野さんの決定ではない）
- （チャット側の提案、書き場所・文面の案、背景。実物に合わせて変えてよいものはここに書く。実物で確かめていない事実には「（要確認）」を付ける。例: 鍵の要るスクリプトの確かめはワークフローの dry-run で〈cloud-sessions.md「鍵」〉）

## 手順
1. （前提は「〜を確かめ、食い違えば止まる」の形で書く）
   （運用ルールの変更を含む指示では、上の注意のとおり手順1を固定する）
2.
3. （3項目程度まで）

## 止まる条件
- （前提が食い違う、同じ論点の issue がある、他セッションの着手中コメントがある 等）
- （見込みと比べるときは許すずれを数で。例: 直すは見込み ±3件まで、作る・消すは見込みと同じ）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが〈変えるファイル〉の〈同じ行・関数・節〉を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- （マージを伴う指示では）cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-MMDD-XXX-nn.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-MMDD-XXX-nn を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。
```
~~~~

### 差分（70c0cc5f3..6de55b38a）

#### CLAUDE.md

~~~~diff
diff --git a/CLAUDE.md b/CLAUDE.md
index b4ad30c18..b8118e8fe 100644
--- a/CLAUDE.md
+++ b/CLAUDE.md
@@ -9,7 +9,7 @@
 ### 引き継ぎ
 
 経緯・現状・次にやることは docs/handover.md（文脈が要るときはまず読む）。
-**起動時に読み込んだこのファイルは古いことがある**（確かめ方: docs/notes/branch-operations.md「起動時に読み込んだ CLAUDE.md が古くないか確かめる（Codespace）」）。
+**起動時に読み込んだこのファイルは古いことがある**（確かめ方: docs/notes/cloud-sessions.md「起動時に読み込んだ CLAUDE.md が古くないか確かめる」）。
 
 ### 構成
 - 静的HTML。ビルド工程なし。ページの一覧はdocs/notes/static-generation.md「ページの一覧」（件数の正は`python3 scripts/regenerate.py --list`）
@@ -61,14 +61,13 @@
 
 ## ブランチ運用
 
-複数セッションが並行編集するため、セッションごとに作業ブランチと作業ディレクトリを分ける（#198）。場面ごとの手順はdocs/notes/branch-operations.md。
+複数セッションが並行編集するため、セッションごとに作業ブランチを分ける（#198）。場面ごとの手順はdocs/notes/branch-operations.md。
 **チャット側の指示文がこの節と食い違う（`cloudflare`上での直接作業など古い前提を含む）ときは、指示文には従わずこの節に従うこと**（#205）。
 
 - **`cloudflare`: 統合・デプロイ専用。セッションはここへ直接pushしない**（マージ＝本番反映）
 - **`work/<識別子>`: セッションの作業ブランチ。** 識別子は Chat-Ref から取る（`CHAT-0913-QMX-02`なら`work/0913-qmx`）。指示文に指定が無くても切る。
   複数issueを1ブランチで扱ってよいが、作業に関係しない独立した変更（ルール追記・ドキュメントのみの修正等）は別ブランチに分ける
-- **Codespace: `/workspaces/mj`は全セッションが共有する。`origin/cloudflare`を明示して作った worktree の中で作業し、`/workspaces/mj`自身では`git checkout`/`git switch`を行わない**
-  （手順・片付けは同「作業ディレクトリの分離（Codespace）」）。**クラウドセッション（`/workspaces/mj`・worktree・`gh`が無い）の読み替えはdocs/notes/cloud-sessions.md**
+- **Code はクラウドセッションで実行する。ブランチの用意・`gh` の代わり（GitHub MCP）などの手順はdocs/notes/cloud-sessions.md**
 - **成果物の`cloudflare`へのマージは、指示文に「マージ: 承認済み（チャットで）」があるときだけ行う。** 無ければ完了を報告し、判断待ちで止まる。
   承認は処理中に求めない。ドキュメントのみの変更は完了報告のうえマージしてよい。承認済みでも、確認が1つでも通らない・止まる条件に当たった・
   前提が崩れたときはマージせずに報告する（docs/notes/branch-operations.md「マージするとき」）
@@ -122,7 +121,7 @@
   各項目（予約実行の初回の日時を含む）・状態の書き方（「完了」の条件・` / 続き: CHAT-…`）は`_template.md`とdocs/notes/branch-operations.md「作業ログの寿命」
 - **ログの節を書き換えるときは、`## 指示`欄より後ろの見出しを相手にし（`## 報告`はファイルの最後の一致）、push 前に`## 指示`欄が変わっていないことを確かめる**（理由は`_template.md`）
 - ログは作業ブランチにだけpushし、成果物と別のコミットにする。`cloudflare`へは指示の最後のマージ1回で成果物と一緒に入れる（マージの結果を書く docs/logs のみの追いのpushは可）
-- 後の指示で使うスクリプト・中間データの置き場所は docs/notes/session-network.md「作業ファイルの置き場所」（scratchpad は再起動で消える）
+- 後の指示で使うスクリプト・中間データの置き場所は docs/notes/cloud-sessions.md「作業ファイルの置き場所」（scratchpad とコンテナは消える）
 - **ターミナルへ返す最終報告は`_template.md`「ターミナルへ返す最終報告」の形**（最後の行は`Chat-Ref: CHAT-MMDD-XXX-nn`、直前は「ログ（公開）」の行。詳細はログの`## 報告`）。
   **質問して止まるとき・pushに失敗したときだけ、内容をターミナルに書く**（ログに届かないため）
 - **指示の完了時（完了・判断待ち・中断の最後の push）に、その指示の「決定」節と作業中の平野さんの回答（grill を含む）を
@@ -179,7 +178,7 @@
 - handover.md / CLAUDE.md をissueやコメントから参照するときは、行番号ではなく節・項目の見出しで書く
 - 「最終更新」は日付＋直近の変更3行以内。外した行は archive へ移さず消してよい
 - 上限はファイルごとに **CLAUDE.md・handover.md・docs/notes/chat-side-operations.md 各28KB（警告域26KB）・docs/instruction-template.md 16KB（警告域14KB）**
-  （`check-assets.yml`、1KB=1024バイト。**作業する worktree の上で測る**）。
+  （`check-assets.yml`、1KB=1024バイト。**作業ブランチの上で測る**）。
   警告域に近づいたら、足す前に `docs/notes/` へ移す。**警告が出たら、上限を上げずに4文書とも整理する**（同じ趣旨の記述をまとめ、
   事例は `docs/notes/handover-archive-2026.md` へ、場面限定の手順は `docs/notes/` へ。退避先には上限を置かない、#421）。
   上限は下げる方向にだけ動かし、上げるのは平野さんの判断。最終目標は CLAUDE.md 20KB・handover.md 24KB 前後（#292・#301 が進んだ時点で見直す、#492）
~~~~

#### docs/handover.md

~~~~diff
diff --git a/docs/handover.md b/docs/handover.md
index 723053432..fe7e74d7f 100644
--- a/docs/handover.md
+++ b/docs/handover.md
@@ -58,7 +58,7 @@ GitHub Pages からの移行の相談に始まり、Cloudflare への移行の
 `html_handling`・`_redirects` の先頭行の規則は CLAUDE.md「禁止事項」。canonical は付けない（#113）。例外は `wayhome/` の個別ページで、URL変種を持たないため canonical を持つ（#162）。
 `_headers` はセキュリティヘッダ（CSP を含む）とキャッシュ制御（#92）。設定値と経緯は `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」
 
-**本番反映**は CLAUDE.md「構成」（ゲートは #170）。設定値・確認範囲は `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」、セッションからの到達は `docs/notes/session-network.md`（#328）。
+**本番反映**は CLAUDE.md「構成」（ゲートは #170）。設定値・確認範囲は `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」、セッションからの到達は `docs/notes/cloud-sessions.md`「ネットワーク」（#328）。
 
 ### ページ構成
 
@@ -95,7 +95,7 @@ GitHub Pages からの移行の相談に始まり、Cloudflare への移行の
 | **Claudeとのチャット** | 設計の相談、調査、原因の切り分け、実装案・指示文の作成 |
 | **Claude Code** | ファイルの編集、issue操作、コミット・push。コミットの到達確認・差分・マージ判定も行い、結果をログで報告する |
 
-Claude Code の実行環境は Codespace（`/workspaces/mj` で `claude`、`gh` 認証済み。Rebuild・gh の認証は `docs/notes/session-network.md`）かクラウドセッション（`docs/notes/cloud-sessions.md`）。
+Claude Code の実行環境はクラウドセッション（`docs/notes/cloud-sessions.md`）。
 Chat-Ref・並行作業の規則は CLAUDE.md「Chat-Ref」「ブランチ運用」、決まるまでの経緯は archive。
 
 ### skill と hook
@@ -193,14 +193,14 @@ GitHub Issues（Open）に全件あるが、着手可能な主なものは以下
 | `docs/gsc/` | Search Console の数値（#142） |
 | `docs/notes/cloudflare.md` | Cloudflare の設定・配信（`_headers`・`_redirects`・`wrangler dev`）・本番反映と確認範囲（チャット側の check-run の確かめ方を含む）・設定の照合（Chrome） |
 | `docs/notes/redirects.md` | 転送（リダイレクト）の一覧と四半期の棚卸しの手順・流入の測り方（#568）。旧 URL の 301 やダッシュボードのルールを足す・消すとき |
-| `docs/notes/session-network.md` | セッションから外部に届くか、gh の認証、Rebuild、シートの行番号、作業ファイルの置き場所 |
-| `docs/notes/cloud-sessions.md` | クラウドセッション（Claude Code on the web）での CLAUDE.md の読み替え（ブランチの用意・GitHub MCP・ネットワーク・プレビュー） |
+| `docs/notes/session-network.md` | シートの行番号の取り方、`ryoei.pro` の User-Agent。外部への到達・gh の認証・Rebuild・Codespace の確認用 URL・作業ファイルの置き場所の節は旧 Codespace の記録 |
+| `docs/notes/cloud-sessions.md` | クラウドセッション（Claude Code の実行環境）の手順（起動時の CLAUDE.md の確かめ・作業ファイルの置き場所・ブランチの用意・GitHub MCP・ネットワーク・プレビュー） |
 | `docs/notes/chat-side-operations.md` | チャット側が指示文を書く前（ログの読み方・場面ごとに読む文書の表もここ） |
 | `docs/notes/glossary.md` | 用語集（チャット側・Claude Code・docs で使う語。ワークフロー・定期実行・本実行・スプレッドシートなど） |
 | `docs/notes/chat-routines.md` | チャット側が「レビュー」「振り返り」「申送り」を行う前。指示の種類ごとの確かめ・期日とカレンダー・外部サービスの設定を頼むとき・ほかのセッションへの共有 |
 | `docs/notes/chrome-reading.md` | チャット側が PC で Claude for Chrome を使い mj を直接読む前 |
 | `docs/notes/skills.md` | skill の追加・更新、git の hook の判定を変える・確かめる前（`.claude/` を変える指示を書く前も） |
-| `docs/notes/branch-operations.md` | ブランチの削除・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md） |
+| `docs/notes/branch-operations.md` | ブランチの削除・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md）。「（Codespace）」の節と `/workspaces/mj` の pull は旧 Codespace の記録 |
 | `docs/notes/static-generation.md` | ページの一覧・生成スクリプト・ページ側のJS・メンテナンス用スクリプト |
 | `docs/notes/github-actions.md` | GitHub Actions のワークフローの一覧・`regenerate-page.yml`・手動実行と変更の前・チャット側の扱い（実行結果の確かめ方・失敗通知） |
 | `docs/notes/sitemap-lastmod.md` | sitemap の lastmod（#265） |
~~~~

#### docs/notes/chat-side-operations.md

~~~~diff
diff --git a/docs/notes/chat-side-operations.md b/docs/notes/chat-side-operations.md
index fada819c4..88ba0d2df 100644
--- a/docs/notes/chat-side-operations.md
+++ b/docs/notes/chat-side-operations.md
@@ -42,7 +42,6 @@
 
 **プレビュー URL はログに無く、最終報告の「確認用:」の行にだけある**（非公開扱い）。チャット側はブランチ名の別名 URL（`docs/notes/cloudflare.md`「work/ ブランチのプレビュー」、#363）を自分で開く。別名は古い版を返すことがあるので `?v=<作業ブランチの HEAD の SHA>` を付け、ログの「確認用URL」の印が出ていることを確かめる。
 **Code が撮った画像は URL と別で、作業ブランチの `docs/logs/<Chat-Ref>/` に置いてよい**（mj-logs には写らないので REST で読む）。
-ビルドが走らなかったときは Codespace の確認用 URL（GitHub ログイン必須。`docs/notes/session-network.md`「Codespace の確認用 URL」）
 
 ## セッション識別子の確認
 
@@ -68,7 +67,7 @@
 | PC で Claude for Chrome を使う | `docs/notes/chrome-reading.md`（先に読む） |
 | 平野さんが「レビュー」「振り返り」「申送り」と言った | `docs/notes/chat-routines.md` の同名の節 |
 | 最初の指示・マージ・クローズ・複数の指示にまたがる作業・起票や方針や文書整理・インフラ・他のセッションが変えている領域・一覧の行の形を決める・シートを用意した・移した・消したと言われた・シートの行や予定を指す・計測・試運転・本番へ書き込むワークフローの試し・データ更新後の生成 | `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」 |
-| マージ・ブランチ操作・`/workspaces/mj` の操作を含む指示 | CLAUDE.md「ブランチ運用」（**必ず読む**、#313） |
+| マージ・ブランチ操作を含む指示 | CLAUDE.md「ブランチ運用」（**必ず読む**、#313） |
 | ワークフローを変える指示、Actions の実行結果・失敗通知 | `docs/notes/github-actions.md`「チャット側」 |
 | /live の【3】・`update-live-channel.yml` に触れる | `docs/notes/live-channel-write.md`「チャット側の指示」 |
 | `.claude/` の hook・settings を変える | `docs/notes/skills.md`「git の hook（mj-git-guard）」 |
@@ -76,7 +75,7 @@
 | 期日・カレンダー・確認の予約 | `docs/notes/chat-routines.md`「期日とカレンダー」 |
 | 外部サービスの設定を頼む・外部の API を使う実装 | `docs/notes/chat-routines.md`「外部サービスの設定を頼むとき」 |
 | ほかのチャットへの共有、共有文を受け取った | `docs/notes/chat-routines.md`「ほかのセッションへの共有」 |
-| Code に環境に依存する操作（`gh`・ネットワーク・ブランチの削除など）をさせる | `docs/notes/cloud-sessions.md`（クラウド）・`docs/notes/session-network.md`（Codespace） |
+| Code に環境に依存する操作（`gh`・ネットワーク・ブランチの削除など）をさせる | `docs/notes/cloud-sessions.md` |
 | Claude Code が分類器に拒否されて止まった | `docs/notes/cloud-sessions.md`「作業ブランチの用意」 |
 
 ### 書く前に実物で確かめる
~~~~

#### docs/instruction-template.md

~~~~diff
diff --git a/docs/instruction-template.md b/docs/instruction-template.md
index 81211f8d5..8d638ce13 100644
--- a/docs/instruction-template.md
+++ b/docs/instruction-template.md
@@ -4,7 +4,6 @@
 作業ブランチ名を書くこと・SHA を固定しないこと・「決定」と「前提」の欄の分け方・0章の途中切れの確認・見込みと比べる止まる条件の規則は `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」「平野さんの判断とマージの許可」「止まる条件と検証の指定」が正。ここには行の文面と書き方だけを置く。
 
 - 「作業ブランチ」の行の文面（式の向きが2つで逆なので、そのまま写す）:
-  - Codespace で既存の作業ブランチを続ける: 「作業ブランチ: origin/cloudflare を起点に切った既存の work/<識別子> を続けて使う（〜のため）。着手時と作業中に origin/cloudflare が進んでいたら merge で取り込んでよい（push 済みなので rebase しない）」。取り込みの可否を書かないと、祖先確認だけで中断する
   - クラウドセッションで新しく作る: 「作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる」
   - クラウドセッションで未マージの作業を続ける: 「作業ブランチ: クラウドセッションで実行する。未マージの work/<識別子> を続けて使う（〜のため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）」。`git checkout -b work/<識別子> origin/work/<識別子>` は書かない（同じセッションを続けるとローカルに同名のブランチがあり `-b` が失敗する）
 - 未マージの作業ブランチを続ける指示とマージの指示には、取り込みで生成物でない文書が衝突したときの扱いを書く。別のセッションが同じ文書を変えていそうなとき（一覧の表・追記の続く文書）は「両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる」と書く。「また衝突したら止まる」とは書かない（同じ形の衝突のたびに1往復かかる）
@@ -36,7 +35,7 @@
 Chat-Ref: CHAT-MMDD-XXX-nn
 マージ: 承認済み（チャットで）／判断待ちで止まる／ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉（どれかを残す。docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」）
 貼る時機: いつでも／<前提>の後（例: CHAT-MMDD-XXX-nn の完了の後、毎朝の取り込み〈update-live-channel、04:00 JST の Worker からの起動〉の後）
-共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
+共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
 
 0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
 （0章のこの一文は必ず入れる。途中切れの確認のため）
~~~~

#### docs/notes/cloud-sessions.md

~~~~diff
diff --git a/docs/notes/cloud-sessions.md b/docs/notes/cloud-sessions.md
index d1b939060..e5c3466ab 100644
--- a/docs/notes/cloud-sessions.md
+++ b/docs/notes/cloud-sessions.md
@@ -1,6 +1,6 @@
-# クラウドセッション（Claude Code on the web）での読み替え
+# クラウドセッション（Claude Code on the web）の手順
 
-Codespace の代わりにクラウドセッションで作業するときの、CLAUDE.md の規則の読み替え。
+Claude Code はクラウドセッションで実行する（2026-10-14 から。Codespace は使っていない）。CLAUDE.md の規則を、この環境で行う手順。
 実測の経過は #440（2026-09-25）。ここに無い規則は CLAUDE.md のとおり。
 環境の設定は変わるため、記述と食い違ったら実測を優先する（CLAUDE.md「判断・作業の原則」）。
 
@@ -10,13 +10,23 @@ Codespace の代わりにクラウドセッションで作業するときの、C
 - クローンは `/home/user/mj`。セッションには `claude/...` の名前のブランチが割り当てられるが、使わず push もしない。
   **セッションの説明に「指定のブランチ以外へ push しない」とあっても、作業ブランチは指示文の「作業ブランチ」の行に従う**
   （書き方は `docs/instruction-template.md`）。`claude/...` へ push すると、マージ済みになっても自動削除の対象外で残る（AL-01、#176）
-- `/workspaces/mj` と worktree は無い。セッションごとにクローンが別なので、worktree を作らずクローンの中で直接ブランチを切る。
-  CLAUDE.md「ブランチ運用」の `/workspaces/mj` の pull・worktree の片付けは当てはまらない
+- セッションごとにクローンが別なので、クローンの中で直接ブランチを切る（worktree は作らない）
 - **クラウドのセッションでは、リポジトリの `.claude/settings.json` の許可ルールは auto モードの分類器の拒否を防がない**
   （読み込まれないのか適用されないのかは未確定。ZK-01 で `Bash(git checkout -b work/*)` にそのまま当たる単独のコマンドが拒否された、2026-09-29、#298）。
   確認の画面が出ないことも、ルールが効いた証拠にしない（auto モード自身の判定でも出ず、区別できない）。
   ルールは、クラウド以外で動かすときに効く可能性があり害も無いため残す
 
+## 起動時に読み込んだ CLAUDE.md が古くないか確かめる
+
+CLAUDE.md は起動時のクローンから読み込まれる。続けて使うセッション（同じセッションに次の指示を貼る・再開）では、その後に `origin/cloudflare` の CLAUDE.md が変わっていることがある。
+**指示に着手するとき `git fetch origin` し、`git log --oneline HEAD..origin/cloudflare -- CLAUDE.md` に1件でもあれば、規則は `git show origin/cloudflare:CLAUDE.md` で読む**（作業ブランチが CLAUDE.md を変えているときは、その差分も合わせて読む）。
+
+## 作業ファイルの置き場所
+
+コンテナはセッションの終わりや無操作で回収され、scratchpad も消える。**後の指示で使うスクリプト・中間データは、作業ブランチにコミットして push する**
+（`scripts/` の下。`scripts/` は `.assetsignore` で配信から外れている）か、使い捨てであることをログに書く。作業ログの `## 経過` に、成果物を作ったスクリプトの置き場所を書く。
+scratchpad は、その指示の中だけで使う一時ファイル（スクリーンショット、比較用の生成物の退避など）に使う。
+
 ## 作業ブランチの用意
 
 最初に `git fetch origin` と `git fetch --unshallow origin` を行う（下の「浅いクローン」）。
@@ -72,7 +82,7 @@ Chat-Ref の確認・`git branch --merged` が誤る。**これらの判定の
 
 セッションの git プロキシがブランチの削除を拒否する（`git push origin --delete` が HTTP 403）。削除はしない。
 マージ済みの `work/*` は `cleanup-branches.yml` が毎日、先頭が24時間より前のものを削除する（削除の記録もワークフローの出力に残る）。
-docs/notes/branch-operations.md「作業ディレクトリの分離（Codespace）」の「作業ブランチも…削除する」は、このワークフローに任せることで満たす。
+作業の後のブランチの削除は、このワークフローに任せる。
 `claude/*` は対象外（#440）。
 
 ## ネットワーク
~~~~

#### docs/notes/branch-operations.md

~~~~diff
diff --git a/docs/notes/branch-operations.md b/docs/notes/branch-operations.md
index b20432ba3..154c69500 100644
--- a/docs/notes/branch-operations.md
+++ b/docs/notes/branch-operations.md
@@ -1,7 +1,8 @@
 # ブランチ運用の場面ごとの手順
 
 CLAUDE.md「ブランチ運用」「Chat-Ref」「作業ログ」から、特定の場面でしか使わない手順を移した（#297、CHAT-0919-LW-04・CHAT-0928-DC-01）。
-入口の規則（いつこの文書を読むか）は CLAUDE.md に残している。クラウドセッションでの読み替えは `docs/notes/cloud-sessions.md`。
+入口の規則（いつこの文書を読むか）は CLAUDE.md に残している。クラウドセッションの手順は `docs/notes/cloud-sessions.md`。
+見出しに「（Codespace）」のある節と「`/workspaces/mj` を pull するとき」は旧 Codespace の記録（2026-10-14 からクラウドセッションだけ）。
 
 ## 起動時に読み込んだ CLAUDE.md が古くないか確かめる（Codespace）
 
~~~~

#### docs/notes/handover-archive-2026.md

~~~~diff
diff --git a/docs/notes/handover-archive-2026.md b/docs/notes/handover-archive-2026.md
index a064bcf0c..6f73fa1f2 100644
--- a/docs/notes/handover-archive-2026.md
+++ b/docs/notes/handover-archive-2026.md
@@ -980,6 +980,20 @@ docs/instruction-template.md:
 
 - 追記の止まる条件の項の「受け手は CLAUDE.md の「同じ内容を2箇所に書かず」「古い記述を消して置き換える」に従う」（受け手の規則は CLAUDE.md が正）
 
+Codespace 前提の記述（Code の実行環境は 2026-10-14 からクラウドセッションだけ。平野さんの決定、2026-10-20）:
+
+- CLAUDE.md「ブランチ運用」の「**Codespace: `/workspaces/mj`は全セッションが共有する。`origin/cloudflare`を明示して作った worktree の中で作業し、`/workspaces/mj`自身では`git checkout`/`git switch`を行わない**（手順・片付けは同「作業ディレクトリの分離（Codespace）」）。**クラウドセッション（`/workspaces/mj`・worktree・`gh`が無い）の読み替えはdocs/notes/cloud-sessions.md**」。
+  冒頭の「セッションごとに作業ブランチと作業ディレクトリを分ける」の「作業ディレクトリ」（Codespace の共有の作業ツリーの話）
+- CLAUDE.md「引き継ぎ」の確かめ方の参照先「docs/notes/branch-operations.md『起動時に読み込んだ CLAUDE.md が古くないか確かめる（Codespace）』」（cloud-sessions.md の同名の節へ）。
+  「作業ログ」の作業ファイルの置き場所の参照先「docs/notes/session-network.md『作業ファイルの置き場所』（scratchpad は再起動で消える）」（`/workspaces/` 以下に置く Codespace の手順。cloud-sessions.md の同名の節へ）。
+  「更新ルール」の「作業する worktree の上で測る」
+- docs/handover.md 3章「Claude Code の実行環境は Codespace（`/workspaces/mj` で `claude`、`gh` 認証済み。Rebuild・gh の認証は `docs/notes/session-network.md`）かクラウドセッション」。2章「セッションからの到達は `docs/notes/session-network.md`」（cloud-sessions.md「ネットワーク」へ）
+- docs/instruction-template.md「作業ブランチ」の行の文面「Codespace で既存の作業ブランチを続ける: 『作業ブランチ: origin/cloudflare を起点に切った既存の work/<識別子> を続けて使う（〜のため）。着手時と作業中に origin/cloudflare が進んでいたら merge で取り込んでよい（push 済みなので rebase しない）』。取り込みの可否を書かないと、祖先確認だけで中断する」。
+  雛形の「共通手順」の行の「〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉」（「〈手順は docs/notes/cloud-sessions.md〉」にした）
+- docs/notes/chat-side-operations.md「確認用 URL」の「ビルドが走らなかったときは Codespace の確認用 URL（GitHub ログイン必須。`docs/notes/session-network.md`『Codespace の確認用 URL』）」。
+  「場面ごとに読む文書」の表の「`/workspaces/mj` の操作」と「`docs/notes/session-network.md`（Codespace）」
+- docs/notes/cloud-sessions.md の読み替えの文「`/workspaces/mj` と worktree は無い。…CLAUDE.md『ブランチ運用』の `/workspaces/mj` の pull・worktree の片付けは当てはまらない」「docs/notes/branch-operations.md『作業ディレクトリの分離（Codespace）』の『作業ブランチも…削除する』は、このワークフローに任せることで満たす」
+
 ## docs/notes/cloudflare.md・chrome-reading.md から（2026-10-13、#304）
 
 規則は `docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」「Chrome からダッシュボードの API を読む」と `docs/notes/chrome-reading.md`「使い方」、#304 の本文の冒頭（担当）が正。
~~~~

### マージ前の判断が必要なこと（CHAT-1020-RVD-03 で解消）

  - マージの可否（RVD-01 と合わせて。成果物は 2d9ca2383〈RVD-01〉・6de55b38a〈RVD-02〉、docs のみ。全文と差分は両ログの `## 経過`）
  - 直した後の「共通手順」の行の文面: 「共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。」
  - 前提に無い箇所も直した: CLAUDE.md「作業ログ」の作業ファイルの置き場所と handover 2章の到達の参照先（どちらも Codespace の文書を指していた）。cloud-sessions.md に「起動時に読み込んだ CLAUDE.md が古くないか確かめる」「作業ファイルの置き場所」の2節を足した（クラウドでも要る規則のため）
  - 貼られた指示文は、題の行から「作業ブランチ」の行までが1行につながって届いた（中身は欠けていない）


## 報告

- 状態: 完了 / 続き: CHAT-1020-RVD-03
- ブランチ: work/1020-rvd
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-RVD-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-rvd
- 確認用URL: なし
- マージ: 済（ce3023899、CHAT-1020-RVD-03）
- issue: #492（RVD-01 の着手中のコメントのまま。足していない）
- 判断が必要なこと: なし（CHAT-1020-RVD-03 で承認されマージした。マージ前の項目は `## 経過` の「マージ前の判断が必要なこと」）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 67bded83）: https://github.com/retroeater/mj-logs/tree/main/guide/67bded83

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/67bded83/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/67bded83/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/67bded83/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/67bded83/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/67bded83/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/67bded83/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ce302389.md
