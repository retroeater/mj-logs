# CHAT-1116-LVP-27

- 着手日時: 2026-11-16
- 対象issue: #400・#490・#398・#399
- ブランチ: work/1116-lvp-rule
- 着手時HEAD: 55b6bf637

## 指示

【Claude作成】Claude Code 向け指示：#400 第4回の判別と「プレイヤー解説」の規則を【2】に入れ、#398・#399・#400 第3回で平野さんが【3】に入れる値の表を作る Chat-Ref: CHAT-1116-LVP-27 マージ: 承認済み（チャットで）。ページの増減が下の「決定」の範囲を超えたら、マージせずに止まる 貼る時機: CHAT-1116-LVP-26 の完了の後（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-lvp-rule の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-lvp-rule を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-lvp-rule origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1116-LVP-26 が完了していなければ止まる。

目的
CHAT-1116-LVP-26 で記録した決定のうち、コードで入れるもの（#400 第4回の判別・#490 U4）を入れ、平野さんが【3】に手で入れるもの（#398・#399・#400 第3回）の値の表を作る。
決定（2026-11-16、平野さん。番号は CHAT-1116-LVP-25 の報告の番号）

*

1. #399: メンバー限定版（行3439）のまとめ単位を6単位にして Y、公開版は N（ページは増えない）

*

2. #398: 紅龍戦の予選の個別ページを作る（約18ページ増）。紅龍戦の「自動掲載」は、予選の値を【3】に入れた後に平野さんが Y にする

*

3. #400 第3回: 回戦の18本を Y、日単位の生放送4本は N（約6ページ増）

*

4. #400 第4回: 【2】の規則で第4回と判定し、毎日の追記に乗せる（個人戦19本、約4〜5ページ増。チーム戦は入れない）

*

10. #490 U4: 概要欄の「プレイヤー解説：」の名前を解説に入れる規則を足す。実装の前に件数を数える

* ページが増えるのは、上の 2〜4 の大会（紅龍戦・リーチ麻雀世界選手権）だけを受け入れる

前提（チャット側。平野さんの決定ではない）

* 件数・ページの見込みは LVP-25 の `## 経過` のもの（要確認）
* #398・#399・#400 第3回の値は平野さんが【3】に入れる。入れ方は、前回までと同じ「入力用」シートで動画ID から引き当てて「掲載」列などに値だけ貼る方法（列が複数なら列ごとの TSV）。【3】の末尾に新しい行として足さない（CHAT-1105-LVP-18 で同じ動画ID の行が重なり、生成が止まりかけた）
* 「プレイヤー解説」は、対局者が解説を兼ねる形。数えた結果、対局者と解説が同じ動画で重なる行が多い・解説の欄の意味が変わると見えるときは、実装せず件数と例を書いて止まる
* 告知動画の制作はまだ始めていない

手順

1. #400・#490 に着手中のコメントを書く。「プレイヤー解説：」が概要欄にある動画の件数と、その名前が対局者と重なる件数を数える。#400 第4回の判別の規則を【2】（`scripts/lib/live_extract.py` など）に入れ、テストを足す。U4 は件数を見て、止まる条件に当たらなければ規則を入れる
2. #398（第4期の4行のステージ「予選」と約46セルの掲載など）・#399（行3439 のまとめ単位・掲載、公開版の掲載）・#400 第3回（22行の掲載）で、平野さんが【3】に入れる値の表を、動画ID・列・値の形で `## 経過` に書き、列ごとの TSV の中身も書く。試しに、その値を入れたと仮定して生成し、増える・減るページの URL を全件書く。今の【2】【3】で全ページを再生成し、テストを回す
3. 止まる条件に当たらなければ、コードの変更（#400 第4回の判別と U4）を CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、Workers Builds の check-run の結果を確かめる。マージの後の最初の毎朝の取り込みで、第4回の行が【3】に追記される見込みを書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* U4 の件数で、解説の欄の意味が変わると見える（件数と例を書いて、U4 は実装せずに残りを進める）
* 今の【2】【3】での再生成で、紅龍戦・リーチ麻雀世界選手権のほかにページが増える・減る（マージせずに止まる）
* 第4回の判別で、ほかの大会の行の【2】の値が変わる（全件を書いて、マージせずに止まる）
* 【2】【3】の読み込みが失敗する
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/lib/live_extract.py`・`scripts/lib/live_layer3.py`・`scripts/generate_live_pages.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #400・#490 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが【3】に入れる値の表（#398・#399・#400 第3回、列ごと）と、入れた後に増えるページの一覧、紅龍戦の「自動掲載」を Y にする時機を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-LVP-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-LVP-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1116-LVP-27` のコミットは0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-16 14:21 JST。CHAT-1116-LVP-26 は完了（cloudflare 55b6bf637）
- 0. 「指示」欄の末尾は指示文の最後の行と一致。work/1116-lvp-rule はリモート・ローカルとも無いので origin/cloudflare から作った
- #400・#490 に他セッションの着手中は無い（#490 の 2026-10-17 の着手中は CHAT-1017-LVP-07 で終わっている）。両方に着手中のコメントを書いた
- 未マージの work/ ブランチ: `work/1101-prp-pub` が `scripts/generate_live_pages.py` を変えているが、今回の成果物は `live_extract.py` だけ（generate_live_pages.py は一時の試しで触って戻した）。`live_extract.py`・`live_layer3.py` を変えているブランチは無い

