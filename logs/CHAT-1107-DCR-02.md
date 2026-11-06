# CHAT-1107-DCR-02

- 着手日時: 2026-11-06
- 対象issue: #672
- ブランチ: work/1106-dcr
- 着手時HEAD: 0881677c5

## 指示

【Claude作成】Claude Code 向け指示：辞書ページの題名を「辞書」にして、DCR-01 の分と合わせて cloudflare へマージする
Chat-Ref: CHAT-1107-DCR-02
マージ: 承認済み（チャットで）。ただし下の「止まる条件」を1つでも満たさなければマージせずに止まる
貼る時機: いつでも（CHAT-1106-DCR-01 は判断待ちで止まっている）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1106-dcr への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1106-dcr を続けて使う（DCR-01 の変更にこの直しを足して一緒にマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1106-DCR-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1107-DCR-02` を足す。

## 目的
DCR-01 の辞書ページ（#672）で、見える題名を log/・title/ と同じくページ名「辞書」にそろえ、DCR-01 の変更と合わせて本番へ入れる。

### 決定（2026-11-06、平野さん）
- DCR-01 のプレビューを見た結果、題名だけを直す。見える題名を「辞書」（h1、log/ の h1「ログ」と同じ形）にし、隠れた h1「リソース 辞書」と見出し「辞書ダウンロード」はなくす（2026-10-09 の h1「リソース 辞書」の決定〈docs/decisions/features.md の #377〉を置き換える）
- URL は `/resource_dictionary.html` のまま（DCR-01 の決定のまま）
- 次の止まる条件をすべて満たせば、プレビューを待たずに cloudflare へマージしてよい（チャット側がプレビューで見た DCR-01 の見た目〈ナビ・≡ のシートの「辞書」の印・400px の面・3行・暗い配色・説明文〉は問題なし）

### 前提（チャット側。平野さんの決定ではない）
- `<title>`（「辞書 | リソース | ryoei.pro」）・description・og は変えない（要確認: 今の値をログに引用する）
- 「本文の列が 400px の中央寄せ」の検査が `<main>` の最初の要素を見ている（DCR-01 の経過）ので、h1 を枠の中の先頭に置けば通る見込み

## 手順
1. `scripts/generate_resource_dictionary.py`（と必要なら `resource_dictionary.css`・テストの期待値）を直し、見える h1「辞書」を log/ と同じ `h1.mj-race-title` で出し、隠れた h1 と h2「辞書ダウンロード」を消す。`docs/decisions/features.md` に決定（置き換えの印を含む、docs/decisions/README.md の書き方）を、`docs/notes/static-generation.md` の resource_dictionary の行に今の形を書く
2. DCR-01 と同じ方法で生成し、テスト（`python3 -m unittest discover -s scripts/tests`）・`generate_resource_dictionary.py --check`（`regenerate-page.yml` の check でよい）・3形式の保存の確かめ・幅 390px の明るい配色・暗い配色のスクリーンショットを行う。docs/notes/self-review.md のとおり差分をレビューさせる
3. 止まる条件に当たらなければ cloudflare へマージし、本番の `https://ryoei.pro/resource_dictionary.html?v=<未使用の値>` に h1「辞書」と新しいナビ（`navbar.js` の分岐）が出ていることを確かめ、#672 に結果を書いて閉じる（残る作業があれば別の issue に起票する）

## 止まる条件（1つでも当たればマージせずに止まる）
- 見える h1 が「辞書」の1つではない、または「辞書ダウンロード」の見出しか隠れた h1 が残る
- DCR-01 の版から見て、HTML・CSS の差分が見出しの部分（とそのテストの期待値・docs）以外に出る
- 辞書ファイル3形式の中身（語数 1,869語〈シートが変わっていれば `--check` の語数〉・文字コード・改行）または保存名の形が変わる
- テストか `--check` が失敗する（今回の変更と無関係な失敗は、原因をログに書いたうえで進めてよい）
- 未マージの work/ ブランチが `generate_resource_dictionary.py`・`resource_dictionary.css`・`navbar.js` の同じ行・関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-DCR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-DCR-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複（`CHAT-1107-DCR-02`）: 全ブランチのコミットに無し。`DCR` は CHAT-1106-DCR-01 の続き（同じチャットの連番）
- `origin/work/1106-dcr` はリモートにあり（0881677c5、ローカルと一致）。`origin/cloudflare` は祖先でない → merge で取り込む
- 0. 指示欄の末尾の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行ともあり
- CHAT-1106-DCR-01 のログの状態を「判断待ち / 続き: CHAT-1107-DCR-02」にした

### 取り込みと重なり

