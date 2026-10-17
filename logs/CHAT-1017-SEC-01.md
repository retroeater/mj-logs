# CHAT-1017-SEC-01

- 着手日時: 2026-10-17
- 対象issue: #9（コメントのみ）、親 issue（起票予定）
- ブランチ: work/1017-sec
- 着手時HEAD: fb3a43e94

## 指示

【Claude作成】Claude Code 向け指示：ryoei.pro のセキュリティ点検（調査のみ。固有の攻撃面の点検・道具の試用・Security Guidance プラグインの動作確認・親 issue の起票） Chat-Ref: CHAT-1017-SEC-01 マージ: ドキュメントのみ（ログ・docs/notes・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉。コード・生成物・`.github/workflows/`・`_headers`・Worker は変えない 貼る時機: 平野さんが claude.ai のプラグイン一覧で「Security Guidance」（Anthropic Directory）を有効にした後。新しいクラウドセッション: 必須（プラグインはセッションの開始時に読み込まれるため。手順1の確認が目的） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-sec の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-sec を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-sec origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 「貼る時機」の前提（プラグインの有効化）は手順1で確かめるが、満たされていなくても止まらず、結果を報告して手順2以降を進める（点検はプラグインに依存しない）。

目的
ryoei.pro（静的配信＋ Worker `mj-ask`・`mj-scheduler` ＋ Secret を持つ GitHub Actions）の脆弱性と設定の穴を、ryoei.pro 固有の攻撃面に沿って一度洗い出し、重大度を付けて親 issue に一覧にする。直すのは別の指示（SEC-02 以降）で行う。あわせて、Anthropic 公式の Security Guidance プラグインがクラウドセッションで動くかを確かめ、今後のセキュリティ検査の方法を `docs/notes/security.md` に残す。
決定（2026-10-17、平野さん）

* サイトのセキュリティ・脆弱性診断と対策を実施する
* Anthropic 公式の Security Guidance プラグインを導入する。まず claude.ai のプラグイン一覧で有効にし、クラウドセッションで動くかを確かめる（動かなければ対応は別途チャットで判断）
* 最初の一手は、ryoei.pro 固有の攻撃面の点検（この指示）。深い診断（Claude Security プラグイン）はプラグインの動作確認の結果を見てから判断する
* CSP（#9）は今回のスコープに入れない。#9 の後にセキュリティ点検を再度行う
* コミュニティ製のセキュリティ系プラグイン（securitymaxxing・OWASP Security Audit・Dependency Sentinel 等）は入れない（公式2つで足りる）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 公式文書（code.claude.com/docs/en/cloud-environments「What carries over from your setup」）では、クラウドセッションはユーザー設定・リポジトリの `.claude/settings.json` の `enabledPlugins` を読み込まない。claude.ai のプラグイン一覧で有効にしたものがクラウドセッションに届くかは文書に無く、手順1で確かめる（要確認）。Security Guidance は hook（SessionStart・UserPromptSubmit・PostToolUse・Stop）だけで作られ、動いていれば `~/.claude/security/`（venv と `log.txt`）ができる（公式文書 code.claude.com/docs/en/security-guidance「Troubleshooting」）
* CI の検査ワークフロー（Actions の設定の検査・Python 依存の CVE・秘密の混入の検査）の追加は SEC-02 に分けた。理由: Actions の整理（WFL 系列、#564）が `.github/workflows/` を変えている最中であることと、この指示で試した道具の結果を見て入れるものを選ぶため。この指示では道具をセッション内で1回動かすだけにする
* 試す道具（pip で入らなければその道具は飛ばして「未確認の項目」に書く。止まらない）: `zizmor`（GitHub Actions のワークフローの設定の検査）、`pip-audit`（`requirements*.txt` の CVE。無ければ `pip freeze` の結果を対象にする）、`detect-secrets`（秘密の混入。履歴は `git log -p` を grep で補う）。Go 製の gitleaks・trufflehog はバイナリの取得が通らない見込み（要確認）なので試さない
* Mozilla HTTP Observatory の API（`https://observatory-api.mdn.mozilla.net/api/v2/scan?host=ryoei.pro`、POST）に到達できれば採点を取る。到達できなければ「未確認の項目」に書く（平野さんが https://developer.mozilla.org/en-US/observatory/analyze?host=ryoei.pro を開いて確かめる）。`ryoei.pro` へのアクセスは User-Agent を必ず指定する（docs/notes/session-network.md）
* `/api/ask` に実際の質問は送らない（Claude API の費用と1日の上限を使う）。`/api/ask/status` の GET と、本文の無い・`Origin` の違う POST が 403／400 で返ることの確認だけにする（各1回）。Worker の検査は `node --test 'workers/ask/test/*.test.mjs'` とソースの読みで行う
* 平野さんのダッシュボード側の確認項目（セッションからは見えない）は、点検せず親 issue の「平野さんの確認」の節に列挙する: GitHub（2FA、`mj-scheduler` の `GITHUB_TOKEN` が fine-grained で `actions: write` だけか・期限、Secret scanning／Push protection の有効化の可否〈個人の private リポジトリで使えるかは要確認〉、Dependabot alerts の有無）、Cloudflare（API トークンの権限、`/api/ask` への Rate Limiting ルールの有無、Workers の Secret の一覧、2FA）、Google（サービスアカウントの鍵の数と作成日、各ブックへの権限が「閲覧者」で足りるのに「編集者」になっていないか）
* `docs/decisions/` の分野ファイルは `security.md` を新設する案（README の規則に従う。既存の `cloudflare.md` の Security Overview の見送り〈security.txt など〉は動かさない）
* 親 issue のラベルは `分野: セキュリティ`（handover.md 5章）。重大度は 高（外から到達でき、費用・改ざん・情報漏えいにつながる）／中（前提が重なれば到達する、または防御の層が1つしか無い）／低（衛生・推奨設定）の3段