### U4「プレイヤー解説：」（実装しない。止まる条件に当たった）

- 【1】（`data/live_channel_raw.jsonl`）で概要欄に「プレイヤー解説」がある動画は **169本**（すべて「プレイヤー解説：」の見出しの行、615行）。題名は麻雀日本シリーズ 168本（【メンバー限定】89・公開 79）と切り抜き1本。2022〜2026年
- **今の規則で、名前はすでに解説に入っている**: `CAST_RE`（`(実況|牌譜解説|解説)[:：]`）は行頭を見ないので、「プレイヤー解説：」の中の「解説：」に当たる。名前がある163本のすべてで、プレイヤー解説の名前が【2】の解説に入っている（ほかの6本は名前が無い）
- **その名前が同じ動画の対局者と重なるのは162本**（例: `-J2kKCsWWdI` 麻雀日本シリーズ2022第１節: プレイヤー解説 多井隆晴・佐々木寿人・黒沢咲・前田直哉 → 解説「多井隆晴、佐々木寿人、黒沢咲、前田直哉、齋藤豪」、対局者12名に4名とも含まれる）。解説の欄が「解説者」でなく「その節の対局者の一部」になっており、解説の欄の意味が変わると見える
- 今の掲載: 168本のうち【3】にあるのは168本、掲載 Y は0本（日本シリーズは #688 の範囲）。今ページに出ている影響は無い
- 扱いの案（#688 と合わせて決める）: A 今のまま（プレイヤー解説も解説に入れる）／B 規則を直し「プレイヤー解説：」を解説から外す（`CAST_RE` を行頭か区切りの後に限る）

### 第4回リーチ麻雀世界選手権（世界麻雀 TOKYO2025）の判別

- 実装（e36ec6960・e1e76b50f）: `scripts/lib/live_extract.py`
  - 大会名の判定（`KW`）に「`世界麻雀TOKYO2025` ＋ 個人戦 ＋ 回戦／最終戦」の題名をリーチ麻雀世界選手権として足した（生放送「~個人戦初日~」など・国別対抗チーム戦・表彰式・レセプションは当たらない）
  - 期は「第4回」（`parse_period`。題名に「第N回」が無いため定数 `WRC4_TITLE` で読む）。題名から大会名を外す規則（`WAR_STRIP`）に「世界麻雀TOKYO2025 個人戦」を足した
  - 概要欄の「A VS. B」の「.」を対局者の区切りに含めた（`SPLIT_VS`。直す前は「. Gemma Sakamoto」のように名前の先頭に「.」が残った）
  - テスト `WorldMahjong2025Test`（個人戦の回戦7通り〈最終戦・空白なしを含む〉、生放送・チーム戦・表彰式・レセプションを拾わない、第3回が変わらない、「VS.」の区切り）。直す前のコードで failures=6
- 【2】の値の比べ（【1】の全14,350本で、直す前後の `live_candidate.build_rows()` を比べた）: **変わるのは第4回の個人戦18本だけ**（候補になり、期・ステージ・回戦・まとめ単位・対局者などが入る）。ほかの大会の行の値は変わらない
  - 自己レビューの補足: `SPLIT_VS` はカレンダー（`lib/live_calendar.py`）と未使用の名前の検査（`check_unused_names.py`）でも使う。全動画の `extract_players_and_staff()` を比べると、差は38本で、すべて名前の先頭の「.」が消えるだけ（第4回の個人戦18・国別対抗チーム戦6・「小島VS．灘」12・役満の切り抜き3）。【2】の値は上の18本のほか変わらない
