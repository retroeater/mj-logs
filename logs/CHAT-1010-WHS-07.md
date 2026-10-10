# CHAT-1010-WHS-07

- 着手日時: 2026-10-10
- 対象issue: #195
- ブランチ: work/1010-whs
- 着手時HEAD: 97467f48

## 指示

【Claude作成】Claude Code 向け指示：WHS-06 の続き。帰り道のヒーローの X のロゴを、選手の X のプロフィール画像（鳳凰戦のページと同じ出し方）に置き換え、名前との間を 8px にする。プレビューで止まる Chat-Ref: CHAT-1010-WHS-07 マージ: 判断待ちで止まる（平野さんがプレビューで見た目を確かめてから、別の指示でマージ） 貼る時機: いつでも（CHAT-1010-WHS-06 の判断待ちへの回答） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-whs を続けて使う（WHS-06 の変更に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1010-WHS-06.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。状態の末尾に `/ 続き: CHAT-1010-WHS-07` を足す。

目的
平野さんの「X のアイコン」は、X のロゴではなく、選手の X のプロフィール画像のことだった。WHS-06 で名前の右に置いた X のロゴを、プロフィール画像に置き換える。
決定（2026-10-10、平野さん）

* 一覧・各話のヒーローで名前の右に出すのは、選手の X のプロフィール画像（鳳凰戦などのページで出している写真と同じもの）。X のロゴではない
* 画像の高さは名前の文字の高さに合わせ、その選手の X のプロフィールにリンクする（リンクは画像だけ。名前はリンクにしない）
* 名前と画像の間は 8px にし、押せる範囲が名前の字にかからないようにする（WHS-06 の「判断が必要なこと」の (a)）
* 件数の表示を消したこと、X ID が無い選手は何も出さないこと、note を使わないことは WHS-06 のとおりでよい

前提（チャット側。平野さんの決定ではない）

* 写真の出し方は、鳳凰戦「順位変動」（`scripts/generate_houou_race.py`）が使っている `scripts/lib/x_images.py`（`with_size()`・`is_default_avatar()`）と、その写真 URL の読み元（「プロ」シートか SNS ブック〈#514、`scripts/lib/sns_book.py`〉。どちらかは実物で確かめる）を借りるのがよいと考えている（要確認）。大きさは文字の高さに見合う小さいもの（例 `SIZE_80`）にする。外部のドメインは今と同じ `pbs.twimg.com` だけで、増やさない
* 形は鳳凰戦などと同じ丸（`border-radius:50%`）を想定している。title/・houou/ などに丸い写真の部品・CSS があれば合わせる
* 写真の読み込みに失敗したときは、ほかのページと同じ `data-fallback`（`img/avatar.svg` など）で代わりの画像を出す。X ID はあるが写真の URL が無い・既定の卵型のときも、代わりの画像を出してリンクは付ける（X ID が無い選手は何も出さない）
* 画像の `alt` とリンクのアクセシブルネームは、WHS-06 の X のリンクと同じ文言（「<名前>さんのX」など）にする。`width`・`height` を指定してレイアウトのずれを防ぐ
* 一覧のヒーローは最新話の選手1人、各話は39話それぞれ。写真が「プロ」シート側で変わると、再生成のたびに差分が出る（ほかのページと同じ扱い）

手順

1. 確かめる: WHS-06 の状態と、`work/1010-whs` がリモートにあること。鳳凰戦（と title/・houou/ にあれば）の写真の出し方（読み元・大きさ・丸・代わりの画像・`data-fallback` の受け手の JS）を実物で読み、ログに書く。未マージのブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が、これから触るファイルの同じ行・同じ関数を変えていないか、取り込みで衝突しないかを確かめる
2. 直す: ヒーローの名前の右の X のロゴを、X のプロフィール画像のリンクにする（高さは名前の文字の高さ、間は 8px、押せる範囲が名前にかからない）。代わりの画像の受け手が帰り道のページに無ければ足す（共通の部品があれば使う）。テストを直す・足す。`docs/notes/video-wayhome.md`・`docs/notes/design.md` の WHS-06 で書いた所を直す。決定を `docs/decisions/` の WHS-06 と同じ分野に足す
3. 生成して止まる: 帰り道の2ページだけを生成し、差分を種類ごとに数えてログに書く。push して Cloudflare のプレビューを出す。平野さんが見るページは一覧・X ID がある回 `OoK3O2BCm8M`・X ID が無い回 `o28svvuVI0M`・（あれば）X ID はあるが写真が無い回。スマホ幅（390px）とパソコン幅のスクリーンショットで、写真の高さが名前の文字とそろい、折り返しで崩れず、押せる範囲が名前にかからないことを確かめ、ログに書く

止まる条件

* WHS-06 の状態が「判断待ち」でない。`work/1010-whs` がリモートに無い
* 写真の読み元が決められない（例: 「プロ」シートにも SNS ブックにも X の写真の URL が無い）
* 未マージのブランチが同じ行・同じ関数を変えている、または取り込みで衝突する
* ほかのページ（鳳凰戦・title/・jpml_pros など）の生成物が変わる（共通の部品を変える必要が出たら、変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-WHS-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-WHS-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1010-WHS-07"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- WHS-06 の `## 報告` の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-WHS-07` を足した
- 作業ブランチ: ローカル・リモートとも `work/1010-whs` は 97467f48。`origin/cloudflare` は祖先でない → 取り込む

## 報告

- 状態: 作業中
- ブランチ: work/1010-whs
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: なし
- マージ: 未
- issue: #195
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a930b4a9）: https://github.com/retroeater/mj-logs/tree/main/guide/a930b4a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
