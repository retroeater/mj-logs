# CHAT-1012-WHS-15

- 着手日時: 2026-10-12
- 対象issue: #194、#545
- ブランチ: work/1012-whs
- 着手時HEAD: 23130279

## 指示

【Claude作成】Claude Code 向け指示：帰り道の取り込み（WHS-13）の直し3点 — SNS ブックの更新から呼ばれた再生成では帰り道の手順を動かさない／新しい回の知らせではジョブを失敗にしない／題名「白鳥翔Vol.6 編」から名前を取り違えない。作業ブランチで手動実行して確かめ、cloudflare へマージする Chat-Ref: CHAT-1012-WHS-15 マージ: 承認済み（チャットで、2026-10-12。作業ブランチでの手動実行の結果が見込みどおりなら、そのままマージしてよい。条件は「止まる条件」のとおり） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-whs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-whs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-12 に、WHS-13 で入れた帰り道の取り込みの手順で2つの run が failure になった。1つは不具合、1つは作りどおりの知らせだが紛らわしい。あわせて、知らせの貼る1行の名前の取り違えを直す。
決定（2026-10-12、平野さん）

* SNS ブックの更新（`update-sns-book.yml`）から呼ばれた再生成では、帰り道の手順（YouTube の情報の取得・OGP 画像・知らせ）を動かさない。帰り道は週次と手動の `all` だけ（WHS-13 の決定どおり）。呼び出し側に `issues: write` を足す案は採らない
* 新しい回の知らせ（常設 issue「帰り道の新しい回」へのコメント）だけでは、ジョブを失敗にしない。取得・OGP 画像・調べる処理の失敗と、シートにあって JSON に無い回は、今までどおり失敗にする（WHS-02・03 の「知らせは失敗通知メールと常設 issue のコメント」のうち、新しい回はコメントだけに変える）
* 10/12 に見つかった2本（`Yjp_PKHvldw`「白鳥翔 編」・`OkKAh8yP8Uk`「白鳥翔Vol.6 編」）は両方ともシートに載せる。名前はどちらも「白鳥翔」（平野さんが貼るときに直す）。題名の「VolN」を名前から外す直しをこの指示で入れる
* 作業ブランチでの手動実行の結果が見込みどおりなら、そのままマージまで進めてよい

前提（チャット側。平野さんの決定ではない）

* 2026-10-12 にチャットが Actions の注記・手順の結果・#545 を REST で確かめた（ジョブのログ本文は読めていない）:
   * `update-sns-book.yml` #13（run 38166926033）: 呼んだ再生成（`target_page: all`、呼び出し側の `permissions` は `contents: write` だけ）で「帰り道の前回の知らせを読む」が `HttpError: Resource not accessible by integration` で失敗。ページの生成・push（「変更をコミット・push」）は成功
   * `regenerate-page.yml` #276（run 38186096774、schedule）: 新しい回2件を見つけて #545 にコメントし（2026-10-11T23:59:06Z）、「帰り道の取り込みの失敗を報告」で `exit 1`（`check_wayhome.py` の `fail = bool(new or missing_json)`）。ページの生成・push は成功、飛ばしたページなし
   * #545 の貼る1行の名前が `白鳥翔Vol.6` になっている。`scripts/lib/wayhome_intake.py` の `SUFFIX_RE`（`\s*編?\s*(vol\.?\s*\d+)?\s*$`）は「編vol.2」の順だけを外し、「Vol.6 編」の順を外さないため（と読んでいる。要確認）
* `workflow_call` では `github.event_name` が呼んだ側のもの（`schedule` など）なので、今の条件（`github.event_name != 'push' && (inputs.target_page == '' || inputs.target_page == 'all')`）では SNS ブックからの呼び出しを見分けられない。案: `workflow_call` に入力（例 `wayhome`、boolean、既定 false）を足し、帰り道の手順の条件を「schedule・workflow_dispatch の all、または workflow_call で `wayhome` が true」にする。SNS ブック・/live の取り込みからの呼び出しは何も渡さない（= 動かない）。実物に合わせてより簡単な見分け方があればそれでよい
* 常設 issue #545 の本文の「知らせの種類とすることの表」に「失敗にする」旨があれば、今回の決定に合わせて直す（本文の書き換えは Code が行う）
* `docs/notes/video-wayhome.md`・`docs/decisions/wayhome.md` に同じ趣旨の記述があれば置き換える

手順

