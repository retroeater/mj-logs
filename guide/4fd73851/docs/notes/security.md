# セキュリティ点検の方法

ryoei.pro のセキュリティ点検の観点・道具・手順。**結果（見つかったことの表）は issue に置き、ここには書かない**（初回は #598）。
CSP は #9 の対象で、#9 の後に点検を再度行う。決定は `docs/decisions/security.md`。

## 点検の観点

ryoei.pro 固有の攻撃面を8つに分けて見る。

| | 対象 | 見ること |
|---|---|---|
| (a) | Worker `mj-ask`（`workers/ask/`、`assets/houou_ask.js`） | 入力の検査、`Origin` が偽装できる前提での費用の守り（回数の上限・IP の取り方）、プロンプトインジェクション（system と user の分け方、検索条件の型の固定、答えの検証）、答えの描画（`textContent` か）、D1 の引数化、エラー応答と `/api/ask/status` が返す情報、レスポンスヘッダ（`_headers` は Worker の応答に効かない）、Workers Logs に書く内容。`mj-scheduler` はトークンの使い道 |
| (b) | 生成スクリプト（`scripts/*.py`・`scripts/lib/*.py`） | シートの値を HTML に入れる箇所の `esc()`、`href` に入る URL の scheme（`javascript:`）、`<script>` に入れる JSON の `<` の逃がし方、属性の引用符 |
| (c) | フロントの JS（`assets/*.js`・ルート直下の `*.js`。`assets/vendor/` を除く） | `innerHTML`・`insertAdjacentHTML`・`document.write`・`eval`・`new Function` と、そこに入る値の出所（生成 JSON・localStorage・URL のクエリ）。`assets/vendor/` は名前と版を一覧にして既知の脆弱性を照合 |
| (d) | GitHub Actions（`.github/workflows/`・`.github/actions/`） | zizmor の結果（2026-10-18 に直した後は 58 件。残りは self-repository 45・push する job の artipacked 8・`sync-logs.yml` の 5）。今の形から外れていないか: 第三者 action が SHA で固定され `dependabot.yml` の対象か、トップレベルが `permissions: {}` で job ごとに最小か、`secrets: inherit` が無く Secret を明示して渡しているか、`run:` に `${{ }}` を直接書いていないか、push しない checkout が `persist-credentials: false` か（形は `docs/notes/github-actions.md`「権限と Secret の渡し方」）。ほかに `pull_request_target`・`workflow_run`、Secret がログに出る経路、Settings の Workflow permissions の既定が読み取りだけか |
| (e) | 公開されるファイル | `.assetsignore` と `wrangler.jsonc`（`assets.directory: "./"`）から配信対象を一覧にし、`docs/`・`scripts/`・`workers/`・テスト・鍵・`.env`・ログが無いか。本番で代表の URL が 404 か |
| (f) | 秘密の混入 | 作業ツリー（detect-secrets）と全履歴（`git log -p --all` の正規表現）。見つけたら値は書かず SHA とファイル名だけ |
| (g) | 配信のヘッダ | `_headers` の内容と、本番の `/`・`/houou/`・`/api/ask/status` の応答ヘッダ |
| (h) | 依存 | CI の Python の依存（pip-audit）。Worker に npm の依存が無いこと |

重大度は 高（外から到達でき、費用・改ざん・情報漏えいにつながる）／中（前提が重なれば到達する、または防御の層が1つしか無い）／低（衛生・推奨設定）の3段。
ダッシュボード側（GitHub・Cloudflare・Google・Anthropic の設定）はセッションから見えないので、点検せず親 issue の「平野さんの確認」に並べる。

## ダッシュボード側の確認（年1回か大きな変更の後）

平野さんが画面で確かめる観点。結果は親 issue に書き、名前・値・権限の詳細は private の issue（認証情報の一覧は #601）に置く（この文書は public の mj-logs に写る）。

