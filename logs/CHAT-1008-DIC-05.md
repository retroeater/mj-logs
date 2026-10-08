# CHAT-1008-DIC-05

- 着手日時: 2026-10-08
- 対象issue: #522・#515
- ブランチ: work/1008-dic
- 着手時HEAD: 8c3bb78f

## 指示

【Claude作成】Claude Code 向け指示：辞書ページを選んだ見た目（P1＋C2＋R1＋H1、Gboard の公式リンクなし）で本実装し、比較ページを消す（判断待ちで止まる） Chat-Ref: CHAT-1008-DIC-05 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1008-DIC-04 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-04 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-04 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1008-DIC-04 の比較ページで平野さんが見た目を選んだ。本番の辞書ページ（`resource_dictionary.html`）をその見た目で作り直し、比較ページと使わない案を消す（#522）。Gboard 形式（#515）も同じページに入れる。マージは平野さんの Android 実機での Gboard の取り込みと、このプレビューの見た目の確認の後。
決定（2026-10-09、平野さん）

* 見た目は P1「ステップカード」＋ C2「チップ」（カテゴリ）＋ R1「セグメント」（形式）＋ H1「ⓘ チップ＋吹き出し」（登録方法）
* Gboard の公式ヘルプへのリンクは置かない（Microsoft IME・Google 日本語入力は公式ヘルプへのリンクを置く。DIC-04 の H1 のとおり）
* Gboard 形式だけを先に本番へ入れることはしない（見た目の刷新と一緒に入れる）
* カテゴリの短い説明の文言は不要（ページに出さない）
* Android の実機での Gboard の取り込みは、平野さんが後日行う

前提（チャット側。平野さんの決定ではない）

* 本番ページの「辞書登録方法」の @IT の2記事へのリンクは、H1 のチップと吹き出しに置き換えて消す案。消さないほうがよい理由があれば報告する
* 比較ページ（`resource_dictionary_compare.html`）・`render_compare_page()`・`ComparePageTest`・使わない案（P2・P3・C1・C3・R2・R3・H2・H3）の CSS・`scripts/regenerate.py` の出力の一覧の比較ページの行を消す（docs/notes/title-pages.md「採用後は使わない案のコードとラジオボタン、比較ページを消す」）。採用した案の CSS は `resource_dictionary.css` に置く（`style.css` は変えない。2026-10-09 の決定）
* 語数の合計の表示と、何も選ばないときにボタンを押せなくする動き（DIC-04 で作った）は残す
* META の description（「Microsoft IME・Google日本語入力」）と `scripts/apply_page_meta.py` は変えず、Gboard を足した案の文面を報告の「判断が必要なこと」に書く
* 本番の公開済みページの作り直しなので、noindex・navbar・sitemap の扱いは今のまま（変えない）

手順

1. 確かめる: CHAT-1008-DIC-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-05` を足す。上の決定を `docs/decisions/` に足す。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/`・`scripts/regenerate.py` を変えていないか確かめる（`style.css` だけを変えているものは止まる理由にしない）。
2. 作る: 本番の辞書ページを決定の見た目で作り直し、前提のとおり比較ページと使わない案を消す。全ページを再生成し、差分を種類に分けて報告する。ローカルの Chromium で PC 幅（1280px）とスマホ幅（390px）のスクリーンショットを撮り、3形式（Microsoft IME・Google 日本語入力・Gboard）で4つ全部と「連盟プロ」「Mリーグ」だけの保存を DIC-04 と同じ観点で確かめる（行数・重複・文字コード・改行・Gboard の zip の CRC）。キーボード操作とフォーカスの見え方、吹き出しの開閉（Enter・Esc・外側のタップ）も確かめる。`docs/notes/static-generation.md`「ページの一覧」の辞書の行を今の作りに合わせる。#522 と #515 に経過をコメントする。
3. 報告する: 本番ページのプレビュー URL と、平野さんに見てほしい点（PC とスマホでの見た目、Android での Gboard の取り込み）を書いて、判断待ちで止まる。

止まる条件

* CHAT-1008-DIC-04 の状態が「判断待ち」でない
* 未マージの work/ ブランチが上の手順1のファイル（`style.css` を除く）を変えている
* 全ページの再生成で、辞書ページ・`dic/`・消した比較ページ以外に、ほかのシートの変化で説明できない差分が出た
* 3形式のどれかで、行数が `dic/*.json` から数えた見込み（重複をまとめた後）と合わない
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- CHAT-1008-DIC-04 の `## 報告` の状態は「判断待ち」
- `git log --all --grep="CHAT-1008-DIC-05"` は0件
- ローカルの work/1008-dic は origin/work/1008-dic と同じ（8c3bb78f）。`git merge-base --is-ancestor origin/cloudflare HEAD` は真で、取り込みは不要だった

### 手順1（確かめ）で止まった

- DIC-04 のログの状態に ` / 続き: CHAT-1008-DIC-05` を足し、`docs/decisions/features.md` に 2026-10-09（DIC-05）の決定を足した（どちらも push 済み）
- 未マージの work/ ブランチ（この作業を除く）:

| ブランチ | 手順1のファイルの変更 |
|---|---|
| `origin/work/1008-hou`（#518、先頭 be5a9100） | `scripts/regenerate.py`・`style.css` |
| `origin/work/1009-nen` | `style.css` だけ |
| `origin/work/1009-swp-fix` | なし |

- `origin/work/1008-hou` が `scripts/regenerate.py` を変えているため、止まる条件「未マージの work/ ブランチが上の手順1のファイル（`style.css` を除く）を変えている」に当たり、手順2に入らず止まった
  - 中身（b33bc59d `feat: add unpublished houou/ sample pages (#518)`）: `OUTPUT_OVERRIDES` の `"houou_race"` の行と `"resource_dictionary"` の行の間に、`"houou_pages": "houou/",` とそのコメントの2行を足す
  - この作業（DIC-04）は同じ辞書の行を `"resource_dictionary.html resource_dictionary_compare.html dic/"` に変えた。隣り合う行なので、後からマージする側で衝突しうる
  - DIC-05 の手順2では比較ページの行を消すので、作業が終わると `scripts/regenerate.py` は cloudflare と同じに戻る（この作業の差分は0行になり、work/1008-hou と重ならない）

## 報告

- 状態: 判断待ち / 続き: CHAT-1008-DIC-06
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし（手順2に入っていない）
- マージ: 未（指示どおり判断待ち）
- issue: #522・#515
- 判断が必要なこと:
  - 未マージの work/1008-hou（#518）が `scripts/regenerate.py` の辞書の行の隣に2行を足しているため止まった。進め方の案: DIC-05 の手順2で比較ページの行を消すと、この作業の `scripts/regenerate.py` の差分は0行になり重ならなくなるので、このまま手順2に進む。衝突が出るとすれば work/1008-dic の途中の版を取り込んだときだけで、そのときは両方の行を残して解ける（追記と隣の行）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
