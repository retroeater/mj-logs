# CHAT-1018-OUK-08

- 着手日時: 2026-10-18
- 対象issue: #519・#597・#141
- ブランチ: work/OUK（CHAT-1018-OUK-06 の続き。CHAT-1018-OUK-05・07 は欠番）
- 着手時HEAD: b852809

## 指示

【Claude作成】Claude Code 向け指示：ouka/ に origin/cloudflare（#541 の通期・AMP）を取り込み、桜花を通期で扱い、「結果」列を使う形にしてプレビューで止まる Chat-Ref: CHAT-1018-OUK-08 マージ: 判断待ちで止まる（平野さんがプレビューで確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: 平野さんが CHAT-1018-OUK-06 の TSV を「桜花」シートの「結果」列に貼り、第6期 B の5人を手で直した後（OUK-06 と同じセッションでよい）。0章で確かめる。CHAT-1018-OUK-05・07 は送る前に差し替えたため欠番 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/OUK の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/OUK を続けて使う（OUK-01〜04・06 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みの衝突の解き方は下の「前提」の「取り込み」のとおり。生成物でない文書の衝突で、両方の変更が両立するもの（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-OUK-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1018-OUK-08` を足す。 続いて「貼る時機」を確かめる: 「桜花」タブの「結果」列を生成と同じ経路で読み、OUK-06 の `ouka_results.tsv` と行ごとに比べる。違うのが第6期 B の5行（8位 赤司美奈子「降級」→「残留」、32位 金澤えり・33位 菅原夏実・35位 山口やよい は空欄 →「残留」、42位 高瀬真濃 は空欄 →「降級」）だけでなければ止まる（違う行を全部書く。「結果」列が空なら何もせず止まる）。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。止まるのは「止まる条件」に当たるときだけ。

目的
OUK-01 で作った ouka/ を、cloudflare に入った鳳凰戦の変更（#541 の通期〈HOU-70・71〉、進行中の期の扱い〈AMP-09・10〉）の上に載せ直し、桜花を通期で扱う。平野さんが埋めた「結果」列で、結果のチップ・順位変動の帯・ランキングの「連続昇級」を出す。プレビューで止まる。
決定（平野さん）

* 2026-10-17: 順位変動の URL の期は `?term=20`（`-2` を付けない。旧形式も受ける）／`regenerate.py --changed` は共通の生成スクリプトの変更で ouka/ も選ぶよう直す／ouka/ も別名を通して今の登録名で表示・集計する（`?name=<旧名>` でも今の名前で1人に。MCK からの伝言、2026-10-15 の案C）／旧 `ouka_*.html` も転送で消えるまでは「女流桜花」の OGP 画像にする（OGR からの伝言）
* 2026-10-17（AMP、houou/ と同じ扱い）: 個人成績の期別の先頭に進行中の期の行（期とリーグ・Ampai へのリンク、案内文なし）／名前の横のチップは進行中の期のリーグ／進行中の期だけに出ている選手も個人成績に載せ、ほかのタブは「データがありません」／質問欄の選手カードのチップも名前の横と同じ決め方
* 2026-10-18: 「桜花」の「結果」列は平野さんが OUK-06 の TSV で埋めた（値は 昇級・降級・残留・入替戦・決定戦・空欄。次の期に出ていない人は前後の順位の人の昇降級から補った。第6期 B は 8位 赤司美奈子の第7期 C1 への移りを成績による降級とみなさず「残留」とし、32・33・35位を「残留」、42位を「降級」で補った）。特別昇級はリーグの成績での結果を残す（鳳凰戦も同じ仕様）
* OUK-01 の決定・仮置きの回答（OUK-01 のログと #519）は変わらない

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 取り込み: work/OUK は HOU-70 より前に分かれている。衝突は `scripts/generate_houou_pages.py`・`scripts/generate_houou_race.py`・`scripts/lib/results.py`・`workers/ask/src/query.mjs`・`assets/houou.js`（work/OUK では `assets/league.js` に改名済み）・`assets/houou_race.js`（同 `assets/league_race.js`）などで出る見込み。houou/ の振る舞いは cloudflare 側を正とし（HOU-70・71 のログ「2. 直した所」、`docs/notes/houou-top.md`「通期のリーグ」、AMP-09・10 のログ「OUK への引き継ぎ」）、その上に OUK-01 の大会のパラメータ化と JS の改名を載せ直す。`assets/houou.js` に入った直し（`NO_FINISHED_NOTE`・`renderPlayer()`・`SPAN_HALF`・`inTerm()` など）は `assets/league.js` に入れる
* 桜花の期: 前期・後期が無いので、全リーグを通期として扱う（期の番号 0、順位変動の期のキー `<期>-0`、URL `?term=<期>`）。OUK-01 の「全行を後期として扱う」読み替えはやめる。houou/ の `term_half()` などの通期の仕組みに大会の設定（例: 全リーグが通期）で乗る形を仮置きで選ぶ
* 「結果」列: houou/ と同じく、結果のチップ（期別・通算のグラフの丸・リーグのタブの吹き出し）、順位変動の昇級・降級・入替戦・決定戦の帯、ランキングの「連続昇級」（9部門目）を出す。部門の格子は 3段×3列で埋まる見込み。空欄の行は結果なし（チップなし）
* 決定戦の帯: houou/ の A1 と同じく、桜花の A の「決定戦」の人数で帯を出す
* 名寄せ: 「別名」で樫野凪 → かしのなぎ の行（MCK の数えでは「桜花」に3行、OUK-01 の生成では2行）。数え方の違いの理由を書き、`ouka/players/?name=樫野凪` で「かしのなぎ」が開くことを確かめる。済んだら #141 に桜花の側の記録を1件コメントする（WRC のチャットが追うため）
* 旧 `ouka_results.html`・`ouka_leagues.html`・`ouka_ranking.html` の og:image を `img/ogp/ouka/index-white.png`（OUK-01 で作成済み）に替える（houou/ と旧 `houou_*.html` と同じ扱い。中身は変えない）
* `.github/workflows/` は変えない（`check-assets.yml` の許可リストへの ouka の追加は、ouka/ のマージの指示で行う。2026-10-17 の決定）

