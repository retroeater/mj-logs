# CHAT-1018-OUK-10

- 着手日時: 2026-10-18
- 対象issue: #519・#597
- ブランチ: work/OUK（CHAT-1018-OUK-09 の続き）
- 着手時HEAD: bed168eea

## 指示

【Claude作成】Claude Code 向け指示：「入替戦」列を読む形にし（houou/・ouka/）、順位変動の名前を今の登録名に、桜花のランキングを8部門にしてプレビューで止まる Chat-Ref: CHAT-1018-OUK-10 マージ: 判断待ちで止まる（平野さんがプレビューで確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1018-OUK-09 と同じセッションでよい。平野さんは「桜花」の「入替戦」列〈G列〉と「結果」、「鳳凰」の「入替戦」列〈I列〉を貼り済み。「鳳凰」の「結果」の12セルはまだ「入替戦」のまま） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/OUK の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/OUK を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない。work/WRC が work/OUK から分かれて merge で取り込むため）。取り込みの衝突は、houou/ の振る舞いを cloudflare 側を正として解く（OUK-08 と同じ考え方。解いた箇所をログに書く）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-OUK-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1018-OUK-10` を足す。 続いて「桜花」「鳳凰」タブを生成と同じ経路で読み、「入替戦」の見出しの列があること、Y の件数（桜花49・鳳凰12）、「桜花」の「結果」に「入替戦」が無いこと、「鳳凰」の「結果」の「入替戦」が12行であることを確かめる。違えば止まる。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。止まるのは「止まる条件」に当たるときだけ。

目的
OUK-08 のプレビューの動作確認と仮置きの確認で決まったことを入れる。「結果」列は鳳凰戦・桜花とも昇級・残留・降級・決定戦の4語にそろい、入替戦に出たかどうかは「入替戦」列（Y/N）で持つ形になったので、生成をその形に合わせる。プレビューで止まる。
決定（2026-10-18、平野さん）

* 順位変動の表の名前は今の登録名にする（別名を通す。houou/race/ も同じく変える）
* 「桜花」「鳳凰」に「入替戦」列（Y/N）を足した。「結果」が「入替戦」だった人は、入替戦の結果で「昇級」「残留」「降級」にした（「結果」の評価を鳳凰戦と統一し、入替戦かどうかは「入替戦」列で残す）。「鳳凰」の「結果」の12セルは ouka/ のマージのときに平野さんが直す
* 「入替戦」が Y の期は、結果のチップの横に小さな紫（#6741d9、今の入替戦のチップの色）の「入替戦」のチップを並べる
* 桜花のランキングの「連続昇級」の部門は消す（8部門）。鳳凰戦は9部門のまま
* 見た目の値（期の表記「20期」「第20期」、帯の出し方、色、最近見た選手が無いときの1件、OGP 画像）は OUK-08 のままでよい

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 「入替戦」のチップを出す所: 結果のチップが出る所すべて（個人成績の期別のカード・通算のグラフの吹き出し・リーグのタブの吹き出し・質問欄の選手カードなど）。結果のチップの右に並べ、狭い画面で折り返す・はみ出す所が無いかを確かめる。houou/ にも同じく入る
* 移る間の扱い: 「鳳凰」の「結果」の12セルは、マージまで「入替戦」のまま。「結果」が「入替戦」の行は今までどおり紫の「入替戦」のチップ1つにし、「入替戦」列の Y で2つ目を出さない（二重にしない）。12セルを直した後は、結果のチップ＋「入替戦」のチップになる。どちらの状態でも生成が通るようにし、テストで両方を確かめる
* 順位変動の入替戦の帯: 「結果」の値ではなく「入替戦」列の Y から出す（上下の判定は今の `playoff_bands()` の考え方のまま）。帯の文字は「入替戦」
* 連続昇級の数え方: 「昇級」の行を数える（入替戦で上がった人も「昇級」になったので数えに入る）。鳳凰戦のランキングの値が変わる場合は、変わる人と値をログに書く
* 桜花の8部門: 部門の格子は1枠空く。並べ方は仮置き（例: 広い画面で4列×2段、狭い画面は今の列数のまま）。トップの説明の「9部門」を「8部門」に。Worker の桜花の部門（`ask.mjs` など）も8部門に
* 質問欄のデータ（`data.json`・`data-ouka.json`）: 「入替戦」の Y を行に持たせ、答えで「入替戦に出た」と言えるようにする（仮置き。型と答え方を書く）
* 順位変動の名前: 別名を通した今の登録名で表に出す。名前を変えた人の一覧（大会・期・リーグ・シートの名前 → 今の名前）をログに書く
* WRC との関係: work/WRC は work/OUK から分かれ、houou/・ouka/ にも効く直し（期別のカードの配置〈`style.css`〉・部門別のタブの「規定未達」〈`generate_houou_pages.py` の `player_divisions()`〉など）を持っている（CHAT-1018-WRC-07〜10 のログの報告「OUK への引き継ぎ」）。この指示で同じ関数・同じ CSS の節を変えたら、どこかをログの報告に「WRC への引き継ぎ」として書く。work/WRC のファイルは直さない
* `.github/workflows/` は変えない（check-assets の許可リストの `ouka` は work/WRC にも入っている。ouka/ のマージの指示で扱う）

手順

1. 取り込み（必要なら）と「入替戦」列: 生成が「入替戦」列を読む形にする（houou/・ouka/・順位変動・質問欄のデータ）。チップ・帯・連続昇級を上の前提で直す。順位変動の名前を今の登録名にする。桜花の部門を8にする。テストを合わせて足す（移る間の両方の状態、二重にしないこと、8部門）
2. 確かめ: `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、houou/ の生成物の差分が「改名した JS を指す行」「順位変動の名前の変わった行」「入替戦のチップ・帯の作りが変わった行」「連続昇級の値（変わる場合）」だけであることを確かめ、種類ごとに件数と例を書く。ほかの生成物（旧 `houou_*`・title/・jpml_pros など）は差分0
3. 見た目: 375px・1280px・1920×1080、字の大きさ 150%・200% で、ouka/ の個人成績（天音まこと〈入替戦で昇級〉・二階堂瑠美〈入替戦で降級〉・伊達朱里紗〈入替戦で残留〉・樫野凪〈名前を変えた人〉）、ouka/ のランキング（8部門の格子）、ouka/ の順位変動（第20期 C1・第18期 A）、houou/ の個人成績（仁平宣明〈「結果」が「入替戦」のままの人〉）、houou/race/ で名前を変えた人がいる表を headless Chrome で見て書く。プレビュー（確認用 URL）を出す。#597・#519 に進み具合を1件ずつコメントする（末尾に `Chat-Ref: CHAT-1018-OUK-10`）。`docs/notes/ouka-top.md`・`docs/notes/houou-top.md` を今の形に直す

