# CHAT-1017-OGR-07

- 着手日時: 2026-10-17
- 対象issue: #581
- ブランチ: work/1017-ogr
- 着手時HEAD: 37bcb2687

## 指示

【Claude作成】Claude Code 向け指示：title/ の大会・期のページの og:title に「タイトル戦」を入れる（#581） Chat-Ref: CHAT-1017-OGR-07 マージ: 承認済み（チャットで）。ただし「止まる条件」の差分の範囲を外れたら止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-ogr の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
CHAT-1016-OGR-06 で title/ の全ページの OGP 画像が「タイトル戦」になった。そのため X のカードで、`title/houou/`（タイトル戦の鳳凰戦）と `houou/`（鳳凰戦）の帯の文字が同じ「鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」になり、区別がつかない。title/ の大会・期のページの og:title に「タイトル戦」を入れて区別する。
決定（2026-10-17、平野さん）

* CHAT-1016-OGR-06 の4つの URL（title/・title/houou/・houou/・saikyo/）を X で確かめた（問題なし）
* title/ の大会のページと期のページの og:title に「タイトル戦」を入れる。例: 「鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」を「鳳凰戦 | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」に、「第41期鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」を「第41期鳳凰戦 | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」にする（docs/decisions/title.md の grill Q3 の og:title の形を置き換える）
* 変えるのは og:title だけ。`<title>`・共有ボタンの文言・画面のパンくずは変えない
* プレビューを見ずにマージしてよい

前提（チャット側。平野さんの決定ではない）

* 2026-10-17 にチャット側が origin/cloudflare の生成済みの HTML で見た形: 入口 `title/index.html` の og:title は「タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」（これは変えない）、大会 `title/houou/index.html` は「鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」、期 `title/houou/1.html` は「第1期鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」。og:title を決めているのは `generate_title_pages.py`（`PageMeta.og_title`）の見込み（要確認）
* title/ のページ数は CHAT-1016-OGR-01 の `## 経過`「手順2」の表で 384件（入口を含む）。変わるのは入口を除く大会・期のページの見込み
* 長い大会名（「女流プロ麻雀日本シリーズ」など）では X の帯で末尾が切れることがある（平野さんは承知のうえ）

手順

1. title/ の大会・期のページの og:title に「 | タイトル戦」を、大会名（期のページは「第N期<大会名>」）の直後に入れて、title/ を再生成する。テストを通す（og:title の期待値を持つテストがあれば直してよい）
2. 記録: #581 に上の「決定」の4点（2026-10-17）をコメントで書く。docs/decisions/title.md の grill Q3 の og:title の決定に「→ 置き換え: 2026-10-17（CHAT-1017-OGR-07）」を付けて新しい形を足し、docs/notes/title-pages.md・docs/notes/ogp.md に og:title の形の記述があれば直す（追記先の今の内容を読み、矛盾していて判断が要るときだけ止まる）
3. cloudflare へマージし、デプロイの後に本番の `title/houou/`・`title/houou/1.html`・`title/` の og:title を `?v=<未使用の値>` 付きで取得してログに引用する

止まる条件

* 差分（docs/ を除く）が、title/ の大会・期のページの og:title の行（ページ数をログに書く。入口と年表などほかのページは変わらない）と、生成スクリプト・テストの変更の範囲を外れる。生成のたびに入るシートの変化で説明できる差分は、内容をログに書いて進めてよい。説明できなければ止まる
* `<title>`・共有ボタンの文言・パンくずが変わる差分が出た
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が、`generate_title_pages.py` の og:title を決める同じ行・同じ関数を変えている、または取り込みで衝突する
* マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係なら原因を報告に書いたうえで残りの手順を進めてよい。自分の変更で落ちると分かっているテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（マージ後の自動の再生成〈regenerate-page.yml〉で変わる範囲も、上の差分の範囲で見る）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-OGR-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-OGR-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `CHAT-1017-OGR-07` のコミット: 無し（`OGR` は同じセッションの続き）
- 作業ブランチ: ローカル・リモートとも `work/1017-ogr` が無いため `git checkout -b work/1017-ogr origin/cloudflare`（着手時 HEAD 37bcb2687）
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有る
- 重なり: 未マージの `work/` 9本（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-amp・1017-hou・1017-mck-367-all・1017-pad・OUK）は `generate_title_pages.py` を変えていない

### 前提の確かめと実装

- og:title を決めているのは前提どおり `generate_title_pages.py` の `page_meta()`: `og_title=f"{og_label} | {SECTION_NAME} | ryoei.pro"`。呼び出しは入口（`OGP_INDEX_ALT`＝「タイトル戦」）・大会（`t.name`）・期（`p.title`）の3か所だけ
- `section_og_label(label)`（`f"{label} | {OGP_INDEX_ALT}"`）を足し、大会・期の呼び出しの `og_label` に使った。入口と `<title>`（`title` 引数）は変えない。共有ボタンの文言は `<title>` から作るので変わらない
- テスト `test_title_ogp.py` の `OgTitleTest` の大会・期の期待値を新しい形にし、大会の `<title>` が変わらないことも確かめるようにした。`python3 -m unittest discover -s scripts/tests` OK

### 再生成と差分

- `python3 scripts/regenerate.py title_pages`: 成功
- 差分（docs/ を除く）: og:title の行だけが **383ページ**（title/ 384 − 入口 1。大会 20・期 363）。`title/index.html` は変化なし。`<title>`・パンくず・ほかの行は変わらない。シートの変化による差分は無し。スクリプト1つとテスト1つ
- 例: `title/houou/index.html` 「鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」→「鳳凰戦 | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」、`title/houou/1.html` 「第1期鳳凰戦 | …」→「第1期鳳凰戦 | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」

### 記録

- #581 に決定の4点をコメントした
- docs/decisions/title.md: 2026-10-01 の og:title の形の行（grill Q3）に「→ 置き換え: 2026-10-17（CHAT-1017-OGR-07）」を付け、OGR-07 の節を足した
- docs/notes/title-pages.md の og:title の形を直した。docs/notes/ogp.md には title/ の og:title の形の記述が無いので変えていない

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

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2922a722）: https://github.com/retroeater/mj-logs/tree/main/guide/2922a722

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2922a722/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2922a722/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2922a722/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2922a722/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2922a722/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2922a722/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2922a722.md
