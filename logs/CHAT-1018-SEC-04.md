# CHAT-1018-SEC-04

- 着手日時: 2026-10-18
- 対象issue: #598（関係: #564・#557・#504）
- ブランチ: work/1018-sec4
- 着手時HEAD: 1cd1d6f2b

## 指示

【Claude作成】Claude Code 向け指示：GitHub Actions のセキュリティの直し（SHA 固定・permissions・secrets の明示・template-injection・persist-credentials・dependabot.yml）と mj-scheduler の小さな直し（#598 の中-4・中-5・低-1・低-2） Chat-Ref: CHAT-1018-SEC-04 マージ: 承認済み（チャットで）。ただし下の「確かめ」が1つでも通らない・止まる条件に当たったときはマージせずに報告する 貼る時機: いつでも。ただし JST 03:45〜05:15（毎朝の運行の起動の間）と月曜 05:00〜07:00（週次の cron 実行）は、`.github/` を含む cloudflare への push と手動実行をしない（その時間帯に当たったら終わるまで待ってよい。待ちは CLAUDE.md の15分の上限の例外とする）。新しいセッション: 推奨（変更が広く長い作業になるため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-sec4 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-sec4 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-sec4 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。作業中に origin/cloudflare が進んだら merge で取り込んでよい（rebase しない）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1017-SEC-01 の点検（#598）で残った GitHub Actions の設定の穴を直す。直した後で、平野さんが Settings の Workflow permissions の既定を読み取りだけに切り替えられる状態にする。あわせて CHAT-1018-SEC-05（Claude Security の診断）で参考に挙がった `mj-scheduler` の小さな点を直す。
決定（2026-10-18、平野さん）

* SEC-04 を WFL（#564）の段④より先に、別の指示で行う（WFL のチャットの推奨。18時の「段④の後」を置き換える）。段④は SEC-04 のマージを待ってから着手する（WFL 側がそう決めた）
* 直すもの（#598）: 中-4（第三者 action の SHA 固定と `.github/dependabot.yml`）、中-5（`secrets: inherit` を必要な Secret の明示に、ワークフロー全体の書き込み権限をジョブ単位に、`permissions` の無いワークフローに明示）、低-1（`run:` に `${{ github.* }}` などを直接埋めている箇所を env 経由に）、低-2（push しないジョブの `actions/checkout` に `persist-credentials: false`）
* `.github/dependabot.yml` は SHA 固定と同時に入れる（CHAT-1018-SEC-02 で SEC-04 に回したもの）
* Workflow permissions の既定（今は Read and write・「Allow GitHub Actions to create and approve pull requests」にチェックあり）は、この指示のマージの後に平野さんが手で読み取りだけにし、チェックを外す
* `mj-scheduler` の参考3件も一緒に直す

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
WFL のチャットからの注意（2026-10-18 の伝言。そのまま守る）

1. 入れ子の呼び出しの権限: 毎朝の運行は `run-daily.yml` → 各ワークフロー（`workflow_call`）→ `regenerate-page.yml`・`sync-yotei.yml`（`workflow_call`）の最大3段。呼ばれる側の job の `permissions` は呼ぶ側の job の権限を超えられない。呼ばれる側を絞るのはよいが、呼ぶ側（`run-daily.yml` の各 job など）を呼ばれる側の必要より狭めると毎朝の運行が止まる
2. `secrets: inherit` を明示に変えるときは入れ子をたどる: `run-daily.yml` の6つの job、update-live-channel → regenerate-page・sync-yotei、update-sns-book → regenerate-page、regenerate-saikyo → regenerate-page、fetch-youtube → regenerate-page。入れ子の下で使う Secret も途中の段で渡し直す。呼ばれる側の `on.workflow_call.secrets` に受け取る Secret を宣言する
3. 確かめ方: `run-daily.yml` は入力 `scheduled` なしの手動実行が常にドライラン（書き込みなし）。作業ブランチでも cloudflare でも、`scheduled` なしの手動実行で入れ子3段・`permissions`・`secrets` を書き込みなしで確かめられる（所要 12〜15 分、CHAT-1015-WFL-16 と同じ確かめ）
4. 時間帯: 上の「貼る時機」のとおり
5. Settings の既定を読み取りだけにする前に、運行表（https://retroeater.github.io/mj-logs/actions/）の全ワークフロー（`workflow_call` だけで動くもの〈sync-yotei など〉を含む）に `permissions` の明示があることを確かめる
6. 用語は docs/notes/glossary.md に合わせる（定期実行・フォールバック実行・cron 実行・本実行／ドライランなど）