| | どこ | 見ること |
|---|---|---|
| 1 | GitHub のアカウント | 2FA が認証アプリなど SMS 以外か、リカバリーコードを控えているか |
| 2 | GitHub のトークン | fine-grained か、対象のリポジトリ・権限が使う分だけか、期限があるか、使っていないものが無いか |
| 3 | GitHub の Code security | Secret scanning／Push protection が使えるか（2026-10 時点で個人の private リポジトリでは使えない） |
| 4 | GitHub の Dependabot | alerts が On か |
| 5 | GitHub の Actions の設定 | Workflow permissions の既定が読み取りだけか、PR の作成・承認の許可が外れているか |
| 6 | Cloudflare の API トークン | 一覧に不要なもの・権限の広すぎるものが無いか、Global API Key を使っていないか |
| 7 | Cloudflare の Rate limiting rules | `/api/ask` と HTML の取得のルールが Active か（`docs/notes/cloudflare.md`） |
| 8 | Cloudflare の Workers の Secret | 鍵が Variable に入っていないか、使っていない Secret が無いか |
| 9 | Cloudflare のアカウント | 2FA が認証アプリか、バックアップコードを控えているか |
| 10 | Google のサービスアカウント | 鍵の数（使っていない鍵が無いか） |
| 11 | Google のブックの共有 | サービスアカウントの権限が使う分だけか（読むだけなら閲覧者） |
| 12 | Anthropic | 組織とワークスペースの使用額の上限、キーの期限、使っていないキーが無いか |

## 道具と入れ方（クラウドセッション、2026-10-17 に実測）

**zizmor・pip-audit・履歴の走査は GitHub Actions の `security-check.yml`（手動実行のみ）にまとめてある**（#598。月1回の起動は #564 の段④の後）。
cloudflare で「Run workflow」（https://github.com/retroeater/mj/actions/workflows/security-check.yml ）を押すか、セッションから `actions_run_trigger` で起動し、Job summary を読む。
zizmor と pip-audit は見つかっても失敗にしない。履歴の走査は `data/security/known-history-hits.txt` に無い当たりがあれば失敗にする（有効な鍵でないと確かめたものだけを、理由を添えて一覧に足す）。

セッションの中で動かすときは、scratchpad に venv を作って入れる（pypi.org に届く。数十秒）。

```
python3 -m venv <scratchpad>/venv
<scratchpad>/venv/bin/pip install zizmor pip-audit detect-secrets
```

| 道具 | 使い方 | 所要 | 注意 |
|---|---|---|---|
| zizmor（1.31.1） | `zizmor --offline .github/workflows` と `.github/actions`。一覧にするときは `--format json` | 1 秒未満 | `--offline` でもワークフローの検査は全部動く（オンラインの監査は action の既知の脆弱性など） |
| pip-audit（2.10.1） | `requirements*.txt` が無いので、`python3 scripts/ci_requirements.py > <scratchpad>/req.txt`（ワークフローの `packages:`・`pip install` の行から集める）を `pip-audit -r` に渡す | 約 10 秒 | 照会先は既定の PyPI（届く）。組の正はワークフローの各行で、一覧は持たない |
| detect-secrets（1.5.0） | `detect-secrets scan --all-files --exclude-files '^\.git/'` | 約 1 分半 | 生成ページの Cloudflare Web Analytics のトークン（公開値）で Hex High Entropy が数千件出る。種類別に数え、Hex 以外を1件ずつ見る |
| 履歴の走査 | `python3 scripts/scan_secret_history.py`。`git log -p --all` の追加行を正規表現（`AIza`・`sk-ant-`・`AKIA`・`-----BEGIN … PRIVATE KEY`・`ghp_`・`github_pat_`・`Bearer AAAA`）で読み、種類・SHA・ファイル名だけを出す（値は出さない） | 約 25 秒 | 先に `git fetch --unshallow origin`（浅いと終了コード2で止まる、#299） |
| npm の照会口 | `https://registry.npmjs.org/-/npm/v1/security/advisories/bulk` に `{"<名前>": ["<版>"]}` を POST | 数秒 | OSV の代わり。元は GitHub Advisory Database |
| Worker のテスト | `node --test 'workers/ask/test/*.test.mjs'`・`'workers/scheduler/test/*.test.mjs'` | 1 秒未満 | |

試していないもの: gitleaks・trufflehog（Go のバイナリの取得が要る）。

## 届いた外部 API（2026-10-17）

- 届いた: `pypi.org`（pip・pip-audit）、`registry.npmjs.org`、`ryoei.pro`（User-Agent を付ける。docs/notes/session-network.md）
- 届かない（プロキシが `CONNECT tunnel failed, response 403`）: `api.osv.dev`、`observatory-api.mdn.mozilla.net`。Observatory は平野さんがブラウザで https://developer.mozilla.org/en-US/observatory/analyze?host=ryoei.pro を開いて確かめる
- `api.github.com` は `/repos/retroeater/mj/...` だけ。`/search/issues` は使えない（issue の検索は `/repos/retroeater/mj/issues?state=all` を全件取って grep）

