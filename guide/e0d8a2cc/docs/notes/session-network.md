# セッション環境の実測と手順

外部ドメインへの到達、gh の認証、シートの行番号の取り方、作業ファイルの置き場所、Codespace の Rebuild をまとめる。

外部ドメインへの到達可否は環境側（codespace・ネットワークポリシー）の設定で変わりうる。
記述と食い違ったら再測定すること。条件の確認は #328、check-run と本番反映の対応は #334。

## Rebuild と Claude Code（#212、2026-09-14 に Rebuild 2回で検証済み）

handover.md 3章「役割分担」から移した。

- `.devcontainer/` の `postCreateCommand`（`post-create.sh`）が Rebuild 後に Claude Code を自動インストールする。手動インストールは不要
- インストールは公式ネイティブインストーラ（`curl -fsSL https://claude.ai/install.sh | bash`）。npm 経由は anthropics/claude-code の README で deprecated とされている
- 設定は `CLAUDE_CONFIG_DIR=/workspaces/.claude-config`。`/workspaces` 配下はリポジトリ外かつ Rebuild を越えて残るため、ログイン状態も維持される
- `post-create.sh` は失敗しても起動を止めない。`claude` が見つからないときは Codespaces の creation log に `[post-create]` の失敗行が出ていないか確認する
- `.devcontainer/` の変更を既存の codespace に Rebuild で反映するには `/workspaces/mj` の作業ツリーを最新にする必要がある（読まれるのは作業ツリーの内容）。反映の手段は次の2つ（2026-09-14決定、pull の条件は 2026-09-15 に変更）
  - **`/workspaces/mj` を pull してから Rebuild する**（`git -C /workspaces/mj pull --ff-only`。セッションは CLAUDE.md「ブランチ運用」の3条件を満たすときは自分の判断で実行してよい。満たさないときは平野さんが確認する）。`/workspaces` 配下は Rebuild を越えて残るため、こちらのほうが軽い
  - **codespace を作り直す**。環境ごと作り直す必要がある場合の手段。作り直すと `/workspaces` 配下の未コミットの変更と worktree はすべて消えるため、他セッションの worktree が残っていないことを確認してから行う

## 2026-09-14（CHAT-0914-WF-03）

codespace 上の Claude Code セッションから `curl -sS -m 15` で実測。プロキシ環境変数（HTTP_PROXY 等）なし。

| ドメイン | 結果 |
|---|---|
| `docs.google.com` | トップ 302。`python3 scripts/regenerate.py wayhome_episodes` でシート取得も成功（exit=0） |
| `www.gstatic.com` | トップ 404、`/charts/loader.js` 200 |
| `ron2.jp` | トップ・画像とも 200 |
| `ryoei.pro` | トップ 200（`<title>ryoei.pro`） |
| `api.cloudflare.com` | トップ 301、`/client/v4/user/tokens/verify` 400（`Missing "Authorization" header`） |
| `api.github.com` | トップ 200 |

同日、別セッション（CHAT-0914-W2-04）でも `ryoei.pro` 200・`api.cloudflare.com/client/v4/` 400 を観測している（#328）。

## 2026-09-14 追加分（CHAT-0914-WF-05）

条件は上と同じ（codespace、`curl -sS -m 15`、プロキシ環境変数なし）。

| ドメイン | 結果 |
|---|---|
| `pbs.twimg.com` | トップ 400。`jpml_pros.html` 内の画像URL 842件はすべて 200 |
| `img.youtube.com` | トップ 404、`/vi/<動画ID>/mqdefault.jpg` 200 |
| `abs.twimg.com` | トップ 400、`/sticky/default_profile_images/default_profile_200x200.png` 200 |
| `hayabusa.io` | 名前解決できない（`curl: (6) Could not resolve host`）。`dns.google` の DoH でも SERVFAIL（lame delegation）で、セッション環境固有の遮断ではない。サイトでの使用は 2026-09-10 に削除済み（`docs/notes/site-findings.md`） |