- 本数の訂正: LVP-25・LVP-26 で「個人戦19本」と書いたが、個人戦の回戦の動画は **18本**（予選10・ベスト32 2・ベスト16 2・ベスト8 2・決勝2）。ほかの「個人戦」の題名はレセプション・表彰式・生放送
- 毎朝の取り込みの書かない道筋（update-live-channel、apply なし、run 43809987575）: 【2】 14,350行・候補 4,370 → 14,351行・候補 4,388（+18）、候補でなくなる動画 0本、【3】に追記する行 18行、保留 0行。**未登録の名前が 0名 → 21名**（すべて第4回の対局者: Yunfei Gao〈2行〉・Aaron Chee・Abril Rodino・Anton Lu・Dicky Suharli・Gemma Sakamoto・Henri Devillez・Junjie You・Lukasz Grzybowski・Mateusz Wozniak・Michael Hernandez・Murilo Bordingnon・Natalya Maximova・Nicolas Ishihara・Pei-jyun Shih・Po-Ping Chang・Steven Hue・Vitalii Balaniuk・Wenlong Li・伊藤正樹・保宏明）
- **マージ後の最初の毎朝の取り込み（2026-11-17 04:00 JST）の見込み**: 【2】に18行が候補として入り、【3】の末尾に18行が追記される。掲載は空欄のまま【2】の自動（リーチ麻雀世界選手権は「自動掲載」Y、18本とも限定 Y の動画なので Y）が当たる。#475 に未登録の名前21名の知らせが出る。そのうえで生成は、未登録の名前が無い6本（ベスト32 2・ベスト8 2・決勝2）だけを載せ、ほかの12本（予選10・ベスト16 2）は未登録の名前の警告を出して載せない（止まらない）。平野さんが21名を「連盟プロ以外」（か「別名」）に足した次の再生成で、残りの12本と `live/wrc/4/q.html`・`b16.html` が出る

### #398・#399・#400 第3回で平野さんが【3】に入れる値

- 方針: 公開の動画・メンバー限定の生放送は Y。同じ卓・日にメンバー限定版がある公開の生放送（冒頭のみ無料）は N のまま（自動の規則と同じ考え方）。1本で予選と決勝を通す配信・ブロックを通す配信は、まとめ単位を「、」で並べる（帝王戦・#399 と同じ形）
- #399: 行3439 のまとめ単位を6単位にして Y。公開版の行1156 は今 N なので入れる値は無い
- #400 第3回: 行3095〜3112 のうち17本を Y。**行3103 `F4P_rk_yy2I`（初日Hanchan1）は概要欄に対局者が無く（【3】は森山茂和の1名だけ）、4人を読めないので N のまま**にした（4人が分かれば Y と対局者を入れる）。日単位の生放送4本（行3113〜3116）は今 N なので入れる値は無い
- #398: 予選の行を Y（第1期 10・第2期 13・第3期 11・第4期 4）。公開の生放送（行3039・3044・3050〜3052・3059・3066〜3068・3080）と決勝の公開の生放送（3049・3065）は N のまま。第2期の行3315（メンバー限定「予選Aブロック」）はまとめ単位を3単位に、第3期の行3230（メンバー限定「Ａ卓」。予選と決勝を通す）はステージ「予選」・まとめ単位「予選A、決勝A」に、第2期の行3312〜3314 と第4期の4行はステージ「予選」に、第4期の4行はまとめ単位「予選A〜D」・ステージの並び「1」にした
- **入れ方の注意**: `b3LYGlvy9hw` は【3】に2行ある（行3226 決勝・Y、行3227 予選D・N）。動画ID で引き当てると行3226 に当たるので、**行3227 の掲載 Y は行番号で入れる**。ほかの動画ID は【3】に1行ずつ

全75セル（掲載 56・ステージ 8・まとめ単位 7・ステージの並び 4）:

| issue | 【3】の行 | 動画ID | 列 | 入れる値 | 今の値 |
|---|---|---|---|---|---|
| #399 | 3439 | `GYEJoRc9-rM` | まとめ単位 | 予備予選Aブロック1、予備予選Aブロック2、予備予選Aブロック決勝戦、予備予選Bブロック1、予備予選Bブロック2、予備予選Bブロック決勝戦 | 予備予選 |
| #399 | 3439 | `GYEJoRc9-rM` | 掲載 | Y | N |
| #400 | 3095 | `oYukH-9qk-E` | 掲載 | Y | N |
| #400 | 3096 | `Fr1S6EaeLaY` | 掲載 | Y | N |
| #400 | 3097 | `gMw44rTweoY` | 掲載 | Y | N |
| #400 | 3098 | `_UzK1_XHu1c` | 掲載 | Y | N |
| #400 | 3099 | `xl1u14PfIOk` | 掲載 | Y | N |
| #400 | 3100 | `CV6al6QzX6c` | 掲載 | Y | N |
| #400 | 3101 | `KESz35efAFg` | 掲載 | Y | N |
| #400 | 3102 | `-258_6-EHN0` | 掲載 | Y | N |
| #400 | 3104 | `Dz68KESK7JA` | 掲載 | Y | N |
| #400 | 3105 | `8M1DUPxqxuw` | 掲載 | Y | N |
| #400 | 3106 | `KGgIV8QQLJI` | 掲載 | Y | N |
| #400 | 3107 | `5dthT1ZoDo0` | 掲載 | Y | N |
| #400 | 3108 | `R9M4E5YL2Y8` | 掲載 | Y | N |
| #400 | 3109 | `vO_uiZvfHnw` | 掲載 | Y | N |
| #400 | 3110 | `UPgzCkdG-3w` | 掲載 | Y | N |
| #400 | 3111 | `zAqF3lO2zSM` | 掲載 | Y | N |
| #400 | 3112 | `6AEa5F2FaVw` | 掲載 | Y | N |
| #398 | 3037 | `SdkOIDsqDLE` | 掲載 | Y | N |
| #398 | 3038 | `QlNafyMG_Co` | 掲載 | Y | N |
| #398 | 3040 | `wC5sJNdQWHc` | 掲載 | Y | N |
| #398 | 3041 | `Hqm5_-aCUx4` | 掲載 | Y | N |
| #398 | 3042 | `OttS5J7A2zE` | 掲載 | Y | N |
| #398 | 3043 | `xo7ZV9XhLJw` | 掲載 | Y | N |
| #398 | 3045 | `p1CIWNdVCyg` | 掲載 | Y | N |
| #398 | 3046 | `ZTWVbZv6nHI` | 掲載 | Y | N |
| #398 | 3053 | `JcY87A0IbIE` | 掲載 | Y | N |
| #398 | 3054 | `OZBCdsPTJS0` | 掲載 | Y | N |
| #398 | 3055 | `tcIJG8puNcM` | 掲載 | Y | N |
| #398 | 3056 | `JalFg_YW0x4` | 掲載 | Y | N |
| #398 | 3057 | `UDnNiknL0So` | 掲載 | Y | N |
| #398 | 3058 | `kmaiG6gQKPA` | 掲載 | Y | N |
| #398 | 3060 | `-IC_T2Qkal4` | 掲載 | Y | N |
| #398 | 3061 | `rDoTevaaukM` | 掲載 | Y | N |
| #398 | 3062 | `a-_3BPgVmJg` | 掲載 | Y | N |
| #398 | 3069 | `rnMLFQIP8hA` | 掲載 | Y | N |
| #398 | 3070 | `h4sElg-aDOM` | 掲載 | Y | N |
| #398 | 3071 | `2CgkDl0D-Z8` | 掲載 | Y | N |
| #398 | 3072 | `J3VQti1XUm4` | 掲載 | Y | N |
| #398 | 3073 | `cBYGHEBOYtw` | 掲載 | Y | N |
| #398 | 3074 | `RilYY5Z3hWE` | 掲載 | Y | N |
| #398 | 3075 | `uPZw_BpBDZM` | 掲載 | Y | N |
| #398 | 3227 | `b3LYGlvy9hw` | 掲載 | Y | N |
| #398 | 3228 | `SZgewZ5mNRg` | 掲載 | Y | N |
| #398 | 3229 | `WV4z0ebRNiY` | 掲載 | Y | N |
| #398 | 3230 | `TRkQYKRqSMk` | まとめ単位 | 予選A、決勝A | 予選Aブロック |
| #398 | 3230 | `TRkQYKRqSMk` | ステージ | 予選 | 予選Aブロック |
| #398 | 3230 | `TRkQYKRqSMk` | 掲載 | Y | N |
| #398 | 3312 | `Z3uiwxXurYE` | ステージ | 予選 | （空） |
| #398 | 3312 | `Z3uiwxXurYE` | 掲載 | Y | N |
| #398 | 3313 | `U0yKrT2voYA` | ステージ | 予選 | （空） |
| #398 | 3313 | `U0yKrT2voYA` | 掲載 | Y | N |
| #398 | 3314 | `1WWMxyZ8YcI` | ステージ | 予選 | （空） |
| #398 | 3314 | `1WWMxyZ8YcI` | 掲載 | Y | N |
| #398 | 3315 | `h6cs7x9VUbk` | まとめ単位 | 予選Aブロック1、予選Aブロック2、予選Aブロック決勝戦 | 予選Aブロック |
| #398 | 3315 | `h6cs7x9VUbk` | 掲載 | Y | N |
| #398 | 3417 | `EgfVDNLQLCU` | 掲載 | Y | N |
| #398 | 3420 | `9bg6b7PImBc` | 掲載 | Y | N |
| #398 | 3619 | `9poqArCQUgw` | まとめ単位 | 予選D | D |
| #398 | 3619 | `9poqArCQUgw` | ステージ | 予選 | （空） |
| #398 | 3619 | `9poqArCQUgw` | ステージの並び | 1 | （空） |
| #398 | 3619 | `9poqArCQUgw` | 掲載 | Y | （空） |
| #398 | 3637 | `B4hv2bzLAiU` | まとめ単位 | 予選C | C |
| #398 | 3637 | `B4hv2bzLAiU` | ステージ | 予選 | （空） |
| #398 | 3637 | `B4hv2bzLAiU` | ステージの並び | 1 | （空） |
| #398 | 3637 | `B4hv2bzLAiU` | 掲載 | Y | （空） |
| #398 | 4068 | `nbEV_NP6omQ` | まとめ単位 | 予選A | A |
| #398 | 4068 | `nbEV_NP6omQ` | ステージ | 予選 | （空） |
| #398 | 4068 | `nbEV_NP6omQ` | ステージの並び | 1 | （空） |
| #398 | 4068 | `nbEV_NP6omQ` | 掲載 | Y | （空） |
| #398 | 4092 | `ppXo6o8uX1o` | まとめ単位 | 予選B | B |
| #398 | 4092 | `ppXo6o8uX1o` | ステージ | 予選 | （空） |
| #398 | 4092 | `ppXo6o8uX1o` | ステージの並び | 1 | （空） |
| #398 | 4092 | `ppXo6o8uX1o` | 掲載 | Y | （空） |

