# 作業ログのひな形

`docs/logs/<Chat-Ref>.md` を作るときは、下の「ひな形」をコピーして使う。
規則（1指示1ファイル、ログのpushを最初の手順にすること、`## 報告` を末尾に置くこと、項目を省かないこと）は
CLAUDE.md「作業ログ」節が正。ここには**書く時点で読めば足りる書き方**だけを置く。

このファイルは `CHAT-*.md` ではないため `cleanup-logs.yml` の削除対象にならない（`scripts/cleanup_logs.py` の `LOG_GLOB`）。

## `## 報告` の各項目の書き方

| 項目 | 書き方 |
|---|---|
| 状態 | 完了 / 判断待ち / 中断（エラー）のいずれか |
| ブランチ | 実際に使ったブランチ名。指示文の指定と違う場合はその旨と理由 |
| ログ | `https://github.com/retroeater/mj/blob/<実際に使ったブランチ>/docs/logs/<Chat-Ref>.md`。マージ済みなら `blob/cloudflare/`（CHAT-0918-TP-21） |
| 比較URL | `https://github.com/retroeater/mj/compare/cloudflare...<ブランチ>` |
| 確認用URL | **URL は書かない**（プレビューの URL は非公開として扱い、ターミナルへの最終報告にだけ書く。CLAUDE.md「作業ログ」節）。コード・生成物を変えた作業では「プレビューあり（URL は最終報告）」と、確認したページ。ビルドが走らなかった・失敗したときはその旨（代わりに使った確認用 URL も最終報告にだけ書く）。docs のみなら「なし」（詳細は `docs/notes/cloudflare.md`「work/ ブランチのプレビュー」） |
| マージ | 済（マージコミットのSHA）/ 未（平野さんの判断待ち） |
| issue | 番号（起票・クローズしたものを含む）。無ければ「なし」 |
| 判断が必要なこと | 箇条書き。無ければ「なし」 |
| 未確認の項目 | 箇条書き。無ければ「なし」 |
| エラー | 箇条書き。無ければ「なし」 |

**`## 報告` を書くときは、見出し直下の「状態:」（着手・中断・完了）も実態に直すこと。** 着手のまま残さない。

**URL の直後には全角文字を続けない**（改行か半角空白で区切る）。続く文字まで URL とみなされ、クリックすると404になる（BD-01）。

**`## 報告` に `Chat-Ref:` の行は置かない。** どの指示の報告かは「ログ:」の URL に含まれる Chat-Ref で見分ける（#419 (b)）。
`Chat-Ref:` を書くのは、コミットのトレーラ・issue のコメントの末尾・**ターミナルへの最終報告の最後の行**の3か所。

## ターミナルへ返す最終報告

2行目の URL は `## 報告` の「ログ」と同じもの。未マージなら
`https://github.com/retroeater/mj/blob/<実際に使ったブランチ>/docs/logs/<Chat-Ref>.md`、マージ後なら `blob/cloudflare/` 以下
（平野さんがログの場所を探さずに済むように、CHAT-0918-HT-05）。

```
完了
https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-MMDD-XX-nn.md
ブランチ: work/MMDD-xx（マージ後に削除済み）
ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-MMDD-XX-nn.md
Chat-Ref: CHAT-MMDD-XX-nn
```

プレビューで確認する作業では、「ブランチ:」の行の次に `確認用: <プレビューの URL>` の行を足す（ログには書かない）。

## ひな形

```markdown
# CHAT-MMDD-XX-nn

- 着手日時: YYYY-MM-DD
- 対象issue: #NNN（複数可。無ければ「なし」）
- ブランチ: work/MMDD-xx
- 着手時HEAD: <SHA>
- 状態: 着手

## 指示

（貼られた指示文をそのまま）

## 経過

（節目ごとに追記。調べたこと・検証の手順と結果など、詳細はすべてここに書く）

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:
```