手順

1. プラグインの動作確認: 同じ論点の open issue（「セキュリティ」「security」「脆弱」「CSP」「zizmor」「pip-audit」、クローズ済みも）を検索し、一覧をログに書く（#9 はあるはず。重なる論点があれば止まらず、親 issue から参照する）。次を調べて結果（有無と先頭数行）をログの `## 経過` に書く: `~/.claude/security/` と `log.txt` の有無、`~/.claude/settings.json`・`~/.claude/plugins/` の有無と `enabledPlugins` の中身、`claude plugin list`（コマンドが無ければ無いと書く）、このセッションに登録されている hook（分かる手段があれば）。手順2で実際にファイルを書いたとき（ログの push）に hook の警告や再プロンプトが出たかも書く
2. 固有の攻撃面の点検（読む・テストを動かす・道具を1回動かすだけ。直さない）。見つけたことは「どこ（ファイル:行）・何が・どう悪用されるか・重大度・直し方の案・直す規模」の表にする: (a) Worker `mj-ask`（`workers/ask/src/*.mjs`、`assets/houou_ask.js`、`workers/ask/wrangler.jsonc`、`schema.sql`）: 入力の検査（長さ・型・文字種）、`Origin` 検査が curl で偽装できる前提での費用の守り（全体 200・IP 10 のほかに何があるか、IP は `CF-Connecting-IP` か）、質問文とシート由来のデータによるプロンプトインジェクション（system と user の分け方、データの埋め方、答えの検証）、答えの描画（`innerHTML` か `textContent` か。選手名・注記・URL の扱い）、D1 の問い合わせの引数化、エラー応答と `status` が返す情報、CORS・レスポンスヘッダ（`_headers` は Worker の応答に効かない見込み。要確認）、Workers Logs に書く内容。`node --test 'workers/ask/test/*.test.mjs'` を回す。本番は `/api/ask/status` の GET 1回と、`Origin: https://example.invalid` の空 POST 1回だけ叩き、状態コードとヘッダを記録する (b) 生成スクリプト（XSS）（`scripts/*.py`・`scripts/lib/*.py`）: スプレッドシートの値（選手名・注記・URL・SNS ID）を HTML に埋める箇所で `html.escape` 相当が無いもの、`href`／`src` に入る URL の scheme の検査（`javascript:`・`data:`）、`<script>` に JSON を埋める箇所の `</script>`・`<!--` のエスケープ、属性値の引用符。grep（f-string・`format`・`+` 連結で `<` を含むもの）で候補を出し、1つずつ読む (c) フロントの JS（`assets/*.js`・ルート直下の `*.js`、`assets/vendor/` を除く）: `innerHTML`・`insertAdjacentHTML`・`document.write`・`eval`・`new Function` の使用箇所と、そこに入る値の出所（生成 JSON・localStorage・URL のクエリ）。`assets/vendor/` はライブラリ名と版を一覧にし、既知の CVE を pip-audit とは別に OSV（`https://api.osv.dev/v1/query`、到達できれば）で照合する (d) GitHub Actions（`.github/workflows/*.yml`、`.github/dependabot.yml` の有無）: `zizmor` を1回動かし結果を全部ログに写す。加えて、第三者 action の SHA 固定の有無、`pull_request_target`・`workflow_run` の使用、`permissions` の粒度、Secret がログに出る経路（`echo`・`set -x`・失敗時の dump）、`secrets: inherit` の範囲、`workflow_dispatch` の入力をシェルに直接埋めている箇所（インジェクション） (e) 公開されるファイル: `.assetsignore` と `wrangler.jsonc` を読み、リポジトリ内で配信対象になるファイルのうち公開してはいけないもの（`docs/`・`scripts/`・`workers/`・テスト・鍵・`.env`・ログ）が無いかを一覧で確かめ、本番で `https://ryoei.pro/docs/handover.md`・`/scripts/regenerate.py`・`/workers/ask/wrangler.jsonc`・`/.assetsignore`・`/wrangler.jsonc` が 404 であることを確かめる（各1回） (f) 秘密の混入: `detect-secrets scan` を作業ツリーに1回、`git log -p --all | grep -E` で鍵の形（`AIza`・`sk-ant-`・`AKIA`・`-----BEGIN PRIVATE KEY`・`ghp_`・`github_pat_`・`AAAA…` の Bearer）を履歴に1回。見つかったら値は書かず、コミットの SHA とファイル名だけログに書く (g) 配信のヘッダ: `_headers` の5件の内容と、本番の `/`・`/houou/`・`/api/ask/status` の応答ヘッダ（各1回）を表にし、Observatory の採点（到達できれば）を記録する。CSP は #9 の対象なので「無い」と書くだけで案は書かない (h) 依存: `pip-audit` を `requirements*.txt`（無ければ `pip freeze`）に1回。Worker は npm の依存が無いことを確かめる
3. 文書と issue: 親 issue「ryoei.pro のセキュリティ点検と対策（2026-10）」を起票し（ラベル `分野: セキュリティ`、本文は (a)〜(h) の結果の表を重大度順に、「平野さんの確認」の節〈前提のダッシュボード側の項目〉、「次の指示（SEC-02）の候補」の節〈CI の検査ワークフローの構成の案: 月1回＋手動、Actions の予算 $10／月〈#298〉の範囲で見込みの所要時間を書く。WFL〈#564〉の段④の後に入れる前提〉、「#9 の後に再点検」の1行）。#9 に「CSP は SEC 系列のスコープ外、#9 の後に再点検」とコメントする。`docs/notes/security.md` を新設し、点検の観点（(a)〜(h)）・使った道具と入れ方・到達できた外部 API・次回の手順を書く（結果の表は issue に置き、notes には書かない）。`docs/decisions/security.md` を新設して上の「決定」を書き、`docs/decisions/README.md` の表に1行足す。handover.md 6章の表に親 issue の1行を足す

