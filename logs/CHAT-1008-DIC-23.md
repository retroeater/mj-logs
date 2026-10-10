# CHAT-1008-DIC-23

- 着手日時: 2026-10-10
- 対象issue: #515・#522
- ブランチ: work/1008-dic
- 着手時HEAD: 7e1c39ce

## 指示

【Claude作成】Claude Code 向け指示：「辞書」のリニューアルの告知動画（初版）を作る。判断待ちで止まる
Chat-Ref: CHAT-1008-DIC-23
マージ: 判断待ちで止まる（制作のスクリプトはマージしない。手順1の辞書ページの再生成だけは本番に入れてよい）
貼る時機: いつでも（CHAT-1008-DIC-22 は完了）
作業ブランチ: クラウドセッションで実行する。work/1008-dic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-dic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
リニューアルした「辞書」（`resource_dictionary.html`、#515・#522）を X で告知するための動画を、`docs/notes/page-announcement.md` の型で作る。初版の mp4 を `SendUserFile` で送り、判断待ちで止まる。

### 決定（2026-10-10、平野さん）
- 告知動画は 20〜30秒。内容は、簡単な操作説明（画面紹介）・元々あった機能の紹介（Microsoft IME・Google 日本語入力）・Android 対応（新規）・連盟関連の用語（タイトル戦・選手等）の充実・Mリーグ関連の用語（チーム・選手等）の追加（新規）・各カテゴリの語数
- 下の「構成」と「投稿文」でよい
- 語の例は「鳳凰戦」「白鳥翔」「渋谷ABEMAS」を映す
- 曲は型のとおり（`scripts/promo_video/title/music.py` の曲調 b）。最初のコマ（X のサムネイルになる）からタイトルをはっきり出すこと、最後は「ryoei.pro」のロゴで終わることを意識する
- カテゴリ間の重複は、「日本プロ麻雀連盟」（一般用語と連盟用語）をシートで1つ消した。連盟所属の Mリーガー20名が連盟プロと Mリーグの両方に入るのはそのままでよく、カテゴリごとの語数の和が全体の語数と一致しなくてよい

### 構成（決定）
縦長 1080×1920・30fps・白地に黒字・音楽つき。型（page-announcement.md「告知動画の型」）に従う。秒は目安で、実物に合わせて ±1秒程度は変えてよい（変えたら報告する）

| 秒 | 画面 | テロップ |
|---|---|---|
| 0〜2.5 | 白地に「麻雀用語辞書」「スマホ・PC の変換に登録」。**最初のコマから文字をはっきり出す**（フェードインで薄いコマを作らない） | なし |
| 2.5〜5 | 本番のページ（スマホ幅）。「辞書ダウンロード」のカードと3つのボタンに順に枠を出す | 形式を選んで押すだけ |
| 5〜8 | Microsoft IME と Google 日本語入力のボタンに枠 | Windows・Mac の IME に対応 |
| 8〜12 | Gboard に押す印 → 押す →「辞書ファイルをダウンロードしました。」と手順が出る | NEW　Android（Gboard）に対応 |
| 12〜19 | 白地に4カテゴリの語数を順にカウントアップ。連盟用語に「鳳凰戦」、連盟プロに「白鳥翔」、Mリーグに「渋谷ABEMAS」を小さく添える（一般用語は例なし） | 連盟のタイトル戦・選手を充実／NEW　Mリーグのチーム・選手を追加 |
| 19〜21.5 | 全体の語数「全 N 語」（ページの説明文の語数と同じ数） | なし |
| 21.5〜25 | 締め: 白地に `img/ogp.png` の「ryoei.pro」が1秒で現れ、その後は動かない。最後のコマはロゴだけ | なし |

### 投稿文（決定。動画のファイルと一緒にログに書く）
```
リソース「辞書」をリニューアルしました。
https://ryoei.pro/resource_dictionary.html

・麻雀用語・連盟・Mリーグの1,870語をスマホやPCの変換に登録
・Android（Gboard）に新しく対応
・Mリーグのチーム・選手を追加、連盟のタイトル戦・選手を充実
```
（語数は手順1で測った全体の語数に合わせる）

### 前提（チャット側。平野さんの決定ではない）
- 平野さんが「辞書」タブを直した（「日本プロ麻雀連盟」を1つ消した）ので、撮る前に本番の辞書ページに反映させる。チャット側の見込み: 全体の語数は 1,870 のまま、一般用語 583 または連盟用語 138 のどちらかが 1 減る（要確認）
- 実画面の撮り方・テロップの位置（画面の上の端から 80〜210px、下の端から10%以上離す）・音量は型のとおり。押した後の画面は、型の「写真は読み終わるのを待って撮る」と同じく、表示が落ち着いてから撮る
- 制作のスクリプトは `scripts/promo_video/dictionary/`（実物の置き方に合わせてよい）に置き、既存の `scripts/promo_video/` の部品（曲・締め）を使う。動画はリポジトリに入れない
- 型の「次に作る時に考える点」の「X のサムネイルは動画の最初のコマ」は、今回の決定（最初のコマからタイトルをはっきり）で扱う

## 手順
1. 確かめる・反映する: `python3 scripts/generate_resource_dictionary.py --check` を回し、通ることとカテゴリごとの語数を報告する（止まれば止まる）。本番の辞書ページの語数が今のシートと違えば、`regenerate-page.yml` を入力 `resource_dictionary` で手動実行して反映し（全ページの再生成は帰り道の件で止まるため使わない）、本番の説明文の語数を確かめる。未マージの work/ ブランチが `scripts/promo_video/` の共有の部品を変えていないか確かめる。
2. 作る: 構成のとおり初版を作り、型の形式（mp4・H.264・yuv420p・AAC・-16 LUFS 前後）で書き出す。最初のコマと最後のコマ、各場面の代表のコマを画像で確かめる。テロップが実画面の見せたい所を隠していないこと、語数が手順1の値と合うことを確かめる。
3. 送る: mp4（ファイル名は `dictionary-promo-v1.mp4`）を `SendUserFile` で送る。ログに、各場面の秒・テロップ・語数・投稿文（語数を合わせたもの）を書き、判断待ちで止まる。

## 止まる条件
- `--check` が止まる、または手順1の反映で本番の語数がシートの語数と合わない
- 未マージの work/ ブランチが `scripts/promo_video/` の共有の部品（曲・締め・lib）の、使う箇所を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 実画面が型どおりに撮れない（ボタンを押した後の表示が撮れない等）。撮れた範囲と理由を報告する
- `regenerate-page.yml` の実行や cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
- マージは冒頭の「マージ:」の行のとおり（制作のスクリプトはマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #515・#522
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 447a0d65）: https://github.com/retroeater/mj-logs/tree/main/guide/447a0d65

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/447a0d65/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
