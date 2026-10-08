# CHAT-1009-SWP-04

- 着手日時: 2026-10-08
- 対象issue: なし
- ブランチ: work/1009-swp-race
- 着手時HEAD: a6988a56

## 指示

【Claude作成】Claude Code 向け指示：横断レビューの追加 — 鳳凰戦「順位変動」（houou_race）を同じ5観点で点検する（読むだけ）
Chat-Ref: CHAT-1009-SWP-04
マージ: ドキュメントのみ（docs/logs/・docs/decisions/）の変更なので、完了報告のうえ cloudflare へ入れてよい。それ以外のファイルを変える必要が出たら、マージせず判断待ちで止まる
貼る時機: いつでも（新しいセッションに、この指示だけを貼る）
作業ブランチ: クラウドセッションで実行する。work/1009-swp-race を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-swp-race origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-race の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

CHAT-1006-SWP-01（サイト全体の横断レビュー）で未公開のため対象外にした `houou_race.html`（鳳凰戦「順位変動」）が公開されたので、同じ観点で点検し、指摘の一覧をログに書く。何も直さず、issue も起票しない。

### 決定（2026-10-09、平野さん）

- `houou_race` は公開されたので、横断レビューの対象に含める

### 前提（チャット側。平野さんの決定ではない）

- 観点と、指摘1件ごとに書く項目は SWP-01 と同じ。CHAT-1006-SWP-01 のログの「### 手順2 サブエージェントの依頼文（5本）」の共通部（「指摘1件ごとに書く項目」など）と G1〜G5 の個別部を読み、そのまま使う。1ページなので、サブエージェントは使わなくてよい
- 「挙げなくてよいこと」には、SWP-01 の一覧に加えて、このページの見せ方の決定（docs/decisions/houou.md、docs/notes/houou-race.md）を入れる。決めたとおりの見た目・動き（帯のすべりこみ、カウントアップ、▲の表記など）は指摘にしない
- 鳳凰戦の新ページ群 houou/（#518、work/1008-hou は未マージ）で、順位変動は `houou/race/` に移り、旧 URL は公開の段で 301 になる予定（docs/decisions/houou.md による。要確認）。そのため、各指摘の行き先は「現行で直す」「houou/ の移設で扱う（#518）」「新サイトの要件」「見送り」のどれかにする
- サイト全体に共通する指摘（共通ナビ・スキップリンクなど、SWP-01 の G1-01・G1-02・G2-02・G2-03・G2-04・G4-01）は、このページでも出るかだけを書き、新しい指摘にしない
- 表示の確認は、作業ツリーを `python3 -m http.server` で配信し、Playwright の Chromium で開く（SWP-01 と同じ）。本番（ryoei.pro）はブラウザで巡回しない。データの JSON の取得失敗・0件などの状態は、手元の配信で応答を差し替えて確かめる
- 使う skill は無い

## 手順

1. 確かめる: CHAT-1006-SWP-01 のログの上の節、docs/decisions/houou.md、docs/notes/houou-race.md を読む。`houou_race` が公開されていること（navbar・sitemap・`llms.txt` に載り、noindex が無い）を確かめる。未マージの work/1008-hou で順位変動がどう扱われているか（移設先・旧 URL）をブランチのログと差分で確かめる（読むだけ）
2. 点検する: 幅 1280px・390px・360px、文字 200%（ルートの文字サイズを 200% にする近似。手段を書く）、キーボードだけの操作、選ぶ部品（期・前後期・リーグ・組）の hover・押下・フォーカス・無効の見た目、再生中・停止・節のラベルを押したとき、JSON の取得失敗・データの無い組み合わせ、title・description・h1・OGP・favicon
3. 一覧をログの `## 経過` に表で書く（SWP-01 と同じ列、通し番号は R-01 の形）。「現行で直す（小）」と重要と判断したものは実物で再現を確かめ、確かめていないものには「未検証」と書く。実機（iPhone の Safari）で確かめるべきものには、平野さんがそのまま開ける本番の URL と見る点を1行で書く。`## 報告` の「判断が必要なこと」に、平野さんが決めること（どれを現行で直すか、houou/ の移設で扱うもの）を書く

## 止まる条件

