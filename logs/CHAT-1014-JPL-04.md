# CHAT-1014-JPL-04

- 着手日時: 2026-10-14
- 対象issue: #523・#497（あわせて #488 の JPL-01 の片付け）
- ブランチ: work/1014-jpl
- 着手時HEAD: b0cfab082

## 指示

【Claude作成】Claude Code 向け指示：JPL-03 の続き。WRC 2大会の改称（「タイトル戦」タブだけ）の影響を先に確かめ、JPMLリーグ の行の確認と JPL-01 の片付けを行う（#523・#497） Chat-Ref: CHAT-1014-JPL-04 マージ: ドキュメントのみ（ログ・docs/decisions・docs/handover.md）なので完了報告のうえ cloudflare へ入れてよい。ただし、マージで動く自動再生成が今のシートで止まる・title/ などの生成物を変える見込みなら、マージせず判断待ちで止まる。コード・生成物は変えない 貼る時機: いつでも（平野さんの「タイトル戦」タブの変更は済んでいる）。JPL-03 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-jpl の使用と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-jpl を続けて使う（JPL-03 の中断のログがある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1014-JPL-03.md の `## 報告` を読み、状態が「中断」でなければ何もせず止まる。JPL-03 の `## 報告` の状態の末尾に `/ 続き: CHAT-1014-JPL-04` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#497・#523 が Open で、他セッションの着手中コメントが無いことを確かめ、両方に着手中コメントを残す。

目的
JPL-03 は「タイトル戦」タブに JPMLリーグ の行が無く止まった。平野さんが行を足し、あわせて WRC 2大会を改称した（「タイトル戦」タブだけ）。改称は #523（2026-10-09 に別課題とした件）にあたり、「タイトル」タブが旧名のままだと title/ の生成が止まるおそれがある（docs/notes/title-pages.md の「タイトル戦」タブの節、CHAT-0916-TT-14 の帝王戦の例）。生成が止まるかどうかと改称の影響を先に確かめ、JPL-03 の残り（JPMLリーグ の行の確認、JPL-01 の片付け）を行う。この指示ではシート・コード・生成物を直さない。
決定（2026-10-14、平野さん）

* 「タイトル戦」タブに JPMLリーグ の行を足した
* 「JPML WRCリーグ」を「WRCリーグ」に、「JPML WRC-Rリーグ」を「WRC-Rリーグ」に改称した。変えたのは「タイトル戦」タブの大会名だけ（「タイトル」タブ・「別名」タブなどは変えていない）
* 改称の影響を先に確かめる（チャット側の案 A を採った。改称を元に戻す案 B は採らない）。「タイトル」タブの旧名の行を直すか「別名」で寄せるかは、影響の洗い出しの後に決める

前提（チャット側。平野さんの決定ではない）

* JPL-01 の「判断が必要なこと」の扱い（チャット側の案。JPL-03 の前提と同じ）: (1) docs/handover.md 5章「次の会話の順番」の「#497／#488（連盟の予定表で JPMLリーグの「(仮)」が外れてから）」は、#488 を消し、#497 をこの指示の結果に合わせて直す（待ちなら待ちの理由に書き換える）(2) YouTube の題名の確かめは issue では追わず、チャット側がカレンダー「ryoei.pro」に 2027-03-08 の予定を作った（10/15 朝の【2】の確認の予定もある）
* チャット側が平野さんに出した JPMLリーグ の行の値の案: slug「jpml-league」、表示順 430、区分「連盟」、状態「継続」（実際の値は平野さんが決めた。要確認）
* 改称の影響が出そうな所の候補（#523 の本文にも候補があるはず。要確認。実物で足し引きする）: 「タイトル」タブの旧名の行（件数）、「別名」タブ、title/ の表示・slug（jwrc・jwrc-r は変わらない見込み）・OGP 画像 `img/ogp/title/<slug>-black.png`（大会名の文字）、`search.json`・`years.json`・sitemap、/live の大会名の照合（`broadcast_key()` など）、`yotei.EVENTS`（JPL-01 で足したテストの WRC の件名を含む）、houou/ など他ページの「JPML WRC」の表記
* 自動再生成（regenerate-page.yml など）は、cloudflare への push と毎朝の実行でシートを読み直す（要確認。docs/notes/github-actions.md）。改称で生成が止まるなら、次の毎朝の実行でも止まる

手順

