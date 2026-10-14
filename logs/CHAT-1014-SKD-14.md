# CHAT-1014-SKD-14

- 着手日時: 2026-10-15
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 27fe15afd

## 指示

【Claude作成】Claude Code 向け指示：最強戦のトップ（歴代最強位）の2案（A 写真カードを残す／B 面の行）を比較ページで並べる — 実データ、プレビューで止まる（#566） Chat-Ref: CHAT-1014-SKD-14 マージ: 判断待ちで止まる（平野さんがプレビューで2案を見比べて決める） 貼る時機: いつでも（CHAT-1014-SKD-13 は完了・マージ済み〈6ae903ee9〉。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-skd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-skd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#566 の最新のコメントを読み、他のセッションの着手中コメントがあれば止まる。

目的
#566 の最後の残り、最強戦のトップ `saikyo/index.html`（歴代最強位と選手名の検索）の形を、2案を実データで並べた比較ページで平野さんが決める。本体のトップは変えない。
決定（2026-10-14〜15、平野さん）

1. トップは A（歴代最強位を写真カードのまま）と B（歴代最強位を面の行にする）を比較ページで見比べて決める
2. 選手名の検索の結果は、卓ごとの影付きのカードをやめ、年度ページと同じ面の行の形にする（A・B 共通）
3. トップの共通ナビは白地にする（2026-10-14 の決定。SKD-12 では入れず、トップの指示で検索欄と一緒に変えるとしたもの）

前提（チャット側。平野さんの決定ではない）

* 両案に共通の上の部分: 白地のナビ（SKD-12 の `.mj-nav-new` の出し分けを比較ページにも当てる）→ 28px 太字の題名「最強戦」→ 1行の選手名の検索欄（houou/ トップの質問欄と同じ形の入力欄。虫眼鏡のアイコン、44px 以上）→ 13px 灰色の「歴代最強位」。今の濃色の固定バー（年度のプルダウンと検索欄）は置かない（年度へは歴代最強位の行・カードから行く）
* A: 歴代最強位は今の写真カード（年度の帯・名前の帯）のまま2列。影と枠を外し角丸 8px（SKD-03 の B と同じ扱い）
* B: 歴代最強位は1枚の面に年度ごとの行（高さ 56px。左から年度 13px 灰色、写真 40px の丸〈写真が無ければ頭の1字の丸〉、名前 16px、右端の ›）。行を押すとその年度の新しい年度ページ `saikyo/<年度>/index.html` へ
* 最強位がまだ決まっていない年度（今の 2026）の名前の欄は、比較ページでは仮に「開催中」（13px 灰色）とする（言葉は平野さんが別に選ぶ。比較ページの見た目の判断には関わらない）
* 検索の結果（決定2）: 検索欄に名前を入れると、歴代最強位の上（または代わり）に、その選手の出た卓を1枚の面の行で出す（行の中身は今の検索結果と同じ情報〈年度・対局・卓・結果〉を、年度ページの行と同じ字の段で）。行を押すと今と同じ行き先（卓または対局のページ。今の行き先を確かめて同じにする）。A・B で同じ
* 比較ページの作り方と置き場所は SKD-03 と同じ（ページ内のラジオボタンで A／B を切り替え、noindex・どこからもリンクしない・sitemap に載せない・`_redirects` に吸われない所。SKD-03 のログ `## 経過` 1）。検索は比較ページでも実際に動くこと
* 暗い配色も両案で作る（新しい年度ページと同じ値）
* 確かめ（DSN-07 以降の定型）: 375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px、灰色の字の条件、部品の状態ごとの高さ（検索の前・結果あり・結果なし）。390px で A と B の「歴代最強位の高さ」と「1画面（844px）に入る年度の数」を測って表にする
* 比較ページの外のページ（トップ・年度ページ・houou/）の見た目が変わらないこと
* ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 比較ページの先頭のボタンの文言が「A 写真カード」「B 面の行」。URL は書かない）

手順

1. #566 に着手中のコメントを書く。トップの今の作り（`scripts/generate_saikyo_pages.py` のトップの関数・`assets/saikyo.js` の検索）を読み、比較ページの置き場所と作り方を決めてログに書く。未マージの work/ ブランチが同じ行・同じ関数を変えていないかを確かめる
2. 比較ページを作り、上の確かめを行う
3. 作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に比較ページのプレビューの URL を書く

止まる条件

* #566 に他のセッションの着手中コメントがある
* 本体の生成物（`saikyo/` のページ）に差分が出る
* 比較ページの置き場所が衝突し、衝突しない置き場所が無い
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 比較ページの置き場所・作り方、検索の行き先、確かめの結果、A と B の高さと1画面の年度の数の表、確認用URL の印がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 27fe15af）: https://github.com/retroeater/mj-logs/tree/main/guide/27fe15af

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/27fe15af.md
