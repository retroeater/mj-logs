# CHAT-1006-LGR-11

- 着手日時: 2026-10-06
- 対象issue: #508（#507）
- ブランチ: work/1006-lgr-10
- 着手時HEAD: 34d6b182

## 指示

【Claude作成】Claude Code 向け指示：houou_race の再生ボタンが隠れる不具合を直し、公開の変更（#508）を cloudflare へマージして公開する。公開後の確かめと、#507・#508 のクローズまで Chat-Ref: CHAT-1006-LGR-11 マージ: 承認済み（チャットで、2026-10-06。平野さんがプレビューを見て「マージしてよい」とした）。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1006-LGR-10 のセッションの続きに貼ってよい） 作業ブランチ: クラウドセッションで実行する。未マージの work/1006-lgr-10 を続けて使う（CHAT-1006-LGR-10 の公開の変更に直しを足してマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-lgr-10 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1006-LGR-10 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1006-LGR-10 のプレビューを平野さんが確かめ、公開してよいとした。プレビューで見つかった再生ボタンの不具合を直したうえで、cloudflare へマージして houou_race を公開する（#508）。
決定（2026-10-06、平野さん）

* プレビューを見て、マージ（公開）してよい
* 公開の前に Search Console の数字は残さない
* 共有ボタンと OGP は今のまま公開する（共有ボタンなし、og:image はサイト共通の画像、og:title は title と同じ）
* 不具合の報告: 24後 A1 で、人数が10人で少ないためか、再生ボタンが隠れてしまう（スマホだと問題なし。PC のブラウザで起きた）。平野さんの画面では、再生ボタンが表の下の端で切れて、上の半分だけが見えていた

前提（チャット側。平野さんの決定ではない）

* CHAT-1006-LGR-10 のログ（mj-logs）で確かめたこと: 状態は判断待ち、変更は 490a610f（noindex を外す、navbar.js「鳳凰戦」の末尾、`sitemap-pages.xml`、`llms.txt`、`houou_race/24-2.json`、docs）。24後 A1 は10名とも8節で、途中で終わる選手は無い
* 不具合の原因の見込み（未確認。実物で再現して確かめる）: 再生ボタンの縦の位置を、表の今の見た目の高さ（`getBoundingClientRect()`）から計算している。表の高さは切り替えの時に動きながら変わるので、人数の多い表から少ない表へ切り替えた直後に前の表の高さで計算し、ボタンが新しい表の下の端にかかって、表の `overflow` で切れる。スマホでは画面の高さのほうが小さく、見えている範囲で計算するので起きにくい。平野さんの画面の位置（ボタンの中心が、10行の表の下の端）は、前の表が20行だった場合の中央と合う
* 直し方の案（付録）: 表の高さの行き先の値を変数に持ち、ボタンの位置はその値で計算する。位置は、ボタンが必ず表の中に収まる範囲に丸める。原因が見込みと違っていたら、実際の原因に合わせて直し、原因と直し方を報告に書く（止まらない）
* 修正の確かめは、CLAUDE.md「判断・作業の原則」のとおり、先に直す前のコードで再現する: PC の幅（1280px 前後）で、人数の多い表（例: 既定の 43前 B1 や、25前 D2）から 24後 A1 へ切り替え、再生ボタンの四辺が表の中に収まっていないことを数値で確かめる。直した後、同じ手順で収まることを確かめる。スマホの幅（390px）でも位置が変わっていないことを見る
* 公開後の確かめは docs/new-page-checklist.md「公開後の確かめ」のとおり。セッションから確かめるのは、check-run と本番の HTML まで。X・LINE のカード表示、実機、Search Console は平野さんが行う（チャット側から伝えてある）
* マージの後の自動の再生成（regenerate-page.yml）で、houou_race.html と `houou_race/` がシートの今の値で作り直されることがある。ほかのページの生成物に差分が出ても、この指示とは関係が無く、シートの変化として扱う
* #507 と #508 は、公開後の確かめが済んだら閉じる（公開した日とマージの SHA をコメントし、「状況:」ラベルを外す）。共有ボタンと専用の OGP 画像は、平野さんが「今のまま」と決めたので、残りの作業として起票しない
* 使う skill は無い

手順

1. 再生ボタンを直す。#508 に着手中のコメントを残す。直す前のコードで再現し（数値をログに書く）、原因を確かめて直し、同じ手順で直ったことを確かめる。CHAT-1006-LGR-10 のログの `## 報告` の状態を「判断待ち（続き: CHAT-1006-LGR-11）」にする
2. プレビューで確かめ、マージする。PC の幅で 24後 A1 と、人数の多い表・少ない表の切り替えを数通り、スマホの幅（390px）で既定の表を確かめる。メニュー「鳳凰戦」の末尾から開けること、noindex が無いことも見る。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする
3. 公開後の確かめと片付け。check-run と、本番の HTML（https://ryoei.pro/houou_race.html が 200 で、robots の noindex が meta にも応答ヘッダの `x-robots-tag` にも無い。本番の navbar.js・`sitemap-pages.xml`・`llms.txt` に houou_race がある）を確かめる。待つのは15分までで、超えたらその時点の状態を「未確認の項目」に書いて先へ進む。確かめが済んだら、#508 と #507 に公開した日とマージの SHA をコメントして閉じる

止まる条件