1. 確かめる（読むだけ）: 「タイトル戦」タブを生成と同じ経路で読み、JPMLリーグ・WRCリーグ・WRC-Rリーグ の行の値（大会名・slug・表示順・区分・状態）をそのままログに書く。表示順の重なりも書く。「タイトル」タブの大会名ごとの行数で、旧名（JPML WRCリーグ・JPML WRC-Rリーグ）と新名の件数を書く
2. 生成を手元で走らせる（`python3 scripts/regenerate.py title_pages` など。書き込み・push はしない）。止まるならその出力をそのままログに引用する。止まらないなら、origin/cloudflare の生成物との差分を種類に分けて書く（JPMLリーグ が「記録の無い大会」に出るか、WRC 2大会の表示名・ページの増減・URL の変化）。次の毎朝の実行・cloudflare への push で同じことが起きる見込みと、その時刻（ワークフローの起動の表から）を書く。改称の影響を、上の候補を実物で確かめて表にする（場所・今の値・影響〈生成が止まる／表示が変わる／照合が外れる／影響なし〉・直し方の案）。「タイトル」タブを直す案と「別名」で寄せる案、その他の案を、メリット・デメリット・平野さんの手作業の要否つきで「判断が必要なこと」に書き、推奨と理由を書く。#523 に結果を要約してコメントする（本文・状態・ラベルは変えない。末尾に Chat-Ref の行）
3. JPMLリーグ の行（#497）と JPL-01 を片付ける: #497 にこの指示の結果をコメントする（本文・状態・ラベルは変えない。末尾に Chat-Ref の行）。#497 をこの時点でクローズしてよいか、記録が入るまで待つかの案（メリット・デメリット）と、記録が入った時点で要る作業（OGP 画像など）を「判断が必要なこと」に書く。docs/decisions/title.md に上の「決定」を足す。前提 (1) のとおり docs/handover.md を直し（今の内容を読んでから）、docs/decisions/broadcast-calendar.md の JPL-01 の節に前提 (2) の扱いを1行足す。docs/logs/CHAT-1014-JPL-01.md の `## 報告` の状態を「完了」にし、末尾に `/ 続き: CHAT-1014-JPL-04` を足す（「判断が必要なこと」の2点に、この指示で扱った旨を書く）。「マージ:」の行のとおり cloudflare へ入れる（入れる前に、マージで動く自動処理と、それが今のシートで止まらない・生成物を変えないことを確かめる）

止まる条件

* 0章で JPL-03 の状態が「中断」でない、#497・#523 が Closed、または他セッションの着手中コメントがある
* 「タイトル戦」タブに JPMLリーグ の行が無い・2行以上ある、または WRC 2大会の名前が決定と違う（値を書いて止まる）
* 手順2で生成が止まる、または生成物が変わる見込みで、cloudflare への push が自動再生成を動かす（手順1〜3の記録はログに書き、マージせず判断待ちで止まる。シート・コード・生成物は直さない）
* docs/ 以外を変える必要が出た
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が docs/handover.md 5章・docs/decisions/title.md・broadcast-calendar.md の同じ行・節を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-JPL-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-JPL-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- Chat-Ref: `CHAT-1014-JPL-04` のコミットは0件（同じセッションの続きなので識別子の重複確認は対象外）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある。「指示」欄の末尾は指示文の最後の行と一致
- ブランチ: `origin/work/1014-jpl`（144776ba1）はローカルと一致。`origin/cloudflare` が祖先でないため `git merge origin/cloudflare`（衝突なし、b0cfab082）
- JPL-03 の `## 報告` の状態は「中断（エラー）」→ 末尾に ` / 続き: CHAT-1014-JPL-04` を足した
- #497・#523 とも Open、他セッションの着手中コメント無し（#497 の JPL-03 の着手中はその中断コメントで解除済み）→ 両方に着手中コメントを残した
- 未マージの `work/*` で docs/handover.md・docs/decisions/title.md・broadcast-calendar.md を変えているのは `work/1014-jpl` 自身だけ

### 1. 「タイトル戦」タブ・「タイトル」タブ（生成と同じ gviz の経路、2026-10-14）

「タイトル戦」タブ 31行。該当の行（大会名・slug・表示順・区分・状態）:

| 行 | 大会名 | slug | 表示順 | 区分 | 状態 |
|---|---|---|---|---|---|
| 11 | JPMLリーグ | jwrc | 410 | 連盟 | 継続 |
| 12 | WRCリーグ | jwrc | 420 | 連盟 | 継続 |
| 13 | WRC-Rリーグ | jwrc-r | 430 | 連盟 | 継続 |

