# CHAT-1104-SKN-10

- 着手日時: 2026-11-05
- 対象issue: #669（コメントのみ）
- ブランチ: work/1105-skn-gutter
- 着手時HEAD: 275d81111

## 指示

【Claude作成】Claude Code 向け指示：番組の一覧で PC 幅だけ説明文と全幅の並びが左へ 7.5px ずれるのを直し、条件を満たせば cloudflare へマージする Chat-Ref: CHAT-1104-SKN-10 マージ: 承認済み（チャットで。下の「止まる条件」に当たらないときだけ） 貼る時機: いつでも（新しいセッションでも、SKN-09 と同じセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-skn-gutter を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-skn-gutter origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#669 の残りの1つ。番組の一覧（wayhome/・exam/・mtsuku/・study/）で、PC 幅（縦のスクロールバーが幅を取る環境）だけ、説明文と全幅の並びが左へ 7.5px ずれる（CHAT-1104-SKN-08 の調べ: scrollbar-gutter と 100vw の組み合わせ。はみ出しではない。スマートフォンでは起きない）。これを直す。
決定（2026-11-05、平野さん）

* このずれを直す（番組のページは意図した例外の濃色の作りのままで、ずれだけを直す）
* 下の「止まる条件」に当たらなければ cloudflare にマージしてよい

前提（チャット側。平野さんの決定ではない）

* 直し方は実物に合わせて決めてよい（例: 全幅の並びの幅を `100vw` でなく、スクロールバーを除いた幅で取る）。番組のページの濃色の作り・幅（1100px や画面いっぱいの列）は変えない
* 同じ `100vw` の使い方がほかのページ（放送対局・最強戦の卓のページなど）にもあれば、同じずれが出ていないかを測り、出ていれば同じ直し方で直してよい（直したページと測った値をログに書く）
* 測り方: headless Chromium で、縦のスクロールバーが幅を取る設定（例: `--hide-scrollbars` を付けない、または `::-webkit-scrollbar` の幅を持たせる）の 1280px と、390px で、説明文と全幅の並びの左端を測る
* 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す
* その時点の mj-logs の actions/morning.md の「1. 失敗など」にある失敗のうち、work/1105-skn-gutter 以外のブランチのものは、この変更と無関係として扱う

手順

1. 同じ論点の open issue（#669 以外）・他セッションの着手中コメントが無いことを確かめる。上の測り方で直す前のずれ（4つの一覧と、同じ使い方のあるページ）を測ってログに書き、直す。
2. 直した後に同じ測り方で測り、1280px でずれが 0、390px で直す前と同じ位置であることを確かめる。テスト（scripts/tests の全体）と `python3 scripts/regenerate.py all --check` を通す。変わる生成物（CSS だけなら生成物の差分は無い見込み）を確かめる。
3. cloudflare にマージし、マージ後の check-run を確かめ、本番（https://ryoei.pro/wayhome/ ほか3つの一覧）を同じ測り方で測る。#669 に、直したことをコメントする（本文・状態・ラベルは変えない。title/ は TTL に依頼済みなので、#669 は閉じない）。

止まる条件

* 同じ論点の open issue・他セッションの着手中コメントがある
* 直した後も 1280px でずれが残る、または 390px の位置が直す前と変わる（測った値を全部書く）
* 番組のページの見た目に、ずれ以外の差（幅・地の色・題名など）が出た
* テストが落ちる、または `regenerate.py all --check` に止まる理由がある（この変更で落ちると分かっているテストは直してよい。直せなければ止まる）
* 生成物に、この変更とその日のシートの変化で説明できない差分が出た（出たファイルと行を全部書く）
* 未マージの work/ ブランチが同じ CSS の節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* マージ後の check-run の失敗のうち、この変更によるもの（無関係なら原因を報告に書いて残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `CHAT-1104-SKN-10` のコミットは全ブランチに無し。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠け無し。CLAUDE.md は起動時から変わっていない
- 0. の確認: 「指示」欄の末尾は指示文の最後の行と一致
- 作業ブランチ: work/1105-skn-gutter はローカルにもリモートにも無かったので、`git checkout -b work/1105-skn-gutter origin/cloudflare`（275d81111）で作った
- 同じ論点の open issue と着手中のコメント
  - `search/issues`（scrollbar・スクロールバー・100vw・fullbleed・「全幅 ずれ」）で探して、出たのは #669 だけ
  - #669 の最後のコメントは自分の SKN-09 のもので、ほかのセッションの着手中のコメントは無い
- 未マージのブランチ: style.css を変えているのは work/1101-prp-pub だけ（pro/ の節の見出しのコメント1行。番組の節とは別の所）
- `100vw` の使い方（style.css）
  - 全幅の並びにしているのは、`body:has(.mj-video-list) .mj-fullbleed` と `body:has(.mj-video-list) .mj-lead` の `margin-inline: calc(50% - 50vw)` の2つだけ
  - `.mj-video-list` を持つのは wayhome/・exam/・mtsuku/・study/ の一覧の4ページだけ
  - ほかの `100vw`（`.mj-share-toast`・`.mj-book-toast` の `max-width: calc(100vw - 32px)`〈position: fixed で画面に対して置く〉、余白の clamp の2つ）は位置をずらさない
  - 放送対局・最強戦の卓のページは全幅の並びを使っていないので、同じずれは無い