* CHAT-1006-LGR-10 の状態が判断待ちでない。#508・#507 に他セッションの着手中コメントがある
* 再生ボタンの不具合が再現できない（試した切り替えと数値を書いて止まる。マージしない）
* cloudflare に入る変更が、次で説明できる差分だけでない: houou_race.html・houou_race.js・`houou_race/`・style.css の houou_race の節・scripts/generate_houou_race.py・scripts/tests/test_houou_race.py・navbar.js・`sitemap-pages.xml`・`llms.txt`・docs/・再生成によるシートの変化の反映
* unittest か CLAUDE.md の検証が通らない。プレビューで横のはみ出し・ページのエラー（Cloudflare Web Analytics の beacon を除く）がある
* origin/cloudflare の取り込みで、自分で直せない衝突が出た（navbar.js・`sitemap-pages.xml`・`llms.txt` は、ほかのセッションの変更と重なりやすい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。ほかのセッションの push と重なって拒否されたときは、取り込み直して差分を確かめ、もう一度だけ行ってよい）
* 本番の確かめで、noindex が残っている、または navbar.js・`sitemap-pages.xml`・`llms.txt` に houou_race が無い（issue は閉じずに、状態を書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、不具合の原因と直し方、直す前と後の数値、本番で確かめた結果、平野さんが行うこと（docs/new-page-checklist.md「公開後の確かめ」のうち平野さんの分。本番の URL を添える）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-11 を書く

付録: 直し方の案（Claude 作成。試作の該当の関数）
試作では、表の高さを決める所で `tableH`（行き先の高さ、px）に入れてから `style.height` に設定している。変数名（`tableEl`・`nodesEl`・`goBtn`・`tableH`）は試作のもので、本番の名前に合わせる。

```js
function placePlay(){            // 再生ボタンを、表の見えている部分の中央に置く
  // 表の高さは動きながら変わるので、今の見た目の高さではなく行き先の高さ（tableH）で計算する。
  // 人数の少ない表へ切り替えた直後に、前の表の高さで計算すると、ボタンが表の下にはみ出して隠れる
  const r = tableEl.getBoundingClientRect(), head = nodesEl.getBoundingClientRect().bottom, half = goBtn.offsetHeight / 2 || 46;
  const top = Math.max(r.top, head), bot = Math.min(r.top + tableH, window.innerHeight);
  const c = bot > top ? (top + bot) / 2 - r.top : tableH / 2;
  goBtn.style.top = Math.max(half, Math.min(tableH - half, c)) + 'px';   // 必ず表の中に収める
}

```

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1006-LGR-11"` は0件（同じチャットの LGR の続き）
- ブランチ: work/1006-lgr-10 はローカルとリモートで一致（34d6b182）。`git merge-base --is-ancestor origin/cloudflare HEAD` は真
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致。CHAT-1006-LGR-10 の `## 報告` の状態は「判断待ち」
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている


### 手順1 再生ボタンの不具合

- #508・#507 に他セッションの着手中コメントは無い。#508 に着手中のコメントを残した。CHAT-1006-LGR-10 のログの状態を「判断待ち（続き: CHAT-1006-LGR-11）」にした
- 再現（直す前の houou_race.js、手元のサーバ、Playwright の Chromium）。既定 43前 B1 → 24後 A1 → 25前 D2（76行）→ 24後 A1 と切り替え、1秒待ってボタンと表の位置を取った（px、画面の上端から）

| 画面 | 表 | 表の上〜下 | ボタンの上〜下 | 表の中か |
|---|---|---|---|---|
| 1280×900 | 24後 A1（B1 から） | 390〜730 | 600〜692 | 中 |
| 1280×900 | 24後 A1（25前 D2 から） | 390〜730 | 600〜692 | 中 |
| 1920×1080 | 24後 A1（B1 から） | 390〜730 | 617〜709 | 中 |
| 1920×1080 | 24後 A1（25前 D2 から） | 390〜730 | 690〜782 | **はみ出す**（下の半分が切れる） |
| 1440×1300 | 24後 A1（25前 D2 から） | 390〜730 | 800〜892 | **はみ出す**（全部が表の外） |

- 原因: `placePlay()` がボタンの位置を、表の今の見た目の高さ（`getBoundingClientRect()`）と画面の下端の小さいほうで計算している。表の高さは CSS で動きながら変わるため、
  人数の多い表から少ない表へ切り替えた直後には前の表の高さで計算され、ボタンが新しい表の下の端をまたぐ（表は `overflow: clip` で切れる）。
  画面の高さが表の下端より小さいときは画面の高さで計算されるので、スマホや高さの小さい画面では起きない。見込みのとおり
- 直し方（付録のとおり）: 表の高さの行き先を `tableH` に持ち（`layout()` で `style.height` と一緒に入れる）、`placePlay()` はそれで計算し、ボタンの半分の高さ以上・表の高さ−半分以下に丸めて必ず表の中に収める
- 直した後（同じ手順）: 1920×1080・1440×1300・1280×900 とも 24後 A1 でボタン 515〜607（表 390〜730）で中。追加の切り替え（1920×1080: 43前 B1→38前 E1 ①組〈55行〉→38後 A1〈13行〉→23前 D2〈55行〉→23後 A2〈13行〉→42後 A2〈16行〉）もすべて中。エラーなし
- 390×780: 既定（43前 B1）のボタンは 528〜620 で、直す前のコードが動いている本番と同じ。24後 A1 は直す前 528〜620、直した後 491〜583（どちらも表 366〜706 の中。直した後は表の見えている部分の中央）

## 報告

- 状態: 作業中
- ブランチ: work/1006-lgr-10
- ログ: https://github.com/retroeater/mj/blob/work/1006-lgr-10/docs/logs/CHAT-1006-LGR-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-lgr-10
- 確認用URL: なし（作業中）
- マージ: 未
- issue: #508（#507）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 01b946ae）: https://github.com/retroeater/mj-logs/tree/main/guide/01b946ae

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/01b946ae/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/01b946ae/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/01b946ae/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/01b946ae/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/01b946ae/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/01b946ae/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/824dc807.md