- `houou_race` が公開されていない（noindex がある、navbar に無いなど）
- 表示の確認の手段（http.server と Playwright の Chromium）がこのセッションで動かない（手段を入れ替えず、試したことと結果を書いて止まる）
- docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- 何も直していない・issue を起票していないこと（`git diff origin/cloudflare --stat` が docs/logs/・docs/decisions/ だけであること）を確かめて報告に書く
- 画面写真や中間ファイルはコミットしない
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-08 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-04"` に該当なし。識別子 SWP は同じチャットの SWP-01〜03 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1009-swp-race origin/cloudflare`。

### 手順1 確かめる

- 読んだもの: CHAT-1006-SWP-01 のログの手順2（共通部・G1〜G5 の個別部）・手順3、`docs/decisions/houou.md`、`docs/notes/houou-race.md`、`houou_race.html`・`houou_race.js`・`style.css`「鳳凰戦 順位変動」の節。
- **公開の確認（止まる条件に当たらない）**: `houou_race.html` に `noindex` は無い。`navbar.js:43`（鳳凰戦の「順位変動」）・`llms.txt:25`・`sitemap-pages.xml:60` に載っている。`origin/cloudflare` は着手時 `a6988a56`。
- **未マージの work/1008-hou（#518）での扱い**: `houou/race/index.html` と `houou/race/<期>-<前後>.json` を作り、`generate_houou_race.py` の `render_page()` と `houou_race.js` を `houou/race/` が呼び出す形で共用する（`houou.js` が `data-default` を差し替え、`?term=43-1&league=B1` で期・リーグを指定）。`docs/decisions/houou.md`（CHAT-1008-HOU-01、grill Q7・Q11）に「旧 URL は公開の段で 301」とあり、ブランチの `_redirects` に `houou_race.html` の 301 はまだ無い（公開の段で足す）。**`houou_race.js` と `style.css` の順位変動の節は `houou/race/` と共通のため、ここで見つかった JS・CSS の指摘の直し先は共通。** 各指摘の行き先は「houou/ の移設で扱う（#518）」を主にした。
- 「挙げなくてよいこと」: SWP-01 の一覧に加え、`docs/decisions/houou.md`・`docs/notes/houou-race.md` の決定（帯のすべりこみ・数え始めの待ち・同じ速さのカウントアップ・「▲」・降級の枠・見出しのカード・幅 400px・共有ボタンなし・OGP は共通画像・説明文・既定の表示・告知動画）は指摘にしない。h1 が画面に見えること（`houou.md` LGR-05）、canonical が無いこと、ページ名が「順位変動」であることも同じ。

### 手順2 点検の手段と範囲

- 表示: `python3 -m http.server`（作業ツリー、127.0.0.1:8801）と Playwright 1.56.1（Node、`viewport` のみ。`mobile: true` は使わない）。外部ドメイン（`pbs.twimg.com` など）は `route` で遮断し、名前チップは頭文字の表示になる。本番は巡回していない。
- 幅 1280・390・360px。360px では **全445表（23期〜43期・前後・リーグ・組）を1つずつ開き**、横スクロール（`scrollWidth`>360）と選手名の切れ（`.mj-race-name` の `scrollWidth`>`clientWidth`）を数えた: どちらも 0 件。
- 文字 200%: `html { font-size: 200% }` を `addStyleTag` で足した近似（実機の「文字サイズ」とは違う）。
- 状態: `page.route` で `houou_race/*.json` を abort／404／不正 JSON／`[]` に差し替え、2.5秒遅延、`javaScriptEnabled: false`、`reducedMotion: 'reduce'`。選ぶ部品の hover は `hover()`、フォーカスは `focus()`・`keyboard.press`、押下は CSS から。
- 再生・停止: 再生ボタン→表を押す→節ラベル（最後の節）→「もう一度見る」を 390×800 と 360×640 で確認。
- コントラスト: WCAG 2.x の相対輝度。線形化 `c≤0.03928 ? c/12.92 : ((c+0.055)/1.055)^2.4`、`L=0.2126R+0.7152G+0.0722B`、比 `(L1+0.05)/(L2+0.05)`。色は `getComputedStyle`（半透明・`color-mix` は重ねた結果。昇級の背景 15%＝(252,230,219)、降級 15%＝(221,233,246)、節ラベルの下地 10%＝(231,231,232)、段の下地 6%＝(241,241,241)）。
- サイト共通の指摘（SWP-01 の G1-01・G1-02・G2-02・G2-03・G2-04・G4-01・G4-02）がこのページでも出るかは、下の「共通の指摘」の表。

