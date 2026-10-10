# CHAT-1008-DIC-18

- 着手日時: 2026-10-10
- 対象issue: #533
- ブランチ: work/1008-dic
- 着手時HEAD: f694ae1b

## 指示

【Claude作成】Claude Code 向け指示：DIC-17 の続き。直したシートで全ページを再生成し、確かめてマージする Chat-Ref: CHAT-1008-DIC-18 マージ: 承認済み（チャットで） 貼る時機: 平野さんが「辞書」タブの2か所（見出しの行の写し・「Mリーグ機構」の重複）を直した後（CHAT-1008-DIC-17 は判断待ちで止まっている）。急ぎ（push のたびと週次の全ページの再生成が止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-17 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-17 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
DIC-17 で生成スクリプトを「コメント」列の無いシートに合わせて直した（work/1008-dic に push 済み・未マージ）。止まっていたのは「辞書」タブのデータの2か所のためで、平野さんが直した。DIC-17 の手順2の残りと手順3を行い、止まっている再生成を戻す。
決定（2026-10-10、平野さん）

* 「辞書」タブの見出しの行の写しと、一般用語「Mリーグ機構」の重複を直した
* DIC-17 の直し（`DICT_HEADERS` からコメントを外し、行は4要素でコメントを空にする）で本番に入れる

前提（チャット側。平野さんの決定ではない）

* 生成スクリプトでの読み飛ばしはしない（DIC-17 のとおり、見出しの写しや重複を見つけて止める仕組みは残す）
* DIC-17 の試算では 1,870 語（一般用語 583・連盟用語 138・連盟プロ 1,099・Mリーグ 71）。平野さんの直し方によって数語ずれることがある。違いは報告し、シートの変化で説明できれば止まらない
* 「Mリーグ機構」「一般社団法人Mリーグ機構」が Mリーグから一般用語へ、「プロ連盟」が連盟用語から一般用語へ移るのは、シートの変化（平野さんの直し）として扱う
* 確かめの内容は DIC-17 の手順2・3のまま（3形式の保存の行数と説明文の語数、Google 日本語入力用が4列でコメント欄が空、マージ後の regenerate が最後まで通り sitemap の lastmod も更新される、#533 へのコメント）

手順

1. 確かめる: CHAT-1008-DIC-17 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-18` を足す。上の決定を `docs/decisions/` に足す。「辞書」タブを生成と同じ経路で読み、生成が通ることを確かめる。未マージの work/ ブランチが `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`dic/` を変えていないか確かめる。
2. 直す: 全ページを再生成し、差分を種類に分けて報告する。3形式の保存のファイルを作り、行数が説明文の語数と合うこと、Google 日本語入力用が4列でコメント欄が空であることを確かめる。カテゴリごとの語数の変化を報告する。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と regenerate-page.yml の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）。regenerate が最後まで通ったこと（`resource_dictionary` より後のページも再生成・コミットされ、sitemap の lastmod も更新されたこと）と、本番の辞書ページの語数・3形式の保存の行数が合うことを確かめる。#533 に「#243 が辞書の見出しの変化で止まった。DIC-17・18 で直した」とコメントする。CHAT-1010-WHS-01 のログの `## 報告` の状態が「判断待ち」なら、その末尾に `/ 辞書の件: CHAT-1008-DIC-18 で解消` を足す。

止まる条件

* CHAT-1008-DIC-17 の状態が「判断待ち」でない
* 「辞書」タブでまだ生成が止まる（止まる行と理由を報告する）
* 未マージの work/ ブランチが上の手順1のファイルを変えている
* 全ページの再生成の差分に、シートの変化と生成スクリプトの直しで説明できない変更がある（見込み: 辞書ページ・`dic/*.json`・`resource_dictionary.*` と、止まっていた間に変わったシートによる他ページの差分〈種類を分けて報告〉）
* 保存されるファイルの形が変わる、または行数が説明文の語数と合わない
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- `git log --all --grep="CHAT-1008-DIC-18"` は0件。CHAT-1008-DIC-17 の状態は「判断待ち」
- origin/work/1008-dic（a58b4e37、DIC-17 の直し・未マージ）にログを積んで push し、`git merge origin/cloudflare` で取り込んだ（衝突なし）。マージ前にもう一度取り込んだ（docs のみ、衝突なし）

### 手順1（確かめ）

- DIC-17 のログの状態に ` / 続き: CHAT-1008-DIC-18` を足し、`docs/decisions/features.md` に 2026-10-10（DIC-18）の決定を足した
- 「辞書」タブを生成と同じ経路で読み、生成が通った（止まる行なし）。語数: 一般用語 583・連盟用語 138・連盟プロ 1,099・Mリーグ 71、保存の語数（（読み, 語）でまとめた数）1,870。DIC-17 の試算と同じ
- 未マージの work/ ブランチ（work/1008-hou・work/1009-nen・work/1009-swp-526・work/1010-rdn・work/1010-whs）に、`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`dic/` を変えているものは無い

### 手順2（直す）

- 全ページを再生成した（`python3 scripts/regenerate.py all`）。差分の種類:
  - 辞書（生成スクリプトの直しとシートの直し）: `resource_dictionary.html`（語数 1,822 → 1,870）・`dic/mahjong.json`・`dic/renmei.json`・`dic/mleague.json`（`dic/pros.json` は変化なし）
  - 鳳凰位決定戦への道（止まっていた間のシートの変化）: `houou_race/23-2.json`〜`42-2.json` の20ファイル。シートの G列が各期の A1 上位3名で「残留」→「決定戦」に変わったもの（60件、シートとの食い違い0）
  - それ以外の差分は無い
- カテゴリごとの語数の変化: 一般用語 546 → 583、連盟用語 127 → 138（部署名「経理部」「事業部」「法務部」「メディア戦略部」が入った）、連盟プロ 1,099（変化なし）、Mリーグ 73 → 71（「Mリーグ機構」「一般社団法人Mリーグ機構」が一般用語へ、「プロ連盟」は連盟用語から一般用語へ。前提のとおりシートの変化）
- 3形式の保存（手元のサーバー、PC 幅・スマホ幅）: どれも 1,870 行で説明文の語数と一致、重複なし
  - Microsoft IME: UTF-16LE・BOM あり・CRLF・3列
  - Google 日本語入力: UTF-8・LF（CR なし）・4列でコメント欄はすべて空
  - Gboard: zip の中身は `dictionary.txt` 1つ、先頭行 `# Gboard Dictionary version:1`、各行「読み・語・ja-JP」、CRC 一致
- テスト（`python3 -m unittest discover -s scripts/tests`）は OK

### マージ

## 報告

- 状態: 完了
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1008-DIC-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: マージの前に書いている。結果は経過の「マージ」に追記する
- issue: #533
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 74c6dc64）: https://github.com/retroeater/mj-logs/tree/main/guide/74c6dc64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c6dc64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c6dc64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c6dc64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c6dc64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c6dc64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/74c6dc64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/647a8db8.md