## 2026-09-19 追加分（CHAT-0918-TP-06）

codespace、`curl -sS -m 20 -A 'Mozilla/5.0'`、プロキシ環境変数なし。

| ドメイン | 結果 |
|---|---|
| `www.ma-jan.or.jp`（連盟公式HP） | トップ 200、`/title-fight.html` と大会ごとの `/title-fight/<名前>.html` 200（歴代優勝者の表を取得できた） |

---

以下の3節は、削除した旧ノート（Codespace 内のセッション環境の記録。CHAT-0914-GH-01 / SK-05 / DOC-01）から
この文書に移したもの（CHAT-0914-SK-17）。

## gh の認証（2026-09-14）

### 現在の設定

- Codespaces のユーザーシークレット `GH_TOKEN` に classic PAT
  （scopes: `repo`, `workflow`, `read:org`, `project`）。
  API 応答に `github-authentication-token-expiration` ヘッダが無く、有効期限は無い
- gh の優先順位は `GH_TOKEN` → `GITHUB_TOKEN` → `~/.config/gh/hosts.yml`。
  Codespace 既定の `GITHUB_TOKEN` も引き続き設定されているが、gh からは使われない
- git の fetch / push は `/etc/gitconfig` の `credential.helper`
  （`/.codespaces/bin/gitcredential_github.sh`）経由で、`GH_TOKEN` とは無関係

### 確認結果（環境変数を外さずに実行）

| コマンド | 結果 |
|---|---|
| `gh auth status` | Active account が `retroeater (GH_TOKEN)`、scopes は上の4つ |
| `gh api graphql -f query='{viewer{login}}'` | `retroeater` |
| `gh project list --owner retroeater` | 成功（`ryoei.pro enhancements`） |
| `gh workflow list` | 成功（6件） |
| `gh secret list` | 成功（`YOUTUBE_API_KEY` のみ） |
| `gh workflow run` | 2026-09-14（CHAT-0914-SK-12）に `sitemap-lastmod.yml` / `regenerate-page.yml` を作業ブランチで手動実行し、どちらも成功 |

### 以前の運用（廃止）と経緯

- Codespace 既定の `GITHUB_TOKEN` には Projects (V2) API の `project` スコープが無く、
  `gh workflow run`（Actions: write）・`gh secret list` も 403 になった
- そのため `env -u GITHUB_TOKEN -u GH_TOKEN gh ...` で環境変数を外し、
  `gh auth login` で作った `hosts.yml` の認証を使っていた
- `hosts.yml` はコンテナの再作成（rebuild）で消え、消えると `env -u` を付けても
  「未認証」になった（#302）。対処案 A〜E の検討は #309
- ユーザーシークレットは GitHub 側に保存され、Codespace の起動時に環境変数として
  注入される。コンテナを作り直しても設定し直す必要が無い

### PAT が失効・削除されたとき

- gh は `GH_TOKEN` を優先するため、無効なトークンでも `hosts.yml` に切り替わらず
  認証エラーになると見込まれる（未検証）
- 対処: GitHub の Settings → Developer settings で classic PAT を発行し直し
  （scopes は上の4つ）、Settings → Codespaces → Secrets の `GH_TOKEN` を更新して
  Codespace を再起動する。シークレットの Repository access に `retroeater/mj` を含めること

## `ryoei.pro` は `urllib` の既定 User-Agent のときだけ 403 になる（2026-09-15 実測）

**セッションから `ryoei.pro` には到達できる。** 403 になるのは User-Agent が `Python-urllib/…`（`urllib` の既定）のときだけで、
Python かどうかでは決まらない。`requests` の既定 User-Agent でも curl でも 200。

2026-09-15T02:07Z、codespace、プロキシ環境変数なし。curl 7.68.0 / Python 3.12.1 / requests 2.32.3（CHAT-0915-NT-01）。
ブラウザ UA は `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/140.0 Safari/537.36`。

