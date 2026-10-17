# CHAT-1017-LVP-06

- 着手日時: 2026-10-17
- 対象issue: #362
- ブランチ: work/1017-lvp
- 着手時HEAD: b3ad7b4c3

## 指示

【Claude作成】Claude Code 向け指示：live/ をデザインの基本の形（面）に作り直す（自律開発、未公開のままプレビューで止める） Chat-Ref: CHAT-1017-LVP-06 マージ: 判断待ちで止まる（プレビューを平野さんとチャット側が見てから決める） 貼る時機: いつでも（推奨: 新しいセッション。LVP のセッションが長くなっているため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
/live の正式公開（#362）の前に、live/ をデザインの基本の形（面、docs/decisions/design-renewal.md）に作り直し、見た目に関わる公開準備の決定を入れて、未公開（noindex のまま）でプレビューまで作る。進め方は自律開発（不明な点は仮置きして最後まで作り、プレビューと一緒に仮置きをまとめて確かめる）。公開そのもの（noindex を外す・navbar・サイトマップ・301 など）は次の指示で行う。
決定（2026-10-16〜17、平野さん）

* live/ を、公開の前にデザインの基本の形（面）に作り直す。2026-10-12 の DSN の Q7（live/ は濃色の例外）と 2026-10-14 の SKD-04（live/ の写真カードは変えない）を置き換える
* 進め方は自律開発（最強戦の新しいページ〈saikyo/〉の形と部品を使う）
* トップ・タイトル戦・期のページは、最強戦の年度ページと同じ白地・面にする。個別ページは、最強戦の卓のページと同じく濃色のヒーローを残す例外にする
* トップ・タイトル戦・期のヒーローはなくす。題名を押すと切り替わる形（「十段戦 ▾」。最強戦の「麻雀最強戦2025 ▾」と同じ）にし、今のタイトル戦のプルダウンの代わりにする
* 検索欄は全ページに残し、今の動き（そのページの範囲でその場で絞り込む、docs/notes/live-page-design.md「2-8」）のまま、見た目を面の形にする
* 指示は2本に分ける。この指示（作り直し＋見た目に関わる決定、未公開のままプレビューで止める）と、マージの後の公開の指示
* この指示に入れる決定:
   * 共有ボタンを live/ から外す（#530。共通部品 `scripts/lib/share.py`・`assets/share.js` の扱いは #530 で決める）
   * パンくずは、最強戦の卓のページと同じく、ヒーローの外の左上に置く（個別ページ）。白地のページは最強戦の年度ページの形
   * 構造化データはパンくず（`BreadcrumbList`）だけを入れる。動画（`VideoObject` など）は入れない
   * `.mj-live-players .mj-saikyo-name` の上書きなど、作り直しで要らなくなる CSS を消す
   * メンバー限定の札は、live/ と女流勉強会（`study/`、未公開）で共通の部品にする。基準は live/ の形（文言「メンバー限定」、YouTube の表現に合わせる。判断 LV-26）。白地で読める色も決める（仮置き可）
   * トップの題（`<title>`・og:title）は「放送対局 | 番組 | ryoei.pro」。タイトル戦・期・個別の題は今のまま
   * トップ・タイトル戦・期の OGP 画像は、その範囲で最新の動画の YouTube のサムネイル（個別ページは今のまま）
   * 【3】の掲載が空欄の行は、行ごとの警告をやめ、「未確認 N 件」の合計の1行の警告にする
   * 名前の辞書の読み込み `load_name_book()` を1か所にまとめる（最強戦・/live・Mつくの3つ。2026-10-15 の MTK-08 の決定）
   * 写真の無い人は今のまま代わりの画像で生成を続ける（止めない）
   * カードの日付は今の「2026年8月27日」の形のまま

前提（チャット側。平野さんの決定ではない）

