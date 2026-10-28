# セッション環境の実測と手順

`ryoei.pro` の User-Agent とシートの行番号の取り方をまとめる。Codespace の時の記録（Rebuild・到達の実測・確認用 URL・gh の認証・作業ファイルの置き場所）は `docs/notes/handover-archive-2026.md`「Codespace の旧記録（2026-10-28 に移動、CHAT-1028-AGT-04）」。

外部ドメインへの到達可否は環境側（codespace・ネットワークポリシー）の設定で変わりうる。
記述と食い違ったら再測定すること。条件の確認は #328。check-run と本番反映の対応は docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」（#334）。

---

以下の節の一部は、削除した旧ノート（Codespace 内のセッション環境の記録。CHAT-0914-GH-01 / SK-05 / DOC-01）から
この文書に移したもの（CHAT-0914-SK-17）。同じく移した「gh の認証」は `docs/notes/handover-archive-2026.md`「Codespace の旧記録（2026-10-28 に移動、CHAT-1028-AGT-04）」へ移した。

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
  （`check_image_links.py`、ワークフローは `check-image-links.yml`）。
  `urllib` で User-Agent をブラウザ風に設定済みだが、手元で `urllib` を直接使って
  書いた検証コードや新しく足したスクリプトは既定の User-Agent になる。
  403 に `server: cloudflare` と `error code: 1010` が付いていたら、
  リンク切れではなくこの判定を疑う
- 現在の検査対象（`jpml_pros.html` の画像）は `pbs.twimg.com` などの
  外部ホストで、`ryoei.pro` は含まない。外部ホストが同様の判定をするかは未確認

## シートの行番号を取る手順

2026-10-21 から、スプレッドシートは Sheets API（`sheets-reader` の鍵）で読む（#557、docs/notes/static-generation.md「シートの読み方（Sheets API）」）。
専用フォルダ「【本番環境】」を「制限付き」にした後は、ログインなしの gviz・CSV エクスポート・htmlview では読めない。Claude Code の環境には鍵が無いので、
行番号はワークフロー（`regenerate-page.yml` の dry_run・`check-sheets-reader.yml` など）の出力で取る。`values.get` の行の順番（見出しを1行目として数える）がシートの行番号と一致する
（`fetch_sheet()`・`fetch_table()` の行は完全に空の行を除くので、行番号とはずれうる）。