止まる条件

* 0章の確認（指示文の途中切れ）
* 作業ツリーかリポジトリの履歴に有効と思われる鍵が見つかった（値を書かずに SHA とファイル名だけ報告して止まる。無効化は平野さんの手作業）
* 本番の (e) の URL のいずれかが 200 を返した（公開されている。何が見えるかを書いて止まる。直すのは別の指示）
* 未マージの work/ ブランチが `docs/notes/security.md`・`docs/decisions/security.md`・`docs/decisions/README.md` の表・handover.md 6章の同じ行を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* 止まらないこと: プラグインが動いていない、道具が pip で入らない、Observatory・OSV に到達できない（いずれも「未確認の項目」に書いて進む）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、重大度 高・中の一覧（件名と issue 内の番号）、プラグインの動作確認の結論（動いた／動かなかった／判別できなかった、とその根拠）、SEC-02 に入れる道具の推奨とその理由を書く
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみ。完了報告のうえ cloudflare へ入れてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-SEC-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-SEC-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 識別子 `SEC`: `git log --all -E --grep 'CHAT-[0-9]{4}-SEC-'` と `docs/logs/CHAT-*-SEC-*.md` の履歴とも0件（`--unshallow` の後）
- 作業ブランチ: ローカル・リモートとも `work/1017-sec` が無く、`git checkout -b work/1017-sec origin/cloudflare`（fb3a43e94）で作成
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 未マージの work/ ブランチ: 1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-lvp・1018-ogr・OUK（触るファイルとの重なりは手順3の前に見る）

### 1. プラグインの動作確認

