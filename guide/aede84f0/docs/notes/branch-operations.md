# ブランチ運用の場面ごとの手順

CLAUDE.md「ブランチ運用」「Chat-Ref」「作業ログ」から、特定の場面でしか使わない手順を移した（#297、CHAT-0919-LW-04・CHAT-0928-DC-01）。
入口の規則（いつこの文書を読むか）は CLAUDE.md に残している。クラウドセッションでの読み替えは `docs/notes/cloud-sessions.md`。

## 起動時に読み込んだ CLAUDE.md が古くないか確かめる（Codespace）

**会話の開始時に `git -C /workspaces/mj fetch origin` し、`/workspaces/mj` の HEAD が `origin/cloudflare` より遅れていたら、
起動時に読み込まれた CLAUDE.md は古い版とみなし、規則は `git show origin/cloudflare:CLAUDE.md` で読む。**
`/workspaces/mj` を pull しても読み込み済みの内容は変わらないため、読み直しは要る。

## 作業ディレクトリの分離（Codespace）

`/workspaces/mj` は全セッションが共有しており、ブランチを分けても作業ツリーは分離されない（#198）。

- **`git -C /workspaces/mj fetch origin` のうえ、`origin/cloudflare` を明示して worktree を作り、その中で作業する**
  （`/workspaces/mj` の HEAD は遅れていることがあるため、指示文に書かれていなくても常に行う）:
  `git -C /workspaces/mj worktree add /workspaces/mj-<識別子> -b work/<識別子> origin/cloudflare`
- ブランチの作成・切り替え・進める操作は、`cd`・`;`・`|`・`&&` を付けない単独のコマンドで実行する（場所は `git -C <パス>` で指定する）。
  既存のブランチを付け替える `checkout -B`・`branch -f`・`reset --hard` は使わず、進めるときは `git merge --ff-only origin/cloudflare` を使う（#298）
- 分岐元が `cloudflare` であることを `git merge-base --is-ancestor origin/cloudflare HEAD` で確認する（work/0913-hv）
- `/workspaces/mj` 自身では `git checkout` / `git switch` を行わない。常に他セッションが使用中とみなす。
  pull してよいのは下の「`/workspaces/mj` を pull するとき」の3条件をすべて満たすときだけ
- 身に覚えのない未コミット変更を見つけたら、捨てる前に `git diff` で中身を確認し、他セッションの作業でないか疑う
- コミットを分割するときは `git add -p`、または `git diff` で切り出したハンクを `git apply --cached` で部分ステージする
  （`git stash` は共有パスの他セッションの未コミット編集を無言で消しうる、#198）
- 作業完了後は `git worktree remove` で片付け、作業ブランチも（下の「ブランチを削除するとき」の手順で）削除する

## Chat-Ref の着手前の確認

### セッション識別子（`XXX`）の重複

そのセッションの最初の指示で、次の2つを見る。1件でもあれば着手せず、見つかった Chat-Ref とブランチを報告する。
使用中の識別子の一覧が要るときも同じ2つで集める。`XXX` は確かめる識別子に置き換える（既存の2文字なども同じ形で引ける）。
使用済みの一覧を履歴から集め直して mj-logs に写す仕組みは #474 で作る（まだ無い）。

- 全ブランチのコミット: `git log --all -E --grep 'CHAT-[0-9]{4}-XXX-' --oneline`
- 全ブランチの `docs/logs/`: `git log --all --diff-filter=A --format= --name-only -- 'docs/logs/CHAT-*-XXX-*.md'`
  （マージ後に削除されたログも履歴に残るため、履歴で見る）

チャット側も mj-logs で確かめるが、写るのは #440 以降に push されたログだけなので、受け手側のこの確認は省かない。

### 0章ゲート（指示文に実装が含まれるとき）

着手前に次を確認し、食い違いや重なりがあれば着手せず報告して止まる。

- 対象 issue の本文とコメントを読み、指示文の範囲と食い違いが無いか
- `docs/handover.md` と CLAUDE.md のサイズが警告域（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）に近くないか
- `origin/cloudflare` の直近のコミットと、触る予定の issue の最近の更新を見て、他セッションの作業と重なっていないか（#265）
- 触る予定のファイルを、未マージの work/ ブランチが変更していないか（`git branch -r --no-merged origin/cloudflare`
  で列挙し、各ブランチとの `git diff --stat origin/cloudflare...<branch> -- <ファイル>` で確認）（#336）

## `/workspaces/mj` を pull するとき（CHAT-0915-NT-04）

`/workspaces/mj` は全セッションが共有する作業ツリーで、原則として触らない。**次の3条件をすべて満たすときに限り、
セッションの判断で `git -C /workspaces/mj pull --ff-only` を行ってよい。**