範囲

* 対象は `.github/workflows/*.yml` と `.github/actions/*`。`sync-logs.yml` は触らない（#298 で 10/21 に削除の予定）
* WFL の段④が後で触るのは `on:` の `schedule:` の行と update-live-channel のフォールバック実行のゲートのステップ。`on:` の `schedule:` の行は変えない。ゲートのステップの `run:` の env 化は行ってよい（段④で消えるかもしれないが、それまでの穴を残さない）
* `permissions` は、各ワークフローのトップレベルを `permissions: {}`（または `contents: read`）にし、job ごとに必要な最小を書く。必要な権限は実物（`git push`・`gh issue`・`actions/github-script`・`gh workflow run`・`gh api` の使い方）から決め、job ごとの根拠をログに表で書く
* SHA 固定: `actions/checkout`・`actions/setup-python`・`actions/github-script`・`google-github-actions/auth` などの第三者 action を、今のタグ（`@v4` など）が指すコミットの SHA に固定し、`# v4.x.y` の形でタグをコメントで残す。SHA の取り方の案: `git ls-remote --tags https://github.com/<owner>/<repo>`（注釈付きタグは `^{}` の行）。セッションから取れない場合（docs/notes/security.md「届いた外部 API」のとおり `api.github.com` は mj だけ）は、SHA 固定と `dependabot.yml` をこの指示から外して残りを進め、取り方の候補（例: 手動実行だけの小さなワークフローで `git ls-remote` を回す、平野さんが PC で取る）を報告に書く（止まらない）。CHAT-1018-SEC-02 で SHA 固定済みの `security-check.yml` は、同じ版にそろっているかだけ見る
* `.github/dependabot.yml`: `package-ecosystem: github-actions`、`directory: "/"`、月1回、`target-branch: cloudflare`。PR が作るブランチ（`dependabot/…`）と `cleanup-branches.yml`・ブランチ運用の規則との関係を docs/notes/github-actions.md に書く（PR のマージの仕方は「Dependabot の PR はチャット経由で Code に確かめさせてからマージする」を案として書き、決めない）。Dependabot security updates は Off のまま（Settings は変えない）
* `persist-credentials: false`: push・`gh` で書き込みに checkout の認証を使うジョブには付けない。付けたジョブと付けなかったジョブを表で書く
* 低-1: `fetch-gsc.yml`・`regenerate-sitemap.yml`・`update-live-channel.yml`（SEC-01 で zizmor の template-injection High 4件）と、Informational の6件も env 経由にそろえる
* `mj-scheduler`（`workers/scheduler/`）の参考3件（CHAT-1018-SEC-05 の「突き合わせ」）: (a) `scheduler.mjs` の `checkMorningRetry`（386〜391行付近）が #506 のコメントの書き手を見ない → 書き手が `retroeater` または Actions のボット（`github-actions[bot]`）のときだけ数える、(b) `schedule.json` の `workflow` を検査せず API のパスに入れる → `^[a-z0-9-]+\.ya?ml$` に合わないものを捨てて `console.error` に書く、(c) `assets/houou_ask.js` の `image.src = thumb`（130行付近）が scheme を確かめない → `https:` 以外は使わない。(c) は `work/OUK` などが `assets/houou_ask.js` を変えていれば外して報告に書く。テストを足し、`node --test 'workers/scheduler/test/*.test.mjs'`・`'workers/ask/test/*.test.mjs'` を回す。`wrangler deploy` はしない（Workers Builds がマージで反映する）
* 並行の作業: PRV（#557、スプレッドシートの非公開化）が `regenerate-page.yml` に dry_run・check と新しいサービスアカウントの Secret を足す見込み。この指示の後に新しい Secret を足すときは、入れ子の各段で明示して渡す必要があることを docs/notes/github-actions.md に書き、#557 にもコメントする。WFL の BIL-01（`check-assets.yml` の起動条件）も並行の見込み