- `git merge --no-edit origin/cloudflare`: 衝突なし（cloudflare 側は rh_paifu の削除・log/ の作業など。`navbar.js`・辞書のファイルには触れていない）。取り込み後に `merge-base --is-ancestor origin/cloudflare HEAD` は真
- 未マージの work/ ブランチで対象のファイルに触れるのは `work/1101-prp-pub` の `navbar.js` 2行（ドロップダウンの連盟プロの href と `/pro/` の分岐のコメント）だけ。辞書の分岐（`/log/` の直後）とは別の箇所なので止まらない

### 実装（ad47f435a）

- `scripts/generate_resource_dictionary.py`: `META.h1` を「リソース 辞書」から「辞書」にし、枠の中の先頭に `<h1 class="mj-race-title">{h1}</h1>`（log/ と同じ形）を出す。非表示の h1 と h2「辞書ダウンロード」を消した
- `resource_dictionary.css`: 冒頭のコメント1行だけを直した（規則は変えていない）
- テスト: `<h1 class="mj-race-title">辞書</h1>` があることと、「辞書ダウンロード」・非表示の h1「リソース」が無いことを確かめる期待値にした
- docs: `docs/notes/static-generation.md` の resource_dictionary の行。`docs/decisions/features.md` に決定を足し、元の決定の行に「→ 置き換え（h1 のみ）」を付けた（元の行は h1 以外の文言の決定も含むため「h1 のみ」とした）
- 指示文は置き換える決定を「2026-10-09 の h1『リソース 辞書』の決定」と書いているが、`docs/decisions/features.md` の実物は 2026-10-07（CHAT-1005-RVW-10）の節。中身（#377 の h1「リソース 辞書」）は同じなので、日付は実物で書いた
- 今の値の引用（変えていない）:
  - `<title>辞書 | リソース | ryoei.pro</title>`
  - `<meta name="description" content="一般的な麻雀用語、および日本プロ麻雀連盟・Mリーグ関連の用語（タイトル戦・選手・チーム・団体等）の辞書（1,869語）をダウンロードできます。">`
  - `og:title` は title と同じ文言、`og:description` は description と同じ文言、`og:url` は `https://ryoei.pro/resource_dictionary.html`
- DCR-01 の版（取り込み後の e4f7707a2 のマージ）から見た HTML の差分: h1 の1行の置き換えと h2 の1行の削除だけ（`git diff`）

### 確かめ

- `python3 -m unittest discover -s scripts/tests` → OK（レビューのサブエージェントの実行で 2038 件）
- `regenerate-page.yml`（work/1106-dcr、ad47f435a）:
  - `check: true`（run 42241585379）: 「検査: 通りました(保存の語数 1869語。ファイルは書いていません)」、注釈「check: 止まるページなし」
  - `dry_run: true`・`compare_ref: origin/cloudflare`（run 42241591191）: 注釈「compare: 差分なし」（コミット済みの生成物がシートからの生成と一致）
- 3形式の保存（ローカル、Playwright）: 保存名は `20261106_MSIME_麻雀用語辞書.txt`・`20261106_Google日本語入力_麻雀用語辞書.txt`・`20261106_Gboard_麻雀用語辞書.zip`。MSIME・Google は DCR-01 着手前の出力とバイト一致、Gboard は zip の中の `dictionary.txt` が一致
- スクリーンショット（幅 390px の明るい・暗い配色、ほか 360・1280px）: 題名「辞書」（28px 太字・左寄せ）の下に面と3行。保存後の登録方法の出方も変わらない。横スクロールなし（scrollWidth＝画面幅）

### 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェントに目的・決定・止まる条件・`git show ad47f435a` を渡した。止まる条件には当たらない、要修正なし、提案2件。

| 指摘 | 扱い |
|---|---|
| `scripts/lib/page.py` の `PageMeta.h1` のコメントが「visually-hidden の h1」のまま（`render_content()` は h1 を使わず、resource_efficiency にも見える h1 の前例あり。この変更より前からの記述） | 見送り（今回の指示のファイルの外。使い方は合っている） |
| h2 が無くなり `<section class="mj-dic-card">` が見出しを持たない | 意図どおり（決定のとおり。名前の無い section は汎用の入れ物と同じ扱いで害はない） |

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1106-dcr
- ログ: https://github.com/retroeater/mj/blob/work/1106-dcr/docs/logs/CHAT-1107-DCR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1106-dcr
- 確認用URL: なし
- マージ: 未
- issue: #672
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 48501e54）: https://github.com/retroeater/mj-logs/tree/main/guide/48501e54

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/48501e54/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9a5e2516.md
