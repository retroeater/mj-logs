# CHAT-1008-DIC-11

- 着手日時: 2026-10-09
- 対象issue: #522・#515
- ブランチ: work/1008-dic
- 着手時HEAD: 8c5766bb

## 指示

【Claude作成】Claude Code 向け指示：DIC-10 の続き。見出しを「辞書ダウンロード」に変えてマージする Chat-Ref: CHAT-1008-DIC-11 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1008-DIC-10 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-10 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-10 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
平野さんが DIC-10 のプレビューを見た。カードの見出しを1つ直して、新しい辞書ページを本番に入れる（#522・#515）。今の cloudflare の生成は「辞書」タブの「一般用語」で止まっているので、このマージで直る。
決定（2026-10-09、平野さん）

* DIC-10 のプレビューの見た目（カード・PC は横／スマホは縦のボタン・「ⓘ 登録方法」の吹き出し・説明文）でよい
* カードの見出し「ダウンロード」を「辞書ダウンロード」に変える。この直しはプレビューを見ずにマージしてよい
* マージの後に、PC でボタンを縦に並べた形のプレビューと、登録方法の別案（保存した後に、その形式の手順をボタンの下に出す）を別の指示で見る

前提（チャット側。平野さんの決定ではない）

* 見出しの文言は生成スクリプトの1か所（とテスト）の想定。吹き出しの `aria-label` などに「ダウンロード」の見出しを指す文言があれば合わせる
* マージ後の本番では、平野さんが Android で Gboard の取り込みを確かめてから #515 を閉じる（2026-10-09 の決定）。この指示では #515 を閉じない

手順

1. 確かめる: CHAT-1008-DIC-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-11` を足す。上の決定を `docs/decisions/` に足す。未マージの work/ ブランチが辞書のファイル（`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/`・`scripts/apply_page_meta.py`）を変えていないか確かめる。
2. 直す: 見出しを「辞書ダウンロード」にし、テストを合わせる。全ページを再生成し、差分を種類に分けて報告する。「辞書」タブを生成と同じ経路で読み、カテゴリの値が3つ（一般用語・連盟用語・Mリーグ）であることを確かめる。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と regenerate-page.yml の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）。本番の `resource_dictionary.html`・`.css`・`.js`・`dic/*.json` が 200 で新しい作り（見出し「辞書ダウンロード」・形式のボタン3つ・説明文の語数）であること、3形式の保存の行数が説明文の語数と合うことを確かめる。#522・#515 に経過をコメントする（#522 は閉じず、別案の比較が続くことを書く）。

止まる条件

* CHAT-1008-DIC-10 の状態が「判断待ち」でない、または「辞書」タブのカテゴリの値が3つでない
* 未マージの work/ ブランチが上の手順1のファイルを変えている
* 全ページの再生成の差分に、決定とシートの変化で説明できない変更がある（見込み: DIC-10 の差分〈辞書ページ・`dic/mahjong.json` の名前・`resource_dictionary.*`・`scripts/apply_page_meta.py` など〉と見出しの文言、マージ後の自動再生成と同じ種類の変化だけ）
* 保存の行数が説明文の語数と合わない
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #522・#515
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d2ff5f93）: https://github.com/retroeater/mj-logs/tree/main/guide/d2ff5f93

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/55b6a3cb.md