手順

1. 取り込みと通期: origin/cloudflare を取り込み、上の前提で衝突を解く（解いた箇所をファイルごとにログに書く）。桜花を通期で扱う形にし、`?term=<期>` を受ける（旧形式 `?term=<期>-2` も受ける）。AMP の扱い（進行中の期の行・チップ・新人・質問欄のカード）を ouka/ に入れる。`regenerate.py --changed` を直す。テストを合わせて足す
2. 「結果」列と OGP: 「結果」列を読む形にし、チップ・帯・連続昇級の部門を出す。旧 `ouka_*.html` の og:image を替える。名寄せを確かめる。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、houou/ の生成物の差分が改名した JS を指す行だけ、旧 `houou_*`・title/・jpml_pros などが差分0、旧 `ouka_*` は og:image の行だけであることを確かめる
3. 確かめと記録: 375px・1280px・1920×1080、字の大きさ 150%・200% で、トップ・個人成績（清水香織・二階堂亜樹・進行中の期だけの選手1人・特別昇級の4人のうち1人）・ランキング（通算・連続昇級）・順位変動（`?term=20&league=A`）を headless Chrome で見て書く。リーグのタブの再生のアイコンの座標（houou-top.md「確かめ方」）も書く。プレビュー（確認用 URL）を出す。#597 と #519 に進み具合を1件ずつコメントし、#141 に名寄せの記録をコメントする（末尾に `Chat-Ref: CHAT-1018-OUK-08`）。`docs/notes/ouka-top.md` を今の形に直す

止まる条件

* 0章の「貼る時機」が満たされない
* 取り込みの衝突のうち、cloudflare 側を正とすると houou/ の振る舞いを変えないと ouka/ が作れないもの（解かずに、どの関数のどこかを書いて止まる）
* houou/ の生成物に、改名した JS を指す行以外の差分が出て直しで消せない。ほかの生成物（旧 `houou_*`・title/・jpml_pros・`workers/ask/data.json` など）に差分が出て直しで消せない
* テスト（scripts と Worker）が落ちて直せない
* 未マージの work/ ブランチ（work/OUK 以外）が、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、新しく仮置きしたこと（通期の扱いの作り、帯・チップの出し方など）と、OUK-01 の仮置きの一覧のうち今も残るものを、(1) 見た目の値 と (2) 見ただけでは判断しにくいもの に分けて書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（トップ・個人成績〈清水香織・二階堂亜樹・進行中の期だけの選手〉・ランキングの通算と連続昇級・順位変動）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OUK-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OUK-08 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1018-OUK-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/OUK
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c7ecedbb）: https://github.com/retroeater/mj-logs/tree/main/guide/c7ecedbb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7ecedbb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7ecedbb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7ecedbb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7ecedbb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7ecedbb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7ecedbb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e2580c91.md