- JPMLリーグ は1行、区分・状態は前提どおり、WRC 2大会の名前は決定どおり → 止まる条件（行・名前）には当たらない
- **slug が JPMLリーグ と WRCリーグ で同じ「jwrc」**（チャット側の案は「jpml-league」）。表示順の重なりは無い（JPMLリーグ が 410 に入り、WRC 2大会が 420・430 へ1つずつ下がった）
- 「タイトル」タブ 3,110行の大会名ごとの行数: JPML WRCリーグ 80・JPML WRC-Rリーグ 32（旧名）、WRCリーグ 0・WRC-Rリーグ 0・JPMLリーグ 0（新名）。
  「タイトル」タブは大会名のほかに「タイトル」列（例「第1期JPML WRCリーグ」）も大会名・期と照合される

### 2. 生成（手元、書き込み・push なし）

`python3 scripts/regenerate.py --check title_pages`（ファイルは書かない、#551）: **止まる（105件）**。出力（「タイトル」タブの104件は同じ形なので先頭と末尾だけ）:

```
検査: 止まります(105件)
- slug が重複しています: jwrc
- 「タイトル」タブ: 113行目: 大会名が「タイトル戦」にありません: 'JPML WRCリーグ'
- 「タイトル」タブ: 114行目: 大会名が「タイトル戦」にありません: 'JPML WRCリーグ'
…
- 「タイトル」タブ: 1263行目: 大会名が「タイトル戦」にありません: 'JPML WRCリーグ'
検査の結果(1ページ):
- title_pages: 止まる(--check)
```

`python3 scripts/regenerate.py --check live_pages`: **止まる（1件）**。/live も同じ「タイトル戦」タブを読み（`scripts/lib/live.py` の `parse_titles()`）、slug の重複で例外にする:

```
検査: 止まります(1件)
- 読み込み: ValueError: 「タイトル戦」シートの値が不正です: slugの重複「jwrc」
```

`scripts/generate_ask_data.py` も同じ `load_titles()` を使うため、同じく止まる見込み（実行していない）。

**追加の確かめ（読み込んだ値を手元のメモリ上で差し替えて組み立て。シート・ファイルは変えていない。スクリプトは scratchpad）:**

- 改称前の再現（「タイトル戦」タブの WRC 2大会を旧名に戻し、JPMLリーグ の行を外す）: title/ は 384ページで、リポジトリの title/ と**全ページ一致**。/live は検査が通り 908ページ・公開する動画 2,918本。比較の基準にした
- **slug だけ直した場合**（JPMLリーグ を jpml-league。WRC 2大会は新名のまま）:
  - title/: 止まる（「タイトル」タブの旧名104件）
  - /live: **検査は通る（止まらない）が、742ページ・2,593本に減る。** 【3】【2】の「タイトル戦」列が旧名の 325行（JPML WRCリーグ 223・JPML WRC-Rリーグ 102）が「タイトル戦が『タイトル戦』シートに無い」で公開されず、`/live/jwrc/`（111）・`/live/jwrc-r/`（55）の 166ページが消える（「転送先の無い URL(404 になる)」の警告だけ）
- slug を直し、さらに「タイトル」タブの大会名とタイトル列も新名にした場合（案1の想定）:
  - title/: 止まらない。384ページで増減・URL の変化なし。変わるのは 30ファイル（jwrc 20・jwrc-r 8・入口 index.html・search.json）で、表示名「JPML WRCリーグ」→「WRCリーグ」（title・description・og・パンくず・h1）。
    **期ページの放送が外れる**: `broadcast_key()` が（大会名, 期）で【3】【2】の旧名と突き合わせるため、jwrc の期ページの YouTube へのリンクが 49→0、jwrc-r が 19→0
  - 記録の無い大会: 「JPMLリーグ」「鳳匠戦」（今は「鳳匠戦」だけ）。JPMLリーグ のページは作られない
  - /live: 上と同じく 166ページ・325本が消える（/live は「タイトル」タブを読まず、【3】【2】の旧名と「タイトル戦」タブを突き合わせるため）

**次の自動実行で起きる見込み**（docs/notes/github-actions.md・workers/scheduler/schedule.json・各ワークフローの `on`）:

| 実行 | 時刻（JST） | 見込み |
|---|---|---|
| update-live-channel.yml（Worker `mj-scheduler` の起動 / 保険の cron 21:43 UTC） | 毎朝 04:00 / 06:43。次は 2026-10-15 | ジョブ regenerate（live_pages・title_pages）で両方が止まり、`regenerate.py` が飛ばす（途中の出力は戻す）。成功したページが無いのでコミットなし、ジョブは「飛ばしたページを報告」で失敗。**/live・title/ は今の版のまま更新されない**（新しい放送が /live に出ない） |
| regenerate-page.yml の週次（cron 20:37 UTC 日曜） | 2026-10-19（月）05:37 | 全ページのうち title_pages・live_pages（と ask の見込み）を飛ばし、残りをコミットしてジョブは失敗 |
| cloudflare への push | — | `scripts/generate_*.py`・`scripts/lib/**`・`*.js` を変える push でだけ regenerate-page.yml が動く。この指示の docs のみの push では動かない |

### 改称の影響（実物で確かめた範囲）

| 場所 | 今の値 | 影響 | 直し方の案 |
|---|---|---|---|
| 「タイトル戦」タブ JPMLリーグ の slug | jwrc（WRCリーグ と重複） | **生成が止まる**（title/・/live・ask） | 「jpml-league」に（平野さんのシート作業、1セル） |
| 「タイトル」タブ 大会名・タイトル列 | 旧名 112行（照合で止まるのは104行） | **title/ の生成が止まる** | 案1〜3（下の「判断が必要なこと」） |
| 【3】【2】（/live のシート）の「タイトル戦」列 | 旧名 325行 | slug を直すと**照合が外れる**（/live の 166ページ・325本が消え、title/ の期ページの放送 68件が外れる）。止まらないので気付きにくい | 旧名のまま照合できるようにする（コード）か、【3】と抽出の規則（`live_extract.py`）を新名にする |
| `scripts/lib/live_extract.py` の大会名の対応（`"JPML WRCリーグ": r"JPML\s*WRCリーグ|WRCリーグ|WRC"` など） | 旧名を出力 | 新しい【2】の行も旧名で作られ続ける（照合が外れる側に増える） | #523 の実装で新名にするか、旧名→新名の寄せを持つ |
| `scripts/lib/wayhome_intake.py` の `TAIKAI_ALIASES`（`"JPMLWRC": "JPML WRCリーグ"`） | 旧名 | 「タイトル」タブを新名にすると、帰り道の新しい回の期の引き当てが外れる見込み（未実行） | #523 の実装で直す |
| title/ の slug・URL（jwrc・jwrc-r） | jwrc・jwrc-r | 影響なし（JPMLリーグ の slug を変えれば） | — |
| OGP 画像 `img/ogp/title/jwrc-black.png`・`jwrc-r-black.png` | 大会名「JPML WRCリーグ」などの文字が焼き込み（ファイル名から。画像の中身は目視していない） | 表示名を変えると**表示が変わる**（og:title と画像の文字が食い違う） | 作り直すなら名前を変える（docs/notes/ogp.md） |
| `title/search.json`・`years.json`・`sitemap-title.xml` | — | 案1の想定で search.json の表示名だけ変わる。sitemap・years は増減なし | 再生成で追随 |
| `scripts/lib/yotei.py` の `EVENTS`（`("WRCリーグ", "JPML WRCリーグ")`）とテスト | 旧名 | 影響なし（予定表の件名と YouTube の題名の両方を同じ規則で当てるだけで、「タイトル戦」タブと照合しない） | 表記をそろえるなら #523 で |
| 手書きのページ・メタ（`apply_page_meta.py`・`llms.txt`・`index.html`・`wrc_ranking.html`・`wrc_results.html`・`rh_results_detail.html`・`video_live.html`） | 「JPML WRC」 | 影響なし（シートを読まない）。表記がそろわないだけ | #523 で |
| 「別名」タブ（変換前・変換後・区分「登録名変更」15・「訂正」13） | 人の名前だけ | 大会名には効かない（`build_aliases()` は名前にだけ適用）。**「別名」で大会名を寄せる案はコードの変更が要る** | — |

## 報告