確かめ（マージの前に全部通す）

* `zizmor --offline .github/` の件数を直す前と後で表にする（docs/notes/security.md の版。SEC-02 の初回は 153 件）。中-4・中-5・低-1・低-2 にあたる監査（unpinned-uses・excessive-permissions・secrets-inherit・template-injection・artipacked）が 0 になるか、残るものの理由を書く
* `python3 -m unittest discover -s scripts/tests`（`test_workflows_table.py` を含む）と Worker のテストが通る
* 作業ブランチで `run-daily.yml` を `scheduled` なしで手動実行し（ドライラン）、全ジョブが success（12〜15 分）。直したワークフローのうち、`workflow_dispatch` を持ち dry_run の入力があるものは dry_run で1回ずつ手動実行する（本実行はしない）。`workflow_dispatch` を持たないもの・ドライランが無いものは一覧にして報告に書く（CLAUDE.md の規則どおり判断を仰ぐ対象。ただし `permissions` の明示の追加だけで中身が同じものは、run-daily のドライランで呼ばれていればそれで足りるとしてよい）
* セッションのトークンで手動実行が 403 になるときは、平野さんに GitHub の画面の「Run workflow」を頼む（直接開けるリンクを書く）。そのときはマージせずに判断待ちで止まる

手順

1. 調べる: #598・#564・#557・#504 の状態、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `.github/` と `workers/scheduler/`・`assets/houou_ask.js` のどの行を変えているか。#598 に着手中のコメント。直す前の zizmor の件数と、各ワークフローの job ごとの必要な権限・使う Secret の表（入れ子をたどったもの）を作ってログに書く
2. 直す（節目ごとにコミットして push）: (a) `permissions` と secrets の明示 → (b) env 化・`persist-credentials` → (c) SHA 固定・`dependabot.yml` → (d) `mj-scheduler` と `houou_ask.js`。docs/notes/github-actions.md に「権限と Secret の渡し方」（入れ子の表、新しい Secret を足すときの手順）、「Dependabot」の節を足す。docs/notes/security.md の (d) の観点を今の状態に合わせる
3. 確かめ（上の「確かめ」）→ マージ → cloudflare で `run-daily.yml` を `scheduled` なしでもう一度手動実行して success を確かめる → `security-check.yml` を手動実行して zizmor の件数を記録 → #598 にコメント（直したもの・残ったもの・zizmor の前後）し、本文の表の中-4・中-5・低-1・低-2 を「済（CHAT-1018-SEC-04）」にする。#557 にコメント。docs/decisions/security.md に上の「決定」を足す

止まる条件