列ごとの TSV（「入力用」シートで動画ID から引き当てて値だけ貼る）:

「掲載」の列（56行。動画ID と値のタブ区切り）:

```
GYEJoRc9-rM	Y
oYukH-9qk-E	Y
Fr1S6EaeLaY	Y
gMw44rTweoY	Y
_UzK1_XHu1c	Y
xl1u14PfIOk	Y
CV6al6QzX6c	Y
KESz35efAFg	Y
-258_6-EHN0	Y
Dz68KESK7JA	Y
8M1DUPxqxuw	Y
KGgIV8QQLJI	Y
5dthT1ZoDo0	Y
R9M4E5YL2Y8	Y
vO_uiZvfHnw	Y
UPgzCkdG-3w	Y
zAqF3lO2zSM	Y
6AEa5F2FaVw	Y
SdkOIDsqDLE	Y
QlNafyMG_Co	Y
wC5sJNdQWHc	Y
Hqm5_-aCUx4	Y
OttS5J7A2zE	Y
xo7ZV9XhLJw	Y
p1CIWNdVCyg	Y
ZTWVbZv6nHI	Y
JcY87A0IbIE	Y
OZBCdsPTJS0	Y
tcIJG8puNcM	Y
JalFg_YW0x4	Y
UDnNiknL0So	Y
kmaiG6gQKPA	Y
-IC_T2Qkal4	Y
rDoTevaaukM	Y
a-_3BPgVmJg	Y
rnMLFQIP8hA	Y
h4sElg-aDOM	Y
2CgkDl0D-Z8	Y
J3VQti1XUm4	Y
cBYGHEBOYtw	Y
RilYY5Z3hWE	Y
uPZw_BpBDZM	Y
b3LYGlvy9hw	Y
SZgewZ5mNRg	Y
WV4z0ebRNiY	Y
TRkQYKRqSMk	Y
Z3uiwxXurYE	Y
U0yKrT2voYA	Y
1WWMxyZ8YcI	Y
h6cs7x9VUbk	Y
EgfVDNLQLCU	Y
9bg6b7PImBc	Y
9poqArCQUgw	Y
B4hv2bzLAiU	Y
nbEV_NP6omQ	Y
ppXo6o8uX1o	Y
```