- 状態: 判断待ち / 続き: CHAT-1014-JPL-05
- ブランチ: work/1014-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1014-jpl/docs/logs/CHAT-1014-JPL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-jpl
- 確認用URL: なし
- マージ: 未（止まる条件「手順2で生成が止まる」に当たったため。docs の変更〈handover.md・decisions・JPL-01/03 のログ〉は作業ブランチにある）
- issue: #523・#497（結果のコメントのみ。本文・状態・ラベルは変えていない）
- 判断が必要なこと:
  - 今のシートのままだと、次の毎朝（10-15 04:00 JST）から /live・title/ の再生成が止まり、ジョブが失敗し続ける（/live に新しい放送が出ない）。直すまでの扱いを決めてほしい
  - JPMLリーグ の slug: 「jwrc」が WRCリーグ と重複。案「jpml-league」（既存の小文字・ハイフン区切り〈pro-queen・nihon-series〉に合わせ、jwrc の略し方と紛れない）。ただし改称を残したまま slug だけ直すと、/live は止まらずに WRC 2大会の 166ページ・325本を消す（気付きにくい）ので、改称の扱いと同時に直すこと
  - 改称（#523）の扱いの案:
    - 案1「タイトル」タブを新名に直す（大会名と「タイトル」列 112行）: title/ は通る。デメリット: /live（【3】【2】と抽出の規則が旧名）で 166ページが消え、title/ の期ページの放送 68件が外れる。直すには【3】の旧名 325行の書き換えと `live_extract.py`・`wayhome_intake.py` の変更（コード）が要る。平野さんの手作業: 大
    - 案2「別名」で寄せる: 「別名」タブは人の名前用で、大会名には効かない。title/・/live の両方に大会名の別名を読む変更（コード）が要る。平野さんの手作業: 別名の2行だけ。デメリット: コードの変更と設計（どの列に・どの向きに寄せるか）が先に要る
    - 案3「タイトル戦」タブに旧名を残して表示名だけ変える仕組みを作る（例: 「タイトル戦」タブに「表示名」列を足し、照合は旧名、表示は新名）: 照合が外れない。デメリット: コードの変更（title/・/live の表示）と OGP 画像の作り直し。平野さんの手作業: 列の追加と2セル
    - 案4（暫定）「タイトル戦」タブの WRC 2大会の大会名を旧名に戻し、JPMLリーグ の slug を直す: 今日のうちに生成が今の版どおりに戻る（改称前の再現で title/ 384ページが一致、/live 908ページ）。デメリット: 決定で採らないとした案 B と同じ形になる（改称は #523 の実装まで保留）。平野さんの手作業: 3セル
    - 推奨: **案4で生成を戻し、#523 は案3で実装する。** 理由: 案1・2・3はどれもコードの変更が要り、それまで毎朝の /live・title/ の更新が止まる。案3は過去の YouTube 概要欄・【3】の旧名をそのまま照合に使えて、#523 の本文の懸念（過去の紐づけ）に合う。案4は決定（案 B を採らない）を見直すことになるので、平野さんの判断が要る
  - #497 をこの時点でクローズするか: (i) クローズ（「タイトル戦」タブに行があり、記録の無い大会として扱われることは確かめた。記録が入ったときの作業は新しい issue かカレンダーで追う）: 残る issue が減る / 記録が入る 2027-03 の作業を忘れやすい。(ii) 記録が入るまで待つ: 決勝後の作業を同じ issue で追える / 半年近く Open のまま。推奨は (i)（slug を直した後）。記録が入った時点で要る作業: 「タイトル」タブに第1期の行（表示=Y）を足す、OGP 画像 `img/ogp/title/jpml-league-black.png` を作る（docs/notes/ogp.md）、`_redirects` に `/title/jpml-league  /title/jpml-league/  301` を足す（`test_title_redirects.py` が要求、#455）、/live の【3】で決勝段階の放送を JPMLリーグ に当てる（`live_extract.py` に大会名の対応を足す）、title/ の入口・sitemap に出ることを確かめる
- 未確認の項目:
  - 10-15 04:00 JST の update-live-channel.yml の regenerate ジョブが、見込みどおり title_pages・live_pages を飛ばして失敗するか
  - `generate_ask_data.py` が slug の重複で止まるか（実行していない）
  - OGP 画像 jwrc-black.png・jwrc-r-black.png の中の文字（目視していない）
- エラー: 手順2で生成が止まる（title_pages: slug の重複と「タイトル」タブの旧名104行、live_pages: slug の重複）。止まる条件に当たったため cloudflare へマージしていない

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 44920ebb）: https://github.com/retroeater/mj-logs/tree/main/guide/44920ebb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/44920ebb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/44920ebb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/44920ebb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/44920ebb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/44920ebb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/44920ebb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a31620fe.md