1. 確かめる: 上の「前提」の読み（失敗の原因2つと `SUFFIX_RE`）を実物で確かめ、ログに書く。食い違えば止まる。未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）が `regenerate-page.yml`・`update-sns-book.yml`・`scripts/check_wayhome.py`・`scripts/lib/wayhome_intake.py` の同じ所を変えていれば止まる
2. 直す: 決定の3点を実装する。テストを足す（「白鳥翔Vol.6 編」「白鳥翔 編vol.2」「白鳥翔編」から名前が「白鳥翔」になる、新しい回だけでは fail が false・JSON に無い回があれば true）。`python3 -m unittest discover -s scripts/tests` が通ること。#545 の本文、`docs/notes/video-wayhome.md` を直し、決定を `docs/decisions/wayhome.md` に足す
3. 手動実行してマージする: 作業ブランチで `regenerate-page.yml` を `all` で手動実行する（push 先が作業ブランチであることを実行の前に確かめる）。見込み: 帰り道の手順が動き、ジョブは success（平野さんが2本をまだ貼っていなければ新しい回2件のまま知らせの状態は変わらず、コメントは書かない。貼った後なら状態が変わったコメントが1件つく。どちらでも可、どちらだったかを書く）。名前の直しは #545 の次のコメントにしか出ないので、テストで確かめたことにする。SNS ブックからの呼び出しで帰り道の手順が skipped になることは、条件式をログに引用して示し、実物はマージ後の次の `update-sns-book.yml`（毎日 04:10 JST。SNS ブックの【3】が変わったときだけ再生成を呼ぶ）で確かめる項目として「未確認の項目」に書く。見込みどおりなら CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、push を契機の再生成と Workers Builds の check-run を待つ（それぞれ上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。#194 にこの直しをコメントする。作業ブランチを片付ける

止まる条件

* 手順1の読みが実物と食い違う、または未マージのブランチと重なる
* 作業ブランチでの手動実行が cloudflare に push する作りになっている、または手動実行を起動できない（403 など。平野さんに「Run workflow」を頼む旨を書いて止まる）
* 手動実行の結果が見込みと違う（ジョブが failure、帰り道の手順が skipped、帰り道以外のページの生成物が変わる、など）。違いを書いて止まる
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（作業ブランチでの手動実行の run 番号と結論を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-WHS-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-WHS-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-WHS-15"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- 作業ブランチ: `work/1012-whs` はローカル・リモートとも無し → `git checkout -b work/1012-whs origin/cloudflare`（23130279）

### 手順1: 確かめ（前提どおり）

- `update-sns-book.yml` run 38166926033: ジョブ `update` は success、呼んだ `regenerate / regenerate` が failure。止まった手順は「帰り道の前回の知らせを読む」、注記は `Unhandled error: HttpError: Resource not accessible by integration`。呼び出し側のジョブは `permissions`（`contents: write`）で `uses: ./.github/workflows/regenerate-page.yml`・`target_page: all`
- `regenerate-page.yml` run 38186096774（schedule）: 失敗した手順は「帰り道の取り込みの失敗を報告」だけ。`check_wayhome.py` は `fail = bool(new or missing_json)`
- `SUFFIX_RE` は `\s*編?\s*(vol\.?\s*\d+)?\s*$`。「《第15期麻雀グランプリMAX 白鳥翔Vol.6 編》」は名前の一覧があってもなくても名前が `白鳥翔Vol.6` になった（「 編」だけが外れ、「Vol.6」が残って名前の末尾一致に失敗し、空白の後ろが名前になる）。「白鳥翔編vol.2」「白鳥翔 編」は `白鳥翔`
- 未マージのブランチで `regenerate-page.yml`・`update-sns-book.yml`・`check_wayhome.py`・`wayhome_intake.py` に触れるものは無い

### 手順2: 直し（c3362de8・c8036e85）

- **呼ばれた再生成で動かさない**: 「帰り道のYouTube情報を取得」「帰り道の一覧のOGP画像を作る」の条件に `contains(github.workflow_ref, '/regenerate-page.yml@')` を足した。
  `workflow_call` では `github.workflow_ref` が呼んだ側のワークフロー（`update-sns-book.yml` など）になるので、直接の週次・手動実行だけが通る。後の「前回の知らせを読む」は `steps.fetch_wayhome.outcome != 'skipped'`、それ以降はその結果を条件にしているので、続けて skipped になる。
  案の `workflow_call` の入力（`wayhome`）は、`workflow_dispatch`・`schedule` で未定義の入力が `false` と等しく扱われうるため採らず、呼び出し側も変えない形にした。条件式（2つの手順で同じ）:
  ```
  if: github.event_name != 'push' && (inputs.target_page == '' || inputs.target_page == 'all') && contains(github.workflow_ref, '/regenerate-page.yml@')
  ```