「ステージ」の列（8行。動画ID と値のタブ区切り）:

```
TRkQYKRqSMk	予選
Z3uiwxXurYE	予選
U0yKrT2voYA	予選
1WWMxyZ8YcI	予選
9poqArCQUgw	予選
B4hv2bzLAiU	予選
nbEV_NP6omQ	予選
ppXo6o8uX1o	予選
```

「まとめ単位」の列（7行。動画ID と値のタブ区切り）:

```
GYEJoRc9-rM	予備予選Aブロック1、予備予選Aブロック2、予備予選Aブロック決勝戦、予備予選Bブロック1、予備予選Bブロック2、予備予選Bブロック決勝戦
TRkQYKRqSMk	予選A、決勝A
h6cs7x9VUbk	予選Aブロック1、予選Aブロック2、予選Aブロック決勝戦
9poqArCQUgw	予選D
B4hv2bzLAiU	予選C
nbEV_NP6omQ	予選A
ppXo6o8uX1o	予選B
```

「ステージの並び」の列（4行。動画ID と値のタブ区切り）:

```
9poqArCQUgw	1
B4hv2bzLAiU	1
nbEV_NP6omQ	1
ppXo6o8uX1o	1
```

### 試し（上の値を入れ、第4回の18行を足したと仮定した生成）

- 一時の変更 8a63edbd0（【3】に上の表〈ステージの並びの4セルを除く71セル〉を当て、【1】から第4回の18行を作って掲載 Y で足す）で生成し、bfa1bce68 で戻した（戻した後のコードは e36ec6960 と同じ）。run 43810505001（compare_ref origin/cloudflare）
- **増えるページ 26（すべて紅龍戦・リーチ麻雀世界選手権。減るページは無い）**:
  - 紅龍戦 18: https://ryoei.pro/live/kouryu/1/q-a.html ・`1/q-b.html`・`1/q-c.html`・`1/q-d.html`、`2/q-a-1.html`・`2/q-a-2.html`・`2/q-a-f.html`・`2/q-b.html`・`2/q-c.html`・`2/q-d.html`、`3/q-a.html`・`3/q-b.html`・`3/q-c.html`・`3/q-d.html`、`4/q-a.html`・`4/q-b.html`・`4/q-c.html`・`4/q-d.html`
  - リーチ麻雀世界選手権 第3回 6: https://ryoei.pro/live/wrc/3.html ・`3/q.html`・`3/b32.html`・`3/b16.html`・`3/b8.html`・`3/f.html`
  - リーチ麻雀世界選手権 第4回 2: https://ryoei.pro/live/wrc/4/b32.html ・`4/b8.html`（`4/f.html` に決勝の2本が足される）。未登録の名前の12本が載ると `4/q.html`・`4/b16.html` が増える（計 28）
- 中身が変わる既存のページ: 紅龍戦・世界選手権の大会・期のページ、`live/gpmax/13/` の予備予選の6ページ（#399 の通しの生放送が足される）、`sitemap-live.xml`・`data/live_pages.json`、ほかの大会の個別ページと `pro/` の「他の対局（N件）」の件数・カード（新旧の比べで中身が違う 588件のうち、ページの増減は上の26件だけ）
- 生成の警告: 新しく出るのは、第4回の12行の「未登録の名前」だけ（「未確認」は 657 → 653〈第4期の4行〉、ほかは今と同じ「別名」の3件と ABEMA の1件）
- 第4期の4行の「ステージの並び」（空）は試しの後に表へ足した（`live/kouryu/4.html` の並びだけに効く）
- 今の【2】【3】での全ページの再生成（試しを戻した後、run 43811045664）: **差分なし**。テスト 2,214件 OK
- 自己レビュー（新しい文脈のサブエージェント、1回。対象 e36ec6960、観点: KW の並びと正規表現、WAR_STRIP の影響、SPLIT_VS の「.」、テストが直す前に落ちるか、不要な複雑さ）: 要修正なし。任意4件:

| 指摘 | 扱い |
|---|---|
| 「最終戦」の題名（「個人戦決勝 最終戦」）を拾わない | 直した（e1e76b50f。`(?:回戦|最終戦)`、テストに足した） |
| KW は空白1つを必須、WAR_STRIP は `\s*` で食い違う | 直した（e1e76b50f。KW も `\s*`。生放送は「~」でつながるので当たらない） |
| `WRC_EDITIONS` は1件だけの辞書 | 直した（e1e76b50f。定数 `WRC4_TITLE` 1つにし、3か所で使う） |
| `SPLIT_VS` はカレンダーと未使用の名前の検査でも使う | 範囲を確かめて上に書いた（差は名前の先頭の「.」が消えるだけ） |

  証跡: 直した後も【2】の値の比べは同じ（変わるのは18本だけ）。全18,654行の war_of の比べで差は18本、第1〜3回の解析は変わらない（レビュー）
- マージ: 祖先を確かめて `git push origin HEAD:cloudflare`（55b6bf637..d40075485）。Workers Builds の check-run（d40075485）は mj が success（今の【2】【3】では生成物は変わらないので、本番の HTML は確かめていない）

## 報告

- 状態: 判断待ち
- ブランチ: work/1116-lvp-rule
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1116-LVP-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1116-lvp-rule
- 確認用URL: なし（今の【2】【3】では生成物の変更なし）
- マージ: 済（d40075485）
- issue: #400・#490・#398・#399
- 判断が必要なこと:
  1. 【3】に入れる値（#398・#399・#400 第3回、75セル）: `## 経過`「#398・#399・#400 第3回で平野さんが【3】に入れる値」の表と、列ごとの TSV（掲載 56・ステージ 8・まとめ単位 7・ステージの並び 4）。`b3LYGlvy9hw` は行3227（予選D）の掲載 Y を行番号で入れる
  2. 入れた後に増えるページ（試しで26）: 紅龍戦 18（`live/kouryu/1/q-a〜d.html`・`2/q-a-1.html`・`2/q-a-2.html`・`2/q-a-f.html`・`2/q-b〜d.html`・`3/q-a〜d.html`・`4/q-a〜d.html`）、第3回 6（`live/wrc/3.html`・`3/q.html`・`3/b32.html`・`3/b16.html`・`3/b8.html`・`3/f.html`）、第4回 2（`live/wrc/4/b32.html`・`4/b8.html`。こちらは値を入れなくても次の毎朝の取り込みの後に出る）
  3. 紅龍戦の「自動掲載」を Y にする時機: 上の値を【3】に入れ、その次の再生成でページが出たのを確かめた後（先に Y にすると、第4期の4行〈掲載が空欄〉に自動の値が付き、まとめ単位「A〜D」〈ステージ空〉のまま載る）
  4. 第3回の行3103 `F4P_rk_yy2I`: 概要欄に対局者が無く4人を読めない。N のままにするか、平野さんが4人を調べて Y にするか
  5. 第4回の未登録の名前21名（海外の選手19名と伊藤正樹・保宏明）: 「連盟プロ以外」に英字の表記のまま足すか（足すと予選10本・ベスト16 2本と `live/wrc/4/q.html`・`b16.html` が出る）。伊藤正樹・保宏明は、連盟の別の表記の人か（「別名」）を確かめる
  6. U4「プレイヤー解説」: 実装しなかった（止まる条件）。名前は今の規則ですでに解説に入っていて、169本中162本で対局者と重なる（すべて麻雀日本シリーズ、掲載 Y は0本）。A 今のまま／B「プレイヤー解説：」を解説から外す規則にする。#688 と合わせて決めるか
- 未確認の項目:
  - 2026-11-17 04:00 JST の毎朝の取り込みで、第4回の18行が【2】の候補になり【3】に追記され、#475 に未登録の名前21名が出ること、`live/wrc/4/b32.html`・`b8.html` が本番に出ること（このセッションからは待てない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c77a96f2）: https://github.com/retroeater/mj-logs/tree/main/guide/c77a96f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