* 未マージの work/ ブランチが、変えるワークフローの同じ行（`permissions`・`secrets`・`uses:`・env 化する `run:`）を変えている、または取り込みで衝突する（重なるファイルだけ外して残りを進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
* run-daily のドライランか、どれかの dry_run の手動実行が失敗し、直しで通せない（マージしない。失敗したジョブ・ステップ・推定原因を書く）
* 既存のテストが通らなくなり、直しで戻せない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* 止まらないこと: SHA が取れない（上の「範囲」のとおり外して進める）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、外したもの（重なり・SHA が取れなかった場合など）、`workflow_dispatch`／ドライランで確かめられなかったワークフローの一覧、Dependabot の PR のマージの仕方の案を書く
* 最終報告に、平野さんの手作業として次を書く: [mj の Actions の設定](https://github.com/retroeater/mj/settings/actions) の Workflow permissions を「Read repository contents and packages permissions」にし、「Allow GitHub Actions to create and approve pull requests」のチェックを外して Save。切り替えた後の確かめは翌朝の run-daily（mj-logs の morning.md）で見る旨も書く。全ワークフローに `permissions` の明示があることを確かめた結果（一覧）を添える
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-SEC-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-SEC-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認（2026-10-18 21:06 JST）

- 同じ Chat-Ref のコミット: なし（`git log --all --grep="CHAT-1018-SEC-04"`、unshallow の後）。識別子 SEC は同じチャットの系列（SEC-01〜09）で、この指示は最初の指示ではない
- `origin/work/1018-sec4`: リモートにもローカルにも無い → `git checkout -b work/1018-sec4 origin/cloudflare`（1cd1d6f2b）
- 指示欄の末尾が指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致することを確かめた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 時間帯: 着手 21:06 JST。止める時間帯（03:45〜05:15、月曜 05:00〜07:00）の外
- 文書のサイズ（バイト）: CLAUDE.md 23801・handover.md 23423・chat-side-operations.md 22155・instruction-template.md 14325（警告域 14336 の手前。今回は触らない）

### 1. 調べ

#### issue の状態

| issue | 状態 | 他セッションの着手中 |
|---|---|---|
| #598 | open（最終更新 10-18 10:57Z、SEC-07 の確認結果）。着手中のコメントを足した | なし |
| #564 | open（段⑤まで。段④は SEC-04 のマージ待ち） | なし |
| #557 | open（PRV-02 が 12:03Z に完了のコメント。読み取り用サービスアカウントの手順書。ワークフローは未変更） | PRV-02 は完了済み |
| #504 | open（10-14 以降の更新なし） | なし |

#### 未マージの work/ ブランチとの重なり（`.github/`・`workers/scheduler/`・`assets/houou_ask.js`）

| ブランチ | 変えている行 | 扱い |
|---|---|---|
| `work/1018-log-367-migrate` | `convert-x-posts.yml`・`fetch-x-posts.yml` の冒頭の注釈と `on.workflow_dispatch.inputs`（mode の説明と選択肢） | `permissions`・`secrets`・`uses:`・env 化する `run:` と同じ行ではない。触る行（`permissions:`・checkout）とは離れているので進める |
| `work/WRC` | `check-assets.yml` の79行（`allowed()` の case） | 同じ行ではない（触るのは `jobs.check` の `permissions` と checkout）。進める |
| `work/OUK`・`work/WRC` | `assets/houou_ask.js`（冒頭の注釈・`LEAGUE_ORDER`・facts・`fetch` の body） | 指示どおり (c) を外す |
| `workers/scheduler/` | どのブランチも変えていない | (a)(b) は進める |

#### zizmor（1.31.1、`--offline .github/`、直す前）

| 監査 | 重大度 | 件数 |
|---|---|---|
| unpinned-uses | High | 56 |
| self-repository | Low | 45 |
| artipacked | Medium | 25 |
| secrets-inherit | Medium | 11 |
| excessive-permissions | High | 4 |
| excessive-permissions | Medium | 2 |
| template-injection | High | 4 |
| template-injection | Informational | 6 |
| 合計 | | 153 |

#### SHA（`git ls-remote --tags https://github.com/<owner>/<repo>`。github.com に届いた。どれも軽量タグで `^{}` の行は無い）

| action | 今のタグが指すコミット | 同じコミットの版 |
|---|---|---|
| `actions/checkout@v4` | `11d5960a326750d5838078e36cf38b85af677262` | v4.4.0（`security-check.yml` と同じ） |
| `actions/setup-python@v5` | `a26af69be951a213d495a4c3e4e4022e16d87065` | v5.6.0（`security-check.yml` と同じ） |
| `actions/github-script@v7` | `f28e40c7f34bde8b3046d885e986cb6290c5673b` | v7.1.0 |
| `actions/cache@v4` | `0057852bfaa89a56745cba8c7296529d2fc39830` | v4.3.0 |
| `google-github-actions/auth@v2` | `c200f3691d83b41bf9bbd8638997a462592937ed` | v2.1.13 |

#### 入れ子

```
run-daily ─ live     → update-live-channel ─ update     （自分のジョブ）
           │                               ├ regenerate → regenerate-page
           │                               └ yotei      → sync-yotei
           ├ sns      → update-sns-book    ─ update / regenerate → regenerate-page
           ├ images   → check-image-links  ─ check / saikyo / sns_book
           ├ saikyo   → regenerate-saikyo  ─ abema / regenerate → regenerate-page
           ├ dojo     → sync-dojo-calendar ─ sync
           └ branches → cleanup-branches   ─ delete
fetch-youtube ─ fetch / regenerate → regenerate-page / report
```

#### job ごとの必要な権限と Secret（実物の使い方から）

| ワークフロー / job | 書き込みに使うもの | 必要な権限 | 使う Secret（入れ子の下を含む） |
|---|---|---|---|
| check-assets / check | なし（読むだけ） | contents: read | なし |
| check-build-failure / notify | `gh api` の読み（check-runs・compare） | checks: read, contents: read | なし |
| check-image-links / check・saikyo・sns_book | `upsert-issue`（github-script で issue の作成・更新・コメント） | contents: read, issues: write | X_BEARER_TOKEN（saikyo の `saikyo_resolve_test` だけ。手動実行のみで、run-daily からは使わない） |
| check-meibo / meibo・ampai | `upsert-issue` | contents: read, issues: write | なし |
| check-saikyo-unregistered / check | `upsert-issue` | contents: read, issues: write | なし |
| cleanup-branches / delete | `git push --force-with-lease … :refs/heads/<branch>`（checkout の認証） | contents: write | なし |
| cleanup-logs / cleanup | `commit-push`（git push）・`upsert-issue` | contents: write, issues: write | なし |
| convert-x-posts / convert | なし | contents: read | LIVE_SHEETS_SA_KEY・X_BEARER_TOKEN・ANTHROPIC_API_KEY・GOOGLE_PLACES_API_KEY |
| fetch-gsc / fetch | git push・`gh issue comment` | contents: write, issues: write | GSC_SERVICE_ACCOUNT_JSON |
| fetch-redirect-counts / fetch | `commit-push`・`upsert-issue`（comment） | contents: write, issues: write | CF_ANALYTICS_TOKEN |
| fetch-x-posts / fetch | なし | contents: read | LIVE_SHEETS_SA_KEY・X_BEARER_TOKEN |
| fetch-youtube / fetch | `commit-push` | contents: write | YOUTUBE_API_KEY |
| fetch-youtube / regenerate | （regenerate-page） | contents: write, issues: write | なし（下の注） |
| fetch-youtube / report | なし（checkout もしない） | なし | なし |
| regenerate-page / regenerate | git push・github-script（帰り道の知らせ: issue の一覧・作成・コメント） | contents: write, issues: write | RAKUTEN_APPLICATION_ID・RAKUTEN_ACCESS_KEY（`if: false` のステップだけ。今は使わない） |
| regenerate-saikyo / abema | なし | contents: read | LIVE_SHEETS_SA_KEY |
| regenerate-saikyo / regenerate | （regenerate-page） | contents: write, issues: write | なし |
| regenerate-sitemap / sync | git push | contents: write | なし |
| run-daily / live | （update-live-channel） | contents: write, issues: write, actions: read | YOUTUBE_API_KEY・LIVE_SHEETS_SA_KEY・GCP_SA_KEY（sync-yotei の分） |
| run-daily / sns | （update-sns-book） | contents: write, issues: write | LIVE_SHEETS_SA_KEY・X_BEARER_TOKEN |
| run-daily / images | （check-image-links） | contents: read, issues: write | なし |
| run-daily / saikyo | （regenerate-saikyo） | contents: write, issues: write | LIVE_SHEETS_SA_KEY |
| run-daily / dojo | （sync-dojo-calendar） | contents: read, issues: write | ANTHROPIC_API_KEY・GCP_SA_KEY |
| run-daily / branches | （cleanup-branches） | contents: write | なし |
| security-check / check | なし | contents: read（SEC-02 で済） | なし |
| sync-birthday-calendar / sync | なし | contents: read | GCP_SA_KEY |
| sync-dojo-calendar / sync | github-script（issue の作成・コメント）。`actions/cache` は GITHUB_TOKEN の権限を使わない | contents: read, issues: write | ANTHROPIC_API_KEY・GCP_SA_KEY |
| sync-yotei / sync | github-script（issue の作成・コメント） | contents: read, issues: write | GCP_SA_KEY・LIVE_SHEETS_SA_KEY |
| update-live-channel / update | git push・github-script（issue）・ゲートの `curl` で Actions の実行の一覧・`check_unused_names.py`（GITHUB_TOKEN で issue のコメントを読む） | contents: write, issues: write, actions: read | YOUTUBE_API_KEY・LIVE_SHEETS_SA_KEY |
| update-live-channel / regenerate | （regenerate-page） | contents: write, issues: write | なし |
| update-live-channel / yotei | （sync-yotei） | contents: read, issues: write | GCP_SA_KEY・LIVE_SHEETS_SA_KEY |
| update-sns-book / update | なし | contents: read | LIVE_SHEETS_SA_KEY・X_BEARER_TOKEN |
| update-sns-book / regenerate | （regenerate-page） | contents: write, issues: write | なし |
| sync-logs（触らない） | — | 既に `contents: read`・`actions: read` を明示 | — |

注:

- **regenerate-page の issues: write**: 帰り道の知らせ（github-script）は fetch-youtube からと直接の `all` のときだけ動くが、job の `permissions` は呼ぶ側の job を超えられない（超えると起動の時点で失敗する）。regenerate-page の job に `issues: write` を書くので、呼ぶ側の job（update-sns-book・regenerate-saikyo・update-live-channel の regenerate と、run-daily の sns・saikyo）にも `issues: write` を足す。呼ばれる側の他の job（update-sns-book の update、regenerate-saikyo の abema）は自分の `permissions` で絞るので広がらない
- 楽天の2つは `on.workflow_call.secrets` に任意（required: false）で宣言し、どこからも渡さない（ステップは `if: false`。再開するときに fetch-youtube から渡す。注釈に書く）
- check-image-links の X_BEARER_TOKEN も任意で宣言し、run-daily からは渡さない
- スクリプトが環境変数から読む Secret はワークフローの `env:` に書いたものだけ（`scripts/` の `os.environ`・`getenv` を grep）。暗に読むものは無い

#### `run:` に `${{ }}` を直接埋めている箇所（直す前）

| ファイル:行 | 埋めているもの | zizmor |
|---|---|---|
| check-build-failure.yml:47・66 | `github.event_name` | （出ない） |
| check-image-links.yml:106・231 | `steps.current.outputs.body-path` | Informational |
| check-meibo.yml:65・111 | `github.server_url`・`github.repository`・`github.run_id` | （出ない） |
| cleanup-branches.yml:62・70 | `github.event_name` | （出ない） |
| cleanup-logs.yml:72 | `steps.mode.outputs.dry-run` | Informational |
| fetch-gsc.yml:51・108・109 | `github.event_name`・`server_url` ほか・`github.ref_name` | High 1 |
| fetch-redirect-counts.yml:54・148 | `github.event_name`・`steps.apply.outputs.apply` | Informational |
| regenerate-page.yml:271 | `server_url` ほか | （出ない） |
| regenerate-sitemap.yml:52・53・60・73・97 | `github.event.before`・`event_name`・`sha`・`ref_name`・`server_url` ほか | High 1 |
| sync-birthday-calendar.yml:92 | `steps.mode.outputs.apply` | Informational |
| sync-dojo-calendar.yml:149 | `inputs.apply` | （出ない） |
| update-live-channel.yml:190・259・262・263・381 | `inputs.verify`・`server_url` ほか・`github.ref_name`・`steps.gate.outputs.verify` | High 2・Informational 1 |

直し方: `github.*` は既定の環境変数（`GITHUB_EVENT_NAME`・`GITHUB_SERVER_URL`・`GITHUB_REPOSITORY`・`GITHUB_RUN_ID`・`GITHUB_REF_NAME`・`GITHUB_SHA`。`workflow_call` でも `github.*` と同じく呼ぶ側の値）を使い、`github.event.before`・`inputs.*`・`steps.*` はステップの `env:` で受ける。

## 報告

- 状態: 作業中
- ブランチ: work/1018-sec4
- ログ: https://github.com/retroeater/mj/blob/work/1018-sec4/docs/logs/CHAT-1018-SEC-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-sec4
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1cd1d6f2）: https://github.com/retroeater/mj-logs/tree/main/guide/1cd1d6f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1cd1d6f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