関係する issue（タイトル・本文のキーワード一致。REST の `/search/issues` はセッションから使えず〈「sessions are bound to their configured repositories」〉、`/repos/retroeater/mj/issues?state=all` 全596件を取得して grep）:

| issue | 状態 | 件名 | 親 issue での扱い |
|---|---|---|---|
| #9 | open | CSP(Content-Security-Policy)を設定する | スコープ外（再点検の起点） |
| #300 | open | 秘密のコミットを検知する仕組みを入れる | (f) と SEC-02 が重なる。参照する |
| #543 | open | データ・コンシェルジュに Turnstile（ボット判定）を入れるか検討する | (a) の費用の守りで参照 |
| #554 | open | AI クローラーへの 403 の原因を調べる | 関係薄（WAF 側） |
| #555 | open | 月次チェック（#304）の取得を自動化する | 関係薄 |
| #304 | open | 月次運用チェックリスト | 「平野さんの確認」の置き場の候補 |
| #278 | open | 埋め込み用の選手カード（分野: セキュリティ） | 関係薄 |
| #564 | open | GitHub Actions の整理（WFL） | (d)・SEC-02 の前提 |
| #36・#110・#124・#132・#299・#116・#91・#120・#130・#447 | closed | セキュリティヘッダ・メソッド遮断・Rate Limiting rules・workflow_dispatch 入力の直展開・公開期間の露出評価・SPF/DMARC 等 | 必要に応じて参照 |

`zizmor`・`pip-audit`・`Dependabot`・`プロンプトインジェクション`・`innerHTML` は0件。

プラグインの状態（2026-10-17 17:0x、このセッション）:

- `~/.claude/security/` あり。`log.txt`（658バイト）と `security_warnings_state_cse_<セッションID>.json` がある。venv は無い（`~/.claude/plugins/data/security-guidance-synced/` も空）
- `log.txt` の先頭:
  ```
  [2026-10-17 16:59:23.153] Hook called with args: ['/root/.claude/plugins/synced/<org>_<…>/30660c6c-…/hooks/security_reminder_hook.py']
  [2026-10-17 16:59:23.155] Processing: hook_event=UserPromptSubmit, tool=
  [2026-10-17 16:59:23.234] Captured git baseline: fb3a43e9487e
  [2026-10-17 17:00:40.108] Processing: hook_event=PostToolUse, tool=Write
  [2026-10-17 17:00:46.483] Processing: hook_event=PostToolUse, tool=Bash
  [2026-10-17 17:00:46.489] Commit review: detected git commit in command
  [2026-10-17 17:00:46.496] Commit review: LLM review disabled or no API credentials
  ```
- `~/.claude/settings.json` は無い。`~/.claude/launcher-settings.json` に Stop hook（`stop-hook-git-check.sh`）だけ。`enabledPlugins` はどこにも無い
- `~/.claude/plugins/synced/<org>_<…>/` に claude.ai から同期されたプラグインがある（`*.meta.json` の `marketplace_name` は `anthropic-plugin-directory`）
- `claude plugin list` は「Synced from claude.ai:」として engineering・searchfit-seo・design・mobbin・product-management・**security-guidance@synced（Version 2.0.13、Status: √ loaded）** を表示
- 登録の hook（`hooks/hooks.json`）: SessionStart（`ensure_agent_sdk.py`）、UserPromptSubmit、PostToolUse（`Edit|Write|MultiEdit|NotebookEdit` と、Bash の `git commit`・`git push` 等で非同期の再起動つきレビュー）、Stop（非同期のレビュー）
- ログの push 時（Write と git commit/push）: hook は呼ばれたが、警告・再プロンプトは出なかった（`shown_warnings: []`）。コミットの LLM レビューは「LLM review disabled or no API credentials」で動かなかった。環境に `ANTHROPIC_API_KEY`・`ANTHROPIC_AUTH_TOKEN` が無い（`llm.py` は どちらかか 3P プロバイダを要する）
- 結論（暫定）: **claude.ai のプラグイン一覧で有効にしたものはクラウドセッションに同期され、hook のパターン照合の部分は動く。LLM によるコミット・停止時のレビューは鍵が無く動かない**

## 報告

- 状態: 作業中
- ブランチ: work/1017-sec
- ログ: https://github.com/retroeater/mj/blob/work/1017-sec/docs/logs/CHAT-1017-SEC-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-sec
- 確認用URL: なし
- マージ: 未
- issue: 未定
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e2580c91）: https://github.com/retroeater/mj-logs/tree/main/guide/e2580c91

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2580c91/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e2580c91.md
