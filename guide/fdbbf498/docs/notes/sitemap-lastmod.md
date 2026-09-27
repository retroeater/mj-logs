# sitemap の lastmod を git から導出する（#265）

## 結論

`sitemap-pages.xml` / `sitemap-wayhome.xml` / `sitemap-saikyo.xml`（#319）/ `sitemap-title.xml`（#222。末尾が `/` の URL は `index.html` に対応させる）の lastmod は、生成・非生成を区別せず
**各ページの git 最終コミット日（JST）** とする。導出は
`scripts/update_sitemap_lastmod.py --from-git`。sitemap を手で書き換えないこと。

| 契機 | 呼び出し元 |
|---|---|
| HTML を含む `cloudflare` への push（人手のコミット） | `.github/workflows/sitemap-lastmod.yml` |
| 再生成（push 検知・週次 all・手動実行） | `.github/workflows/regenerate-page.yml` |

## なぜ

#121 の自動更新は `regenerate-page.yml` が再生成したときだけ働いた。そのため
人がテンプレート修正やアクセシビリティ対応をコミットすると lastmod が動かず、
2026-09-14 時点で pages 25件中22件、wayhome 38件全件がずれていた（CHAT-0914-A-04）。
非生成ページだけを直してもこの穴は塞がらないため、根拠を git に一本化した。
publishedAt（動画の公開日）を wayhome に使う案は、ページを直しても動かないため取り下げた。

## --from-git の規則

- URL → ファイル: `https://ryoei.pro/` は `index.html`、それ以外はパスをそのまま使う
- 日付: `git log -1 --format=%ct` を JST で日付にする（`%cs` はコミッタのタイムゾーン依存で、UTC の Codespace では JST とずれうる）
- 未コミットの差分（`git diff HEAD` に出る）があるファイルは当日日付（JST）。再生成直後に呼ぶケース
- ファイルが無い・git 履歴が無い（未追跡）は、エラーにせずスキップして標準エラーに出す。新規の wayhome・最強戦のページは生成スクリプトが当日日付を入れる
- 既存値と同じなら書き換えない（冪等）。書き換えは正規表現で該当箇所のみ（XML パーサはコメントや整形を変えるため使わない）
- 変更したページは標準出力に `パス: 旧 → 新` で出す

## ワークフローの競合対策

- `sitemap-lastmod.yml` の `paths: '**.html'` は、自分のコミット（sitemap*.xml のみ）に一致しないため再帰しない。`GITHUB_TOKEN` の push は他のワークフローを起動しないので、`regenerate-page.yml` のコミットもここを起動しない（そのため `regenerate-page.yml` 側でも `--from-git` を呼ぶ）
- 同じ push で `regenerate-page.yml` も起動する場合（`scripts/generate_*.py`・`scripts/lib/**`・ルート直下 `*.js` を含む push）、`sitemap-lastmod.yml` は何もしない。両方が sitemap を push すると後着が拒否されるため
  - `table.js` のようにどのページにも一致しない変更と HTML を同時に push すると、どちらも lastmod を直さない。週次の all 実行で直る
- それ以外の push 競合は、rebase ではなく「最新のブランチ先頭へ reset して計算し直す」を最大3回繰り返す。lastmod は git 履歴だけで決まるため、sitemap の衝突を解くより作り直すほうが確実
- `regenerate-page.yml` 自身の push 競合は対策していない（#263）

## 注意

- HTML を含む push のたびに sitemap コミットが1つ増え、Workers Builds のデプロイも1回増える（表示は変わらない）
- 作業ブランチでコミットした日が lastmod になる（`cloudflare` へのマージ日ではない）