* 仮置きにしてプレビューで確かめるもの（ほかにも迷ったら仮置きして一覧に足す）: 期・タイトル戦のページの行（サムネイルの有無）、「〇〇の他の対局」の形、個別ページの選手の並べ方（最強戦の年度ページの行の形か写真カードか）、ダークモードの色、白地でのメンバー限定の札の色、共通ナビの色（白地のページは最強戦の新ページと同じ白地、個別ページは濃色）
* 部品と値の正は docs/notes/design.md と docs/decisions/design-renewal.md（SKD-01〜22）・docs/decisions/saikyo.md。最強戦の新ページの部品（`.mj-saikyo-n-*` など）は、共通にできるものは共通にしてよい（そのときは最強戦の生成物の見た目が変わらないことを確かめる）
* 2026-10-17 に平野さんが【3】の行3955・4166（第43期十段位決定戦のメンバー限定版）のステージ・ステージの並び・まとめ単位を「十段位決定戦」「8」「十段位決定戦」に直す予定（要確認。直っていなければ `live/judan/43/f.html` が生成されない。CHAT-1017-LVP-04 のログ）
* 決めたことの経緯は CHAT-1016-LVP-01〜02・CHAT-1017-LVP-03〜05 のログと docs/decisions/live.md

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。`python3 scripts/regenerate.py --check live_pages` を回し、止まる理由と警告を書く（`live/judan/43/f.html` が作られるか、上の行3955・4166 が直っているかも書く）。`git branch -r --no-merged origin/cloudflare` の各ブランチが、この指示で変える生成スクリプト・`style.css`・`assets/live.js`・`assets/share.js`・最強戦／Mつく／女流勉強会の生成の同じ関数・同じ規則を変えていないかを確かめる
2. 上の決定どおりに作り直す。確かめは、docs/notes/chat-routines.md「指示の種類ごとの確かめ」の「一覧の行の形を決める指示」に従い、端の実データ（いちばん長い名前、6名以上のページ、メンバー限定だけのページ、動画とライブが混ざるページ、複数の卓を続けて配信した動画、写真の無い人）の画面を 375px と 1280px の明・暗で撮る。開閉・検索中・0件などの部品の状態ごとの見え方も見る（DSN-11）。全ページを再生成し、live/ 以外の生成物の差分が0であること（最強戦・Mつく・女流勉強会は、共通にした部品・`load_name_book()` のせいで差分が出ていないこと）と、生成物の大きさの増減を書く
3. 作業ブランチに push してプレビューで止める（確認用の URL は最終報告にだけ書く）。`## 報告` の「判断が必要なこと」に、仮置きの一覧（項目・仮置きした形・ほかの案）と、プレビューで見てほしいページの一覧（上の端のデータのページを含む）を書く。上の「決定」を docs/decisions/live.md と docs/decisions/design-renewal.md（DSN の Q7・SKD-04 を置き換えた旨）に足す

止まる条件

* `--check live_pages` が止まる理由を出す（第43期十段位決定戦の URL の重なりだけなら止まらず、報告に書く）
* 未マージの work/ ブランチが、上の手順1のファイルの同じ関数・同じ規則を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* live/ 以外の生成物に、共通化で説明できない差分が出る
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-LVP-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-LVP-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1017-LVP-06` のコミットは0件。`LVP` は同じセッションの続き（推奨は新しいセッションだが、同じセッションで続けた）
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1017-lvp はローカル・リモートとも origin/cloudflare の祖先（LVP-05 でマージ済み）。`git merge --ff-only origin/cloudflare` で b3ad7b4c3 まで進めた

### 手順1: 着手前の確かめ

- #362 のコメント（41件）に他セッションの着手中は無し。着手中のコメントを書いた
- `python3 scripts/regenerate.py --check live_pages`: 通る（止まる理由なし、ページ 922）。「【3】手動補正」4,334行、「【2】自動変換後」14,200行、公開する動画 2,971本
  - 警告は1件: 公開しなかった【3】の行 753件（掲載が空欄 751 ＋ 配信予定 2〈行4316・4320、第24期プロクイーン決定戦のメンバー限定版〉）
  - LVP-04 で出た「URL が同じになる…live/judan/43/f.html」の警告は消えた。行3955・4166 は直っている（ページ数が LVP-04 の 921 から 922 に増えた）
- 未マージの work/ ブランチ（6本）:
  - work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes: docs/logs だけ
  - work/1017-amp: style.css の `#pros_table` の幅（別の箇所）。live/・最強戦・Mつく・女流勉強会の生成は触っていない
  - work/OUK: style.css（女流桜花の色・houou のコメント、別の箇所）、assets/share.js（コメント1行のみ）。live/ の生成・同じ関数は触っていない
  - 同じ関数・同じ規則を変えているものは無い

## 報告

- 状態: 作業中
- ブランチ: work/1017-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1017-lvp/docs/logs/CHAT-1017-LVP-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b3ad7b4c）: https://github.com/retroeater/mj-logs/tree/main/guide/b3ad7b4c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