1. ブランチが `cloudflare` であること
2. `git -C /workspaces/mj status --porcelain` が空であること
3. HEAD が `origin/cloudflare` の祖先であること

1つでも満たさなければ触らず、状態を報告する（未コミット変更の扱いは上の「作業ディレクトリの分離」）。
**`--ff-only` 以外の pull（merge・rebase を伴うもの）・merge・reset はしない。**

- 理由: 放っておくと起動時の CLAUDE.md が遅れる。切り替え・未コミット変更の破壊（WH-22）は3条件で防げる

## ブランチを削除するとき（#209、2026-09-13決定）

- マージ済みの判定は、削除の直前に、完全な履歴（浅いクローンでは判定できない）に対して、
  **origin基準（`git branch --merged origin/cloudflare`）で行う。** 指示文に書かれたSHAや判定結果は
  作成時点のもので古くなりうるため、根拠にしない（#206）
- `/workspaces/mj`のHEADとローカル`cloudflare`は遅れていることがある。`git branch -d`が通ったか・
  警告（"not yet merged to HEAD"等）が出たか・「not fully merged」で拒否されたかを判定の根拠にせず、
  先頭が`origin/cloudflare`の祖先と確認したうえで`-D`で削除する（#313、2026-09-14）
- 祖先でなかった場合も「未マージ」と即断しない。同じ内容が別SHAで`cloudflare`に入っていないかを、
  件名・差分の突き合わせで確認してから判定する
- 未マージのブランチは削除しない。内容（コミット数・変更ファイル・関連issue番号）を記録して平野さんの判断を仰ぐ
- **削除する際は、削除直前の先頭SHAを必ず記録に残すこと。** 記録先は対応するissueのコメント
  （issueが無い場合は作業ログの`## 経過`）。ブランチ名・SHA・マージ済み/未マージの判定を、ブランチごとに1行で書く。
  削除後は判定の検証も誤削除の復旧もできなくなるため（#207）
- **積み直して（rebase / cherry-pick / squash）マージした場合は、元のSHAと、対応する`cloudflare`側のSHAの
  両方を記録すること。** 積み直すと元のSHAは`cloudflare`の祖先にならず、祖先関係だけでは検証できない（#209）

## ワークフローを変更したとき

- **着手時に、同種の既存ファイルの履歴（`git log -- .github/workflows/<file>`）と関連する過去のログ・issueを見て、
  GitHubの仕様で初回だけ手順が違う点が無いか確かめる**（CHAT-0918-HT-06）
- **`.github/workflows/`配下を変更したら、マージ前に作業ブランチでワークフローを手動実行し結果を確認すること。**
  `gh workflow run <ファイル名> --ref <作業ブランチ>`で起動し、`gh run list --workflow=<ファイル名>`で
  runを特定して`gh run view <run-id> --log`を見る。省くと一度も実行されていないワークフローがマージされる。
  セッションのトークンで起動が403になる場合は、平野さんにGitHub画面の「Run workflow」を依頼する。
  **`workflow_dispatch`を持たないワークフローはマージ前に検証できないため、その旨を報告し判断を仰ぐこと**
- 新規追加のワークフローは既定ブランチに無いため`gh workflow run --ref <作業ブランチ>`が404になり、マージ前に実行できない。
  その場合は副作用の無い状態（dry-run 既定、schedule も dry-run で走る等）でマージし、`cloudflare`で手動実行して確認してから有効化する（CHAT-0918-HT-06/08）

## 生成物を含む作業ブランチを取り込む・マージするとき（CHAT-0918-SX-16）

- **見た目を変える作業では、プレビュー（Workers Builds の確認用URL）のために作業ブランチへ生成物をコミットする。**
  生成物をコミットしなければ衝突は起きないが、プレビューが本番と同じ中身になり確認に使えない（CHAT-0918-SX-09 で実際にそうなった）。
  そのため cloudflare の自動再生成と同じファイルを両側で変えることになり、`origin/cloudflare` の取り込みで衝突しうる
  （SX-10 で `saikyo/` の12ファイル。SX-11・SX-15 の取り込みは衝突なし、#384・#406）
  - 衝突した生成物は、どちらの版も選ばず、取り込んだ後のスクリプトで生成し直して解く（決まりは CLAUDE.md「ブランチ運用」）。
    衝突の印を消すには cloudflare 側を採ってから（`git checkout --theirs -- <ファイル>` → `git add`）再生成で上書きする。
    再生成の後、採った側の中身が残っていない（全ファイルが今回の生成物になっている）ことと、双方の変更（自分の変更と、cloudflare 側で入ったシートの変更など）が残っていることを確かめ、ログに書く
    （CHAT-0924-TQ-18・TQ-26・TQ-27 で `title/` の生成物だけが衝突し、この形で解いた）
  - 生成物以外（スクリプト・CSS・手で編集するサイトマップ等）が衝突したら、解消せずに止まって報告する。サイトマップは、生成スクリプトが書き出すもの（`sitemap-title.xml` など）は生成物として生成し直し、手で編集するもの（サイトマップインデックスの `sitemap.xml` など）が衝突したら止まる
  - 手元（Codespace）の生成は、画像の到達確認の結果が GitHub Actions と違うことがある（SX-11〜SX-14 で `_200x200` が1件だけ Codespace から 404、#380）。
    作業に関係しない生成物の差分はコミットに含めない
