# CHAT-1104-TOP-11

- 着手日時: 2026-11-04
- 対象issue: #101, #231
- ブランチ: work/1104-top
- 着手時HEAD: 68f0411e8

## 指示

【Claude作成】Claude Code 向け指示：新しいナビ（細い帯＋メニューのシート＋右上の質問）を白地の新しい形のページに入れ、新トップから大会日程と一行の説明を外す（#101） Chat-Ref: CHAT-1104-TOP-11 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-top を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-top origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
スマホの新しい面（白地・400px）と合っていない共通ナビを、細い帯（左にロゴ、右に「質問」と「≡」）と下から開くメニューのシートに作り直し、白地の新しい形のページに入れる。右上の「質問」は、決めてある「サイトのどこからでも呼び出せる質問欄の入口」にする。あわせて新トップ `top/` から「開催中・直近の大会」の面と一行の説明を外す。
決定（2026-11-03〜04、平野さん。TOP 系列のチャットで）

* 新トップから「開催中・直近の大会」の面を消す（2026-11-03。TOP-02 の大会日程の面〈#231 (a)〉を置き換え）
* 新トップの一行の説明「日本プロ麻雀連盟の選手・大会・放送対局のデータ」を消す（2026-11-03）
* 質問欄の送信ボタンは「↑」のまま（2026-11-04）
* 共通ナビを作り直す。形は案 A: 細い帯の左にロゴ、右に「質問」と「≡」だけ。≡ を押すと下からシートが開き、項目をまとまりごとに並べる（チャット側の比較ページの案 A。まとまりの例: 選手〈連盟プロ〉・大会〈鳳凰戦・女流桜花・WRC・WRC-R・最強戦・タイトル戦〉・動画〈放送対局・帰り道・プロテスト・Mつく〉・その他〈リソース・良栄〉）（2026-11-04）
* 新しいナビを出す範囲は、白地の新しい形のページ全部（`navbar.js` が白地の新しい形にしているページ。houou/・ouka/・jwrc/・wrcr/・saikyo/・title/・pro/・r/・top/ など）。古い表のページ（黒地の Bootstrap のナビのページ）は今のまま（2026-11-04）
* 右上の「質問」は、2026-11-03 の決定「大会別の質問欄は残したまま、サイト横断で常に画面の右上などから呼び出せる1つの入口。開いているページの大会を既定の文脈にでき、トップでは5大会を横断して答える」の入口にする

前提（チャット側。平野さんの決定ではない）

* 今の `navbar.js`: 白地の新しい形にするページはパスの一覧（`newNavCurrent` の if の連なり）で決まり、項目は今のナビと同じ（要確認。実物の項目・ドロップダウンの中身・行き先を `## 経過` に表で写してから作る。シートのまとまりは上の例を基に、実物の全項目が漏れなく入るように仮置きで分ける）。ロゴ「R」は `/index.html` へ
* 未マージの work/ ブランチが `navbar.js` を変えている見込み（TOP-01 の調べ: work/1029-lgn-page〈`/log/` を白地の対象に足す〉・work/1101-prp-pub〈「連盟プロ」を `/pro/` に〉、RYO 系列の公開〈「良栄」を `/r/` へのリンクにしてドロップダウンをやめる〉）。新しいナビの項目の一覧は、これらの変更が後から入っても1か所を直せば済む形にし（項目の表を1つの定数にする等）、`## 経過` に重なりの見込みと取り込むときの解き方を書く
* 「質問」を押すと、下から質問欄のシートが開く（仮置き）。中身は新トップの横断の質問欄（`assets/top_ask.js`、`contest: "all"`）。開いているページが houou/・ouka/・jwrc/・wrcr/・saikyo/ のときは `context` にその大会の id を渡す（パスから決める）。新トップ（top/）ではページ上部の入力欄にフォーカスを移すだけでもよい（仮置き。どちらにしたかを書く）。大会別の質問欄（各大会のトップの欄）は今のまま残す
* シートの部品は、順位変動・連盟プロの絞り込みの「下から出るシート」と同じ形・同じ動き（閉じ方・フォーカスの戻し方・背景を押すと閉じる・Esc）。既存の部品があればそれを使う
* PC 幅でも同じ帯でよい（仮置き。PC 幅でシートを右から出すなどの違いは付けない）
* 新しいナビの JS・CSS は新しいファイルにしてよい（例 `assets/nav.js`・`assets/nav.css`）。白地のページだけが読む。`navbar.js` の古いナビ（黒地）の部分は変えない
* 「開催中・直近の大会」を消すと、TOP-02 の大会日程の判定（`load_desired()` の読み込み）・`SCHEDULE_MAX`・関連のテストも要らなくなる。消したことで毎朝の `update-live-channel.yml` の `top_page` がシートを読む必要が無くなるなら、その見込みを書く（ワークフローの変更はこの指示ではしない）。title/search.json に記録0件の大会も出す案（a）は、大会日程を消したので要らなくなる（作らない）
* #231 は、トップの大会日程の面を消したので、この issue の (a) は第一弾に入らなくなった。#231 に1行コメントし、本文・状態は変えない（閉じるかは平野さんに聞く。「判断が必要なこと」に書く）

手順

1. 今の `navbar.js` の白地のナビの対象パス・項目・行き先・ドロップダウンを `## 経過` に表で写す。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）で `navbar.js`・`style.css`・`scripts/generate_top_page.py`・`assets/top*.js` を変えているものを一覧にし、重なりと解き方の見込みを書く。#101 に着手中コメント
2. 新しいナビ（帯・≡ のシート・右上の質問のシート）を作り、白地の新しい形のページに出す。新トップから「開催中・直近の大会」の面と一行の説明を外す（使われなくなる関数・テスト・CSS も消す）。テスト（ナビの項目の漏れ・対象パス・質問の `context` の決め方・トップの面の並び）、`docs/notes/top-page.md`・`docs/notes/design.md`（ナビの節があれば）・`docs/decisions/top.md` を直す。共有の部品を変えるので、全ページを再生成して差分を確かめる（白地のページだけが変わり、古い表のページは変わらないこと）
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行・画面の確かめ（360px・400px・PC 幅、明暗。白地のページの代表として top/・houou/・pro/・title/ のそれぞれでナビ・≡ のシート・質問のシートを開いた状態。古い表のページの代表として jpml_pros.html が変わっていないこと）。ブランチ名の別名 URL でプレビューできる状態にし、仮置きの一覧をログに書いて止まる

止まる条件

* 白地の新しい形のページを `navbar.js` の対象パスから決められない（どのページが白地か一覧にして止まる）
* 未マージの work/ ブランチが `navbar.js` の同じ行・同じ関数を変えていて、取り込みで両方を残して解けない（解ける重なり〈対象パスの if の連なりに行を足すもの、項目の行き先の1行〉は止まらず、解き方を書く）
* 古い表のページ（黒地のナビ）の生成物・見た目に差分が出た
* 本番の質問の Worker（`workers/ask/`）を変える必要が出た（TOP は変えない。必要なことを書いて止まる）
* #101 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d772aeb2）: https://github.com/retroeater/mj-logs/tree/main/guide/d772aeb2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/68f0411e.md