| クライアント | User-Agent | `/` | `/saikyo/2026.html` | `/img/ogp.png` |
|---|---|---|---|---|
| curl | 既定（`curl/7.68.0`） | 200 | 200 | 200 |
| curl | ブラウザ UA | 200 | 200 | 200 |
| curl | `Python-urllib/3.12` を指定 | **403** | **403** | **403** |
| urllib | 既定（`Python-urllib/3.12`） | **403** | **403** | **403** |
| urllib | ブラウザ UA | 200 | 200 | 200 |
| requests | 既定（`python-requests/2.32.3`） | 200 | 200 | 200 |
| requests | ブラウザ UA | 200 | 200 | 200 |

- 403 は本文 `error code: 1010`、`server: cloudflare`。Cloudflare が User-Agent で拒否しているもので、
  **サイトの障害でもデプロイの失敗でもない**。curl で `Python-urllib` を名乗っても 403、`urllib` でもブラウザ UA なら 200 のため、
  判定は User-Agent の文字列による（2026-09-14 の初回観測では `User-Agent: Mozilla/5.0` と
  `check_image_links.py` の User-Agent〈`Mozilla/5.0 (compatible; ryoei.pro link checker; ...)`〉も 200）
- **`urllib` で `ryoei.pro` を取得するときは User-Agent を必ず指定する。** 指定しないと、本番は正常なのに
  「落ちている」「未反映」と誤判定する。`requests` と curl は既定のままでよい
- **リンク切れ検査の系統をローカルで走らせるときは特に注意する**
  （`check_image_links.py` / `check_ron2_images.py` / `collect_ron2_images.py`、
  ワークフローは `check-image-links.yml` / `check-ron2-images.yml`）。
  3本とも `urllib` で User-Agent をブラウザ風に設定済みだが、手元で `urllib` を直接使って
  書いた検証コードや新しく足したスクリプトは既定の User-Agent になる。
  403 に `server: cloudflare` と `error code: 1010` が付いていたら、
  リンク切れではなくこの判定を疑う
- 現在の検査対象（`jpml_pros.html` の画像）は `ron2.jp`・`pbs.twimg.com` などの
  外部ホストで、`ryoei.pro` は含まない。外部ホストが同様の判定をするかは未確認

## シートの行番号を取る手順

issue に行番号を書くときなどは、gviz ではなくシート単位の CSV エクスポートを使う。
gviz の結果は行番号を持たない。CSV エクスポートはグリッドをそのまま出すため、
レコードの順番（ヘッダを1行目として数える）がシートの行番号と一致する。

```
https://docs.google.com/spreadsheets/d/<SPREADSHEET_ID>/export?format=csv&gid=<gid>
```

gid は `https://docs.google.com/spreadsheets/d/<SPREADSHEET_ID>/htmlview` の HTML に
`{name: "<シート名>", pageUrl: "...gid=<数字>"` の形で載っている
（2026-09-14 時点で「最強戦」は `1323657235`）。

## 作業ファイルの置き場所（2026-09-19、CHAT-0916-LV-33・LV-34）

- **後の指示で使うスクリプト・中間データは scratchpad（`/tmp` 以下）に置かない。** Codespace の再起動で消える。
  CHAT-0916-LV-06 で、LV-03〜05 で使ったスクリプトと中間データ（`/tmp` 以下の scratchpad）が全部消え、
  トランスクリプトからコマンドを再構成して復旧した（`scripts/live_seed/README.md`「トランスクリプトからの再構成」）
- `/workspaces/` 以下（リポジトリ外）に置く。/live では `/workspaces/live-seed/`（貼り付け用の TSV）と `/workspaces/live-work/` を使った
- 作業ログの`## 経過`に、成果物を作ったスクリプトの置き場所を書く
- 指示の完了時に、リポジトリへ保存する（例: `scripts/live_seed/`。`scripts/` は `.assetsignore` で配信から外れている）か、使い捨てであることをログに書く
- scratchpad は、その指示の中だけで使う一時ファイル（スクリーンショット、比較用の生成物の退避など）に使う