止まる条件

* 0章の確かめが合わない
* houou/ の生成物に、手順2の4種類以外の差分が出て直しで消せない。ほかの生成物に差分が出て直しで消せない
* テスト（scripts と Worker）が落ちて直せない
* 未マージの work/ ブランチ（work/OUK と、work/OUK から分かれた work/WRC 以外）が、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、新しく仮置きしたこと（8部門の格子の並べ方、「入替戦」のチップの置き方、質問欄の答え方など）を、(1) 見た目の値 と (2) 見ただけでは判断しにくいもの に分けて書く。「WRC への引き継ぎ」（work/WRC と同じ関数・CSS の節を変えた所）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（上の手順3のページ）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OUK-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OUK-10 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章

- Chat-Ref の重複なし。work/OUK はリモートとローカルが同じ（bed168eea）。「指示」欄の末尾は指示文の最後の行と一致。CHAT-1018-OUK-09 のログの状態に ` / 続き: CHAT-1018-OUK-10` を足した。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 生成と同じ経路（`lib/sheets.fetch_table`）で「桜花」「鳳凰」を読んだ:
  - 「桜花」1,719行。見出し: 名前・期・リーグ・リーグキー・順位・結果・入替戦（G列）・合計・第1〜8節・表示・備考。「入替戦」は Y 49・N 1,670（OUK-09 の `ouka_playoff.tsv` と全行一致）
  - 「桜花」の「結果」: 残留 1,029・降級 226・昇級 197・決定戦 60・**入替戦 49**・空欄 158（OUK-09 の前の値のまま）。「入替戦」が Y の49行は全部「結果」が「入替戦」。OUK-09 の `ouka_results.tsv` と違う行は、その49行（153行 後藤咲・160行・161行・162行・182行…）
  - 「鳳凰」16,011行。「入替戦」（I列）は Y 12・N 15,999（OUK-09 の `houou_playoff.tsv` と全行一致）。「結果」の「入替戦」は12行（指示どおり）
- 念のため CSV の経路（`fetch_csv`）でも「桜花」を読み直した。「結果」の件数は同じ（入替戦 49）
- 止まる条件「0章の確かめが合わない」に当たる: 指示の「『桜花』の『結果』に『入替戦』が無いこと」が合わない。origin/cloudflare の取り込み・生成のコードの変更は始めていない

## 報告

- 状態: 中断（0章の確かめが合わないため、作業を始めずに止まった） / 続き: CHAT-1018-OUK-11
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1018-OUK-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/OUK
- 確認用URL: なし
- マージ: 未（作業を始めていない）
- issue: なし（#597・#519 へのコメントは、作業を始めていないため書いていない）
- 判断が必要なこと:
  - 「桜花」の「結果」列（F列）に、OUK-09 の `ouka_results.tsv`（https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1018-OUK-09/ouka_results.tsv ）がまだ貼られていないように見える。「入替戦」列（G列）は貼られている。F2 から貼った後（「結果」に「入替戦」が0行、Y の49行が昇級25・残留18・降級6）に、この指示を貼り直すか、続きの指示がほしい
  - 「鳳凰」は指示どおり（「入替戦」列 Y 12、「結果」の「入替戦」12行）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5a267bc8）: https://github.com/retroeater/mj-logs/tree/main/guide/5a267bc8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