- **マージ後の自動再生成は、変えた箇所より広く出る。** `scripts/lib/` や生成スクリプトを変えてマージすると、
  `regenerate-page.yml` が関係するページを再生成し、その時点までのシートの変更もまとめて本番に出る。
  例: SX-09 は /live・/title の見出し名の修正だったが、`scripts/lib/` の変更で全生成ページが対象になり、
  最強戦の年度ページと `saikyo_results.html` に SX-07 で貼った対局日が出た（自動再生成 9b6d459）。
  マージの指示を書く側・受ける側とも、再生成で変わる範囲（対象ページと、前回の再生成以後のシートの変更）を先に見積もる

## 未コミットの変更を戻すとき（2026-09-27）

生成物の差分を確かめたあとなどに作業ツリーを戻すときは、**戻すファイル・ディレクトリを名指しする**
（例: `git checkout -- saikyo/ title/`、`git restore -- <パス>`）。
`git checkout HEAD -- .`・`git reset --hard`・`git clean -fd` のように作業ツリー全体を対象にするコマンドは、
書きかけの作業ログなど**関係の無い未コミットの変更まで無言で消す**。UT-20 で、確認のために `git checkout HEAD -- .` を使い、
未コミットだったログの追記を消した（書き直した）。

- 戻す前に `git status --short` で未コミットの変更を見て、残したいもの（ログ等）は先にコミットする
- 別のコミットの中身を確かめたいだけなら、作業ツリーに展開せず `git show <コミット>:<パス>` や `git diff <A> <B> -- <パス>` で読む
- 退避に `git stash` は使わない（CLAUDE.md「禁止事項」、上の「作業ディレクトリの分離」）

## 作業ログの寿命（cleanup-logs.yml）

`cleanup-logs.yml`が週次で片付ける。対象は`cloudflare`上のログのみ（2026-09-18、CHAT-0918-HT-06/08/09）。判定の正は`scripts/cleanup_logs.py`。

- 最終コミットから7日以上たち、`## 報告`の状態が完了で「判断が必要なこと」「未確認の項目」「エラー」がすべて「なし」のログは自動で削除される。
  `## 報告`節の無い旧形式のログも7日を過ぎれば削除される。ただし`## 未決・判断待ち`節に「なし」以外が書いてあるものは削除せず通知する（削除もコミットとして残り、履歴から復元できる）
- それ以外（判断待ち・中断・「なし」でない項目がある）は削除されず、常設issue #357 にコメントで通知される。
  通知を受けたときの扱いは CLAUDE.md「作業ログ」節

## 過去の作業ログを直す・参照するとき（2026-09-20、CHAT-0919-HG-08）

- **過去のログの誤りは、元の記述を書き換えずに、該当箇所の直後に「訂正（<Chat-Ref>）」の段落を足す。**
  字下げした箇条で、正しい事実と根拠（どのログ・issue か）を書く。同じ誤りが他の箇所にもあれば、そこには訂正の段落への案内を1行置く
  （前例: https://github.com/retroeater/mj/blob/4be965e22e7e64c4ba0db6400a943ffcd17a99f2/docs/logs/CHAT-0919-HG-01.md の「横断のまとめ」a の直下の HG-04 の訂正〈`0174702c`〉、c の直下の HG-06 の訂正〈HG-07 で追加、`7f62893e`〉）
- **ログを SHA 指定の permalink で参照するときは、そのログへの訂正をすべて含む最新の版の SHA で固定する。**
  ログは cleanup-logs.yml で消えうるので、パスではなく permalink にするが、古い版を指すと訂正が読めない
  （HG-06 で訂正前の `a7146487` の版を指定し、HG-07 で `7f62893e` に差し替えた）。
  後から訂正を足したら、そのログを permalink で参照している箇所（issue・docs）も差し替える。permalink の SHA は `cloudflare` の祖先であること（ブランチを消しても残る）を確かめる