### 点検の結果

見られなかったこと・制約は表の後。通し番号は R-01 の形（指摘 R-01〜R-13、13件）。**実機確認は R-12 だけ**（文字サイズ）。

#### 指摘の一覧（13件）

| ID | ページ | 事象 | 根拠 | 直す先 | 分類 | 大きさ | 行き先 | 実機 | 検証 |
|---|---|---|---|---|---|---|---|---|---|
| R-01 | `houou_race`（G1-4 コントラスト） | 昇級の帯の文字（白、17px・800）が橙の地 `#e8590c` の上で読みにくい。降級の帯（白 on `#1c6fc4`）は 5.10:1 で基準内 | 白 on `#e8590c`: L(白)=1.0、L(#e8590c)=0.2214、(1.05)/(0.2714)= **3.58:1**。17px・800 は WCAG の「大きい文字」（18.66px 以上の太字か 24px 以上）に当たらず、基準は 4.5:1。例: `#c2410c` なら 5.18:1、`#b84a08` なら 5.22:1。計算値 `rgb(255,255,255)` on `rgb(232,89,12)`（17px・800） | `style.css`「鳳凰戦 順位変動」の `--mj-race-warm`・`.mj-race-band.up`。見た目（橙の色）を決める必要がある | 新規 | 中 | houou/ の移設で扱う（#518）。色は共通の CSS のため `houou/race/` に同時に効く | 不要 | 再現（computed の色から計算） |
| R-02 | `houou_race`（G1-4） | 小さい字（13px）の灰色 `#6a7180` が、色のついた地や薄い地の上で 4.5:1 に届かない | 順位の数字（13px）on 昇級の地 (252,230,219): **4.08:1**、on 降級の地 (221,233,246): **3.98:1**。節ラベル（13px、再生前）on `rgba(14,17,22,.1)`＝(231,231,232): **3.96:1**。期の「第」「期」（13px）on (241,241,241): **4.34:1**。「第n節まで」（13px）も同じ灰色。白地では 4.90:1 で基準内。灰色を `#5a6170` にすると全部 5.0:1 以上（`#5f6675` で 4.66〜5.10:1） | `style.css` の `--mj-race-muted`（`.mj-race-rk`・`.mj-race-nd`・`.mj-race-stepper output`・`.mj-race-name small`） | 新規 | 小 | houou/ の移設で扱う（#518） | 不要 | 再現（computed の色から計算） |
| R-03 | `houou_race`（G4 キーボード） | 選ぶ部品のボタン（前期/後期・A〜E・A1 など・組）を Enter か Space で押すと、フォーカスが `<body>` に戻り、次の Tab が先頭のスキップリンクから始まる。期の送りも、最初の期（第23期）・最後の期で押すとボタンが disabled になりフォーカスが消える | 再現: `.mj-race-seg button[data-league="B2"]` にフォーカスして Enter → `document.activeElement` は `BODY`（`本文へスキップ`）。Space（C）も同じ。期の送りを 第23期 まで押して `document.activeElement` が `BODY`。`houou_race.js` の `renderSelectors()` が `lgBox.replaceChildren(...rows)` で全ボタンを作り直す（`houou_race.js:119-143` 付近）。期の送りは `kiPrev.disabled`（#184 の「次へ」と同じ型） | `houou_race.js`（`renderSelectors()` で、押されたボタンに相当するボタンへフォーカスを戻す） | 新規（#184 に近い） | 小 | houou/ の移設で扱う（#518。`houou_race.js` を共用するため `houou/race/` でも出る） | 不要（外付けキーボード／VoiceOver で確かめるなら要） | 再現 |
| R-04 | `houou_race`（G4 キーボード・G3 状態） | 再生ボタンを Enter／Space で押すと、再生中は押したボタンが `opacity: 0`（`pointer-events: none`）になるが、フォーカスは残ったままで、フォーカスの輪も見えない。再生を止める操作は「表を押す」（マウス・タッチ）だけで、キーボードでは節ラベルのボタン（押すとその節の終わりで止まる）が代わりになるが、そうとは分からない。1つの表の再生は最長で13節×約3.7秒＝約48秒 | 再現: `.mj-race-play` にフォーカスして Enter → `document.activeElement` は `mj-race-play hide`、`opacity: 0`、`pointer-events: none`、`outline-style: solid`（見えない）。節ラベル（`.mj-race-nd`）の Enter で `phase='paused'`（止まる）。コード: `tableEl.addEventListener('click', …)` が止める唯一の経路（`houou_race.js` の「再生中は表を押すと止まる」） | `houou_race.js`（再生中はフォーカスを節ラベルへ移す、または一時停止の操作を足す）・`style.css`（`.mj-race-play.hide` の `opacity`） | 新規 | 小〜中（止め方の見せ方を決める） | houou/ の移設で扱う（#518） | 不要 | 再現 |
| R-05 | `houou_race`（G4 支援技術） | 順位表は `role="list"` で、直下に `role="listitem"` でない要素が19個ある（順位のマス 16・昇級/降級の帯 2・再生ボタン 1。16人の表の例）。順位の数字が選手の行と切り離されているため、支援技術で順位と選手が結びつかない可能性がある | DOM を集計: `(none):div.mj-race-rk` 16、`(none):div.mj-race-band` 2、`listitem:div.mj-race-row` 16、`(none):button.mj-race-play` 1（`#raceTable` の直下）。読み上げの実際の挙動は未検証 | `houou_race.js`（`build()` で順位を行の中に入れる、または `role` を整理する） | 新規 | 小〜中（読み上げ方の確認が要る） | houou/ の移設で扱う（#518）／#186 の読み上げの確認と併せる | 不要 | 再現（DOM）。読み上げは未検証 |
| R-06 | `houou_race`（G1 余白・幅） | 1280px で、ページ末尾の説明文 `.mj-lead` が画面の左端（x=0、幅 720px）に置かれ、本文の列（`.mj-race`、x=440〜840、幅 400px 中央）とそろっていない。390px では説明文が全幅（左の余白 4px）で、本文の左端（8px）と 4px ずれる | 主セッション計測（1280px）: `.mj-race` left=440・width=400、`.mj-lead` left=0・width=720・font 13px。`style.css` に `body:has(.mj-race) .mj-lead` の規則が無い（#312 の「表・グラフのページでは本文の幅にそろえる」の対象に入っていない） | `style.css`（`.mj-lead` を `.mj-race` の幅にそろえる1規則） | 新規（#312 の延長） | 小 | houou/ の移設で扱う（#518。`houou/race/` の枠が同じ作りか確認が要る） | 不要 | 再現 |
| R-07 | `houou_race`（G2 タップ領域） | 主な操作の大きさ（390px・360px で同じ）: 期の送り 42×42px、節ラベル 67〜75px×28px、名前チップ（X へのリンク）28×28px。選ぶ部品の段（46px）と再生ボタン（92px）は 44px 以上。いずれも 24×24px 以上だが 44px 未満。ナビのブランド 14×40px・ハンバーガー 56×40px は SWP-01 の G2-04 と同じ | `getBoundingClientRect`（計測値）。`style.css`: `.mj-race-round { width: 42px; height: 42px }`、`.mj-race .mj-race-nd { height: 28px }`、`.mj-race-chip { width: 28px; height: 28px }` | `style.css`（`.mj-race-round`・`.mj-race-nd`・`.mj-race-chip`） | 新規 | 小 | houou/ の移設で扱う（#518）。節ラベルの高さは見た目の決定が要る | 不要 | 再現（計測） |
| R-08 | `houou_race`（G3 取得失敗） | 期ごとの JSON が取れない（abort・404・不正 JSON）と、表の場所に「データを読み込めませんでした」と1行だけ出る（高さ 26px、スタイルなし、やり直しの案内なし）。**表が1度出た後に取得が失敗すると**、前の表の節ラベル（5個）と表の高さ（544px）が残ったまま、その上に同じ文が出て、選ぶ部品は新しい期になる。次の選択で JSON が取れれば復旧する | 再現: 初回（abort／404／不正 JSON の3通り）で `#raceTable` の文字は「データを読み込めませんでした」・高さ 26px・行 0・節ラベル 0。第43期を表示した後で第42期を abort にすると、表の高さ 544px・節ラベル 5・文は「データを読み込めませんでした」・`#raceKi` は「第42期」。`houou_race.js:171-186` の `catch` | `houou_race.js`（失敗時に節ラベルを消す、文のスタイル、再試行）・`style.css` | 新規 | 小 | houou/ の移設で扱う（#518） | 不要 | 再現 |
| R-09 | `houou_race`（G3 データの無い組み合わせ） | JSON に選んだ表が無い（応答が `[]` など）と、`TypeError: Cannot read properties of undefined (reading 'rounds')` が出て、表の場所が空のままでメッセージも出ない。選べる組み合わせは `data-index` から作るため、通常は起きない（445表すべてを開いて、選択の食い違いは 0 件） | 再現: `houou_race/*.json` を `[]` に差し替えて初期表示 → `pageerror`、`#raceTable` の文字は空、高さ 2px。`setup(cache[key].find(...))` が未定義を受け取る（`houou_race.js` の `changed()`） | `houou_race.js`（`find` の結果が無いときは失敗の表示にする） | 新規 | 小 | 見送り（通常は起きない。R-08 の直しの中で扱ってよい） | 不要 | 再現（応答の差し替え） |
| R-10 | `houou_race`（G3 読み込み中・G5 冒頭） | 読み込み中は表の場所が空（高さ 2px）で、読み込み中の表示が無い。JS が無効だと「鳳凰戦 順位変動」の見出しと「第 期」だけで、表も案内も無い（`<noscript>` が無く、表は HTML に焼き込まれていない）。ページの内容は meta description と末尾の説明文だけ | 再現: 応答を 2.5秒遅らせた状態の `#raceTable`（文字空・高さ 2px）。`javaScriptEnabled: false` で `main` の文字は「鳳凰戦 順位変動／第期／（説明文）」 | `houou_race.js`・`scripts/generate_houou_race.py`（表を HTML に焼くか `<noscript>`） | 新規 | 中 | 新サイトの要件（#296 の方針。静的に焼く）。現行では見送り | 不要 | 再現 |
| R-11 | `houou_race`（G3 hover・押下） | 期の送り・前期/後期・リーグのボタン・節ラベルに、hover の見た目の変化が無い（PC の話）。押下（`:active` で縮む）とフォーカス（2px の黒い輪）はある。無効（`opacity` 0.3〜0.35）は読み取れる | 再現: 選ぶ部品の未選択ボタンの `background-color`・`color` が通常時と hover 時で同じ（`rgba(0,0,0,0)`・`rgb(106,113,128)`）。フォーカス: `outline: 2px solid rgb(14,17,22)` | `style.css`（`.mj-race-seg button:hover` など） | 新規（SWP-01 の G3-11 と同型） | 小 | 見送り（見た目の新しい決定が要る。#528 の部品の行に含める） | 不要 | 再現 |
| R-12 | `houou_race`（G2 文字の拡大） | 本文の字がすべて px 指定（26・17・13・11px）で、ルートの文字サイズを 200% にしても変わらない（変わるのはナビだけ）。名前チップの頭文字は 11px | 近似: `html{font-size:200%}` で h1 25.74px・選ぶ部品 26px・選手名 17px・説明文 13px のまま（100% と同じ）、`scrollWidth`=390。`style.css` の `--mj-race-l/m/s` が px | `style.css`（`--mj-race-*` の単位） | 新規（SWP-01 の G2-11 と同型） | 中 | 実機で確かめてから決める | **要**: https://ryoei.pro/houou_race.html を iPhone の Safari で開き、「文字サイズ」を最大にして、選手名・ポイント・節ラベルが拡大されるか、はみ出さないか | 近似のみ（未検証） |
| R-13 | `houou_race`（G1 値の食い違い。#528 の材料） | このページだけの独自の値の系統: 文字色 `#0e1116`（サイトの本文は `#212529`）、灰色 `#6a7180`、橙 `#e8590c`・青 `#1c6fc4`（昇級・降級）、角丸 8px／6px／3px／50%、見出しは 26px・800 のカード（他のページの見える h1 は `resource_efficiency` の 16px など）、行の高さ 34px・選ぶ部品の高さ 46px | `style.css`「鳳凰戦 順位変動」の節の `.mj-race { --mj-race-ink … --mj-race-row … }` と各規則。SWP-01 の G1-09（トークンの複製）・「ほぼ黒の文字色」の行と同根 | `style.css`（トークンを `docs/notes/design.md` の値に寄せるか、独自を例外として決める） | 重複: #270・#528 | 中 | 新サイトの要件（#528 に値を足す。houou/ の移設で `houou/` の部品として整理する可能性がある） | 不要 | 再現（CSS の値） |

#### 共通の指摘（SWP-01 の G1-01・G1-02・G2-02・G2-03・G2-04・G4-01・G4-02）がこのページで出るか

| SWP-01 の ID | このページ | 確認 |
|---|---|---|
| G1-01（濃色ページのスキップリンク 2.10:1） | **出ない**。ページは明色（背景白）で、スキップリンクは紺 on 白 | 計算の対象外 |
| G1-02（ナビのフォーカスが 1.29:1、虫眼鏡は表示なし） | **出る**（ナビ項目）。`.nav-link` は `outline: none`・`box-shadow: rgba(13,110,253,.25) 0 0 0 4px`。虫眼鏡はこのページに無い（`data-search="off"`） | 再現 |
| G2-02（固定ナビで開いたメニューが画面を超える） | **出ない**。ナビは `position: relative`（固定なし） | 計測 |
| G2-03（検索アイコンが2行目に落ち、ナビ 95px） | **出ない**。検索アイコンが無く、ナビは 390・360px で 56px | 計測 |
| G2-04（ブランド 14×40px・ハンバーガー 56×40px） | **出る**（390px で 14×40・56×40） | 再現 |
| G4-01（`role=button` の `<a>` が Space で動かない） | **出る**。ドロップダウンのトグルで Space → `aria-expanded="false"`・`scrollY` 215 | 再現 |
| G4-02（ナビに現在地の表示が無い） | **出る**。ナビの `.active`・`aria-current` が 0 件 | 再現 |

#### 問題が見つからなかったこと

- 横スクロールとはみ出し: 390・360px で全445表とも無し。選手名の切れも無し（「第n節まで」の表記を含む）。
- 動き: `prefers-reduced-motion: reduce` で ▶ を押すと帯と色が出た最終の状態になり（`もう一度見る` に変わる）、行の `transition` は 0s。決めたとおりの見せ方（帯のすべりこみ・数え上げ・▲）は指摘にしていない。
- 再生・停止: 再生ボタンの位置は表の見えている部分の中央（360×640 で表が画面の下から始まっていても収まる）。表を押すと止まり、節ラベルを押すとその節の終わりへ、もう一度見るで最初に戻る。
- 画像の取得失敗: 名前チップは頭文字の表示になり、崩れない（#411 の取りこぼしの話は当てはまらない）。
- 公開・メタ: `title`「順位変動 | 鳳凰戦 | ryoei.pro」、description、h1「鳳凰戦 順位変動」（1つ）、`lang`、viewport、favicon、`og:*`、`twitter:card` があり、`noindex` と canonical は無い（決定どおり）。navbar・`llms.txt`・sitemap に載っている。`apple-touch-icon`・`og:locale`・`twitter:image` が無いのは全ページ共通（#202・#267）。仮の文言・`console.log`・TODO は無い。
- フォーカスの輪: 選ぶ部品・節ラベル・チップは 2px の黒（`#0e1116`）で、白地では 18.9:1。

#### 見られなかったこと・制約

- 実機（iPhone の Safari）、実機の文字サイズ設定、VoiceOver・NVDA。文字の拡大はルート 200% の近似。
- 本番は見ていない（手元の配信で `houou_race/*.json` を配信）。外部画像（X のアイコン）は遮断したため、アイコン画像のある表の見た目は未確認。
- R-05 の読み上げの実際の挙動、R-04 の外付けキーボードでの操作感。
- 再生中の画面の途中（追い越しの動き）の見た目は決定どおりとして見ていない。

#### 集計

| 分類 | 件数 | 小 | 小〜中 | 中 | 大 |
|---|---|---|---|---|---|
| 新規 | 12（R-01〜R-12） | 7（R-02・R-03・R-06・R-07・R-08・R-09・R-11） | 2（R-04・R-05） | 3（R-01・R-10・R-12） | 0 |
| 既存 issue と重複 | 1（R-13: #270・#528） | 0 | 0 | 1 | 0 |
| 決定済みで対象外 | 0 | 0 | 0 | 0 | 0 |
| **合計** | **13** | 7 | 2 | 4 | 0 |

行き先: houou/ の移設で扱う（#518）8件（R-01〜R-08）、新サイトの要件 2件（R-10・R-13）、見送り 2件（R-09・R-11）、実機で確かめてから決める 1件（R-12）。現行の `houou_race.html` で直す案としても、`houou_race.js`・`style.css` の順位変動の節の直しは `houou/race/` に同時に効く（共用）。
主セッションが実物で再現したもの: R-01〜R-11・R-13（R-12 は近似のみで「未検証」。R-05 は DOM のみ再現し読み上げは未検証）。サブエージェントは使っていない。

## 報告

- 状態: 完了
- ブランチ: work/1009-swp-race
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1009-SWP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-race
- 確認用URL: なし（docs のみ）
- マージ: 済（docs/logs/・docs/decisions/ のみの変更を cloudflare へ push。マージ後の SHA はターミナルの最終報告）
- issue: なし（起票・コメントはしていない。この指示は読むだけ）
- 判断が必要なこと:
  - **どれを現行で直すか**: 指摘は13件（新規 12・重複 1）。現行の `houou_race.html` を直す場合も、直す場所（`houou_race.js`・`style.css` の順位変動の節）は未マージの `houou/race/`（#518）と共通で、どちらかに入れれば両方に効く。#518 の公開の前に直すか、`houou/` の移設で一緒に扱うかを決めてほしい。直す場合の優先は、R-03（選ぶ部品を押すとフォーカスが `<body>` に戻る）・R-04（再生中は再生ボタンのフォーカスが見えず、止める操作がキーボードで分かりにくい）・R-01（昇級の帯の文字 3.58:1）・R-02（13px の灰色 3.96〜4.34:1）。R-01・R-02 は色の決定（`#c2410c` と `#5a6170` の例を書いた）が要る
  - **houou/ の移設で扱うもの**: R-01〜R-08（行き先欄のとおり）。`houou/race/` の枠が `houou_race.html` と同じ作りなら同時に直る。#518 の担当（work/1008-hou を作業中のチャット）へ、この一覧を渡すか、#518 にコメントするか決めてほしい（この指示では issue を触っていない）
  - **実機で見てほしいページ（1件）**: https://ryoei.pro/houou_race.html を iPhone の Safari で開き、「文字サイズ」を最大にして、選手名・ポイント・節ラベルが拡大されるか、はみ出さないか（R-12。本文の字がすべて px 指定で、200% の近似では変わらなかった）
  - R-13（このページ独自の色・角丸・見出しの値）は、SWP-01 の「デザインの不足・不整合」（#528）に材料として足すか、`houou/` の部品として整理するかを決めてほしい
- 未確認の項目:
  - 実機（iPhone の Safari）、実機の文字サイズ、VoiceOver・NVDA（R-05 の読み上げの実際の挙動を含む）。文字の拡大はルート 200% の近似
  - 本番は見ていない。X のアイコン画像は遮断したため、画像のある表の見た目は未確認
  - 再生中の途中の見た目（追い越しの動き）は決定どおりとして見ていない
  - `houou/race/`（未マージの work/1008-hou）でこれらの指摘が実際に出るかは、`houou_race.js` と `style.css` の順位変動の節を共用しているという読みで、動かして確かめていない
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d5ad06b4）: https://github.com/retroeater/mj-logs/tree/main/guide/d5ad06b4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5ad06b4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5ad06b4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5ad06b4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5ad06b4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5ad06b4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5ad06b4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
