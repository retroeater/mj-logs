# Claude for Chrome で mj を読むとき（チャット側、PC）

チャット側の主な読み方は mj-logs（`docs/notes/chat-side-operations.md`）。この文書は、PC で Claude for Chrome を使い
private の `retroeater/mj` を直接読むときの補助の手順。**Chrome を使う前にこの文書を読む。**
事例と確かめた日付は `docs/notes/handover-archive-2026.md`「docs/notes/chat-side-operations.md から」。

## 使い方

- **Chrome は平野さんが用意した「Claude」プロファイル（接続名「Claude in Chrome」）で使う。** 平野さん自身のプロファイルは使わない
- **1つの確認につき、ブラウザの操作は1回（タイムアウト4分）まで。** タイムアウトしたら同じ操作をやり直さず、
  `docs/notes/chat-side-operations.md`「確認対象ごとの手段」に切り替える（待つより速い）。
  **後で別の確認をするときは、また1回試してよい。** 一度のタイムアウトを理由に、その会話でブラウザを使うのをやめない
- 応答しなくなる事象は一時的なもので、恒久的な故障として扱わない（navigate は通るが get_page_text / screenshot / find だけが
  4分でタイムアウトする形がある。Claude Desktop の再起動では直らないことがある）
- **タブを閉じる操作はタイムアウトしやすい。** 閉じられずに残ったタブは、平野さんが閉じてよい
- **タブのグループは、会話のターンをまたぐと無効になることがある。** URL を指定して開き直すほうが確実

## GitHub のファイルを読むとき（確認済みの挙動）

- **raw 表示か、blob 表示（Markdown を描画した表示。`?plain=1` を付けない）を開き、get_page_text で本文全体を取る。**
  40KB 台のファイルも1回で取れている
  - raw 表示: `https://github.com/retroeater/mj/raw/<ブランチまたはSHA>/<パス>` を開くと、トークン付きの raw.githubusercontent.com に移動する
  - **`?plain=1` 付きの blob 表示では、get_page_text はファイルのメタ情報しか返さない。**
    Markdown 以外のファイル（`.py` など）は、`?plain=1` を付けない blob 表示でもメタ情報（行数・サイズ）しか返らない
- **github.com を開いた状態の JavaScript から、raw を fetch できる。** 本文をチャットに返さず、統計や一部分だけを返せる。
  `/retroeater/mj/raw/<ブランチ>/<パス>` を fetch すれば、ページの表示を待たずに中身を取れる（work/ ブランチのログも読める）
- **javascript_tool の戻り値は長いと切り詰められる。** 上限は一定でなく、約1,000字で `[TRUNCATED]` になったことがある
- **`?` `&` `=` を含む出力はブロックされる。** 返す前に別の文字へ置き換える
- **重い処理は打ち切られる。複数ファイルを回す処理は書かず、1回の操作では1ファイルだけを扱う。**
  複数ファイルを連続で fetch した直後に、次の操作が4分のタイムアウトになった例が複数ある（因果は未確認）
- ブランチの検索は `https://github.com/retroeater/mj/branches/all?query=<語>`
- **Issues の検索結果の一覧は get_page_text では取れない。** JavaScript で `[role=listitem]` の中のリンクを拾う
- **ブランチ名で開いた内容は最新とは限らない。** 「未反映」と判断する前に、コミットSHA指定で開くか Claude Code 側に確認してもらう
  （ブランチ名で開いて、push 済みの変更を「未反映」と誤報した例が2回ある）
- **変更の前後を読み比べるときは、コミットの差分（`https://github.com/retroeater/mj/commit/<SHA>.diff`）を開いて get_page_text で読む。**
  比較ページ（`/compare`）は差分が読み込まれず、コミットの一覧しか取れない
- ブランチ名で mj を開くときは、最終報告2行目の URL を使う（推測して組み立てない）

## 上の方法で読めないとき

- `?plain=1` 付きの blob 表示を開き、JavaScript で `#read-only-cursor-text-area` の `value` を読む。
  戻り値が切り詰められるため区切って返す。作業ログは末尾の `## 報告` 節から読むと速い
- **作業ログも、まず `?plain=1` を付けない表示 + get_page_text を試す。** どちらの読み方もタイムアウトした例と、
  もう片方で読めた例の両方がある。**どちらかが駄目ならもう片方を試す**