- **新しい回では失敗にしない**: `lib/wayhome_intake.py` に `should_fail(state)`（JSON に無い回があるときだけ真）を足し、`check_wayhome.py` が使う。「帰り道の取り込みの失敗を報告」の文言も直した（取得・OGP・調べる処理の失敗と JSON に無い回は今までどおり失敗）
- **名前の Vol**: `SUFFIX_RE` を `(\s*(編|vol\.?\s*\d+))+\s*$`（大文字小文字を問わない）にし、「編」と「vol.N」をどちらの順でも外す
- 知らせることが無いときのコメントの文言を、前回の知らせがあるときは「前回の知らせから変わったため」にした（下の手動実行で「前回の知らせが無いため」と書いたのを見て直した。c8036e85）
- テスト: 「白鳥翔Vol.6 編」「白鳥翔 編vol.2」「白鳥翔編」で名前が `白鳥翔`（選手名の一覧があってもなくても）、新しい回だけでは `should_fail` が偽・JSON に無い回があれば真、文言の分け方。修正前のコードでは失敗1・エラー1、修正後は全体 OK
- #545 の本文を直した（新しい回の行に「ジョブは失敗にしない」、失敗にする場合の段落、「ほかのワークフローから呼ばれた再生成では動かない」）。`updated_at` を直す前に取り直して変わっていないことを確かめ、REST の PATCH で
- 文書: `docs/notes/video-wayhome.md`（手順・表の「新しい回」を成功に、呼ばれた再生成では動かない）。決定を `docs/decisions/wayhome.md` に足し、WHS-02 の「知らせ先は…両方」の行に「→ 置き換え」を付けた

### 手順3: 作業ブランチでの手動実行（run 38193457871）

- `actions_run_trigger`（ref `work/1012-whs`、`all`）。push 先は `HEAD:${{ github.ref_name }}`（作業ブランチ）。**success**（01:50:38Z〜約3分半）
- 帰り道の手順（取得・OGP・前回の知らせを読む・調べる・知らせを書く）はすべて success、「帰り道の取り込みの失敗を報告」は skipped
- **平野さんが2本をシートに貼った後だった**: `Yjp_PKHvldw`（第6期鸞和戦 #2 白鳥翔）・`OkKAh8yP8Uk`（第6期鸞和戦 #1 白鳥翔）の YouTube の情報が取れ、各話が **42枚**に。
  コミット 13b38ae3 の差分は `data/youtube_meta.json`（2本の追加、`missing_video_ids` は空）・2本のページ・白鳥翔の4回と最新話の前後のカード（`2Bn3SktouP4`・`8w7SW0XgX64`・`WztsfHBisUM`・`atD2e-NgnKw`・`kf3RaFtz8o8`）・`video_wayhome.html`・sitemap の lastmod と、
  **一覧の OGP 画像の名前が `index-20261011-2.jpg` に変わった**（最新話2本が同じ公開日 2026-10-11 なので連番。旧 `index-20260921.jpg` は削除）。帰り道以外の生成物は変わっていない
- 状態が「新しい回 2件」から「0件」に変わったので、#545 にコメントが1件ついた（01:54:01Z）。文言は「前回の知らせが無いため」だったので直した（上）。名前の Vol の直しは次の新しい回のコメントにしか出ないので、テストで確かめた
- SNS ブックからの呼び出しで帰り道の手順が skipped になることは、マージ後の次の `update-sns-book.yml`（毎日 04:10 JST、【3】が変わった日だけ再生成を呼ぶ）で確かめる

### マージと確かめ

- fetch 直後に `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1012-whs:cloudflare`（23130279..32ed04cb。作業ブランチでの手動実行のコミット 13b38ae3 を含む）
- 32ed04cb の check-run: regenerate（push 契機。`.github/` と `scripts/lib/` の変更で全ページ）success・Workers Builds: mj success・check・sync success（01:58Z）。push 契機の再生成はコミットを作らなかった
- 本番（`?v=` に未使用の値）: `/wayhome/OkKAh8yP8Uk.html`・`/wayhome/Yjp_PKHvldw.html`・`/video_wayhome.html`・`/img/ogp/wayhome/index-20261011-2.jpg` が 200 で、手元の生成物と一致
- #194 に直しをコメントした
- 作業ブランチの片付け: クラウドセッションはブランチを削除できない。`work/1012-whs` はマージ済みなので `delete-merged-branches.yml` に任せる

## 報告

- 状態: 判断待ち
- ブランチ: work/1012-whs（マージ済み。削除は delete-merged-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1012-WHS-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-whs
- 確認用URL: なし（作業ブランチでの手動実行と本番で確認）
- マージ: 済（32ed04cb）。作業ブランチでの手動実行 run 38193457871 は success（帰り道の手順はすべて動き、失敗の報告は skipped。2本が取り込まれ各話42枚、OGP は `index-20261011-2.jpg`）
- issue: #194（コメント）、#545（本文を直した。状態が変わったコメントが1件ついた）
- 判断が必要なこと:
  - 下の未確認の項目を、次の `update-sns-book.yml` の結果で確かめてから完了にする（確かめる指示か、チャットでの REST の確認で）
- 未確認の項目:
  - SNS ブックの更新（`update-sns-book.yml`）から呼ばれた再生成で、帰り道の手順が skipped になり失敗しないこと（条件式は経過に引用。次の 04:10 JST の実行で、【3】が変わって再生成を呼んだ日に分かる）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 32ed04cb）: https://github.com/retroeater/mj-logs/tree/main/guide/32ed04cb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/32ed04cb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/32ed04cb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/32ed04cb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/32ed04cb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/32ed04cb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/32ed04cb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/32ed04cb.md