- 測り方: headless Chromium（Playwright）。1280px は既定（縦のスクロールバーが幅を取る。`html` の `scrollbar-gutter: stable` で溝 15px）。390px は `isMobile`（スマートフォンと同じ、重ねて出すスクロールバー）。全幅の並び（`.mj-fullbleed`）・説明文（`.mj-lead`）・ヒーロー・題名の左右の端を測った
- 直す前（本番、275d81111）: 4つの一覧とも同じ値
  - 1280px: 全幅の並び -7.5..1272.5、説明文 -7.5..1272.5、ヒーロー 0..1265、文書の幅 1273
  - 390px: 全幅の並び・説明文・ヒーローとも 0..390
- 直し方（8cf27d7b8）: `body:has(.mj-video-list)` に `container-type: inline-size` を付けた。全幅の並びと説明文の `margin-inline` を `calc(50% - 50vw)` から `calc(50% - 50cqi)` にした
  - `100vw` はスクロールバーの溝を含むが、`cqi` は `<body>`（溝を除いた幅）を基にするので、ずれない
  - inline-size の包含は、固定の要素の包む箱にならず、sticky も壊さない
  - `<body>` の中で `cqi` を使う部品（写真カードなど）は、自分のコンテナを持つので変わらない
- 直した後（ローカルとプレビュー〈8cf27d7b8 のビルド〉で同じ値）: 4つの一覧とも同じ値
  - 1280px: 全幅の並び 0..1265、説明文 0..1265、ヒーロー 0..1265（ずれ 0）、文書の幅 1265
  - 390px: すべて 0..390（直す前と同じ）
- ずれ以外の差の確かめ（本番とローカルで、`<main>` の中の全要素の位置・大きさ・字の色・地の色・字の大きさを比べた）
  - 390px: 4つとも、違う要素は0
  - 1280px: 字の色・地の色・字の大きさは4つとも同じ。位置と大きさは、全幅の並びの中の要素が変わった（wayhome 445件・exam 123件・mtsuku 610件・study 405件）
    - 全幅の並びの幅が 1280 → 1265 になった（それまでは左の 7.5px が画面の外、右の 7.5px が溝の下にあった）
    - 中の余白 `clamp(12px, 3vw, 48px)`（1280px で 38.4px）が、左右とも見えるようになった
  - カードの並び（1280px、4つとも同じ値）
    - 直す前: 1行に4枚、カードの幅 285.8px、並びの左右の端 30.9..1234.1（見える左右の余白は 30.9px ずつ）
    - 直した後: 1行に4枚、カードの幅 282.0px、並びの左右の端 38.4..1226.6（左右の余白は 38.4px ずつ）
  - 直す前も、見える範囲では左右の余白がそろっていた（30.9px ずつ）。直したことで、余白が CSS の値（38.4px）になり、カードが 3.8px 狭くなった。止まる条件の「番組のページの見た目に、ずれ以外の差（幅…）が出た」に当たると判断し、マージせずに止めた
- スクリーンショット: docs/logs/CHAT-1104-SKN-10/（wayhome の before〈本番〉と after〈プレビュー〉の 390px・1280px）
- テスト: `python3 -m unittest discover -s scripts/tests` → 2031件 OK
- `regenerate.py all --check`（run 41973893501）: 「check: 止まるページなし」
- 新旧の生成物の比べ（`compare_ref=275d81111a828…`〈その時点の origin/cloudflare〉、run 41973907716）: 「compare: 差分なし」。CSS だけの変更で、生成物は変わらない
  - 1回目の起動（run 41973897823）は compare_ref の SHA を打ち間違えて `fatal: invalid reference` で落ちた。正しい SHA で起動し直したのが上の run

## 報告

- 状態: 判断待ち（止まる条件: 1280px でずれ以外の差〈カードの幅 -3.8px・左右の余白 30.9 → 38.4px〉が出た） / 続き: CHAT-1104-SKN-11
- ブランチ: work/1105-skn-gutter
- ログ: https://github.com/retroeater/mj/blob/work/1105-skn-gutter/docs/logs/CHAT-1104-SKN-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-skn-gutter
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページ: wayhome/・exam/・mtsuku/・study/ の一覧。この版の印: 1280px で全幅の並びの左右の余白が 38.4px ずつ（直す前は 30.9px）
- マージ: 未（判断待ち）
- issue: #669（コメントはマージの後に書く）
- 判断が必要なこと:
  - 直すと、1280px（縦のスクロールバーが幅を取る PC）で、全幅の並びのカードが 285.8 → 282.0px（1行4枚のまま）、左右の余白が 30.9 → 38.4px になる。直す前も見える範囲の左右の余白はそろっていて、ずれは画面の外（左 7.5px）と溝の下（右 7.5px）にあった。この版でマージしてよいか。スマートフォン（390px）は何も変わらない
- 未確認の項目: 平野さんの実機（PC）での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5ef09b7d）: https://github.com/retroeater/mj-logs/tree/main/guide/5ef09b7d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5ef09b7d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5ef09b7d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5ef09b7d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5ef09b7d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5ef09b7d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5ef09b7d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