## 本番に送ってよい要求

`/api/ask` に実際の質問は送らない（Claude API の費用と1日の上限を使う）。確かめるのは次だけ（各1回）。

- `GET /api/ask/status`（状態コードとヘッダ）
- `POST /api/ask` を `Origin: https://example.invalid`・本文なしで（403 が返る）

## Security Guidance プラグイン（Anthropic 公式）

claude.ai のプラグイン一覧で有効にすると、クラウドセッションの開始時に `~/.claude/plugins/synced/` へ同期され、`claude plugin list` に `security-guidance@synced … √ loaded` と出る（2026-10-17、`.claude/settings.json` の `enabledPlugins` は使っていない）。

- hook（SessionStart・UserPromptSubmit・PostToolUse・Stop）が動き、`~/.claude/security/log.txt` に記録が残る
- 編集のパターン照合は鍵なしで動く（2026-10-18、`.github/workflows/` のファイルを Write したときに Actions のインジェクションの注意が PostToolUse の追加の文脈として出た）。コミット・停止時の LLM のレビューは `ANTHROPIC_API_KEY` などが無いため動かない（`log.txt` に「LLM review disabled or no API credentials」）
- 動いているかの確かめ方: `ls ~/.claude/security/`、`cat ~/.claude/security/log.txt`、`claude plugin list`

## Claude Security プラグイン（Anthropic 公式、深い診断）

多エージェントで inventory → 脅威モデル → 部品×分類ごとの調査 → 広域の走査・秘密の走査 → 3票の検証パネル（2/3）を行う。コードを読むだけで、実行・外部への要求はしない（2026-10-18 の実行で確認）。

- 入れ方: claude.ai のプラグイン一覧で有効にする。**有効にした後に始めたセッション**でだけ `claude plugin list` に `claude-security@synced … √ loaded` と出る（前から開いていたセッションには同期されない）。Python 3.9 以上が要る
- 使い方: skill `claude-security` は `disable-model-invocation: true` で、指示文からは呼べない。平野さんが `/claude-security` を打つか、Claude Code が skill の手順書（プラグインの `skills/claude-security/jobs/scan-codebase.md`）を読んで、その手順どおりに `write_scan_meta.py` → Workflow `claude-security:scan` → `save_result.py` → `render_report.py` を自分で進める（2026-10-18 は後者）。時間とトークンを使うことへの承認が要り、指示文に費用の受け入れがあればそれで足りる
- 対象の絞り方: リポジトリ全体（5千ファイル超、大半が生成物）は選ばない。`--scope` にディレクトリ・ファイルをカンマ区切りで渡す（例: `workers,scripts/lib,assets/houou_ask.js` で 56 ファイル）。大きいリポジトリでは focus `attack-surface` になり、テスト・生成物は参照扱いになる
- 結果の置き場所: 結果のディレクトリ（`CLAUDE-SECURITY-<時刻>/`）は作業ツリーの外（scratchpad）に作れる。`render_report.py` に `--products-dir` で渡す。中に `.gitignore`（`*`）が付く
- 所要（2026-10-18、medium、56 ファイル）: 約 12 分、エージェント 15、サブエージェントのトークン約 109 万（Max の使用量から出る）。指摘 0 件、`verification.status: verified`
- 注意: 「Suggest patches」は指摘を見てからチャットで決める。結果に秘密の値が出たら値を写さない。結果は毎回同じとは限らない（非決定的）
- 回し方（2026-10-18 の決定）: 定期では回さない。`workers/` を大きく変えたときに差分だけの診断（Scan changes）を回し、#9（CSP）の後の再点検で1回回す。そのときの使用量を見て、今後の間隔を決め直す

## 次回の手順

1. `git fetch --unshallow origin` のうえ、上の道具を scratchpad に入れる
2. (a)〜(h) を順に見る（(b)・(c) は量が多いので、読むだけのサブエージェントに分け、主な指摘は該当行を自分で開いて確かめる）
3. 前回の親 issue の表と比べ、直ったもの・新しいものを分けて、新しい親 issue（または前回の issue へのコメント）にする
4. (d)・(f)・(h) は `security-check.yml` の実行結果（Job summary とジョブのログ）を起点にし、前回の件数と比べる
5. 「ダッシュボード側の確認」の 3 で、Secret scanning／Push protection が個人の private リポジトリで使えるようになっていないかを確かめる（使えるなら有効にし、`security-check.yml` の履歴の走査との分担を決める。#300 はクローズ済み）
