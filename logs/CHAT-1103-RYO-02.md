# CHAT-1103-RYO-02

- 着手日時: 2026-11-03
- 対象issue: #655（公開は #656）
- ブランチ: work/1103-ryo
- 着手時HEAD: cce7d2c3a

## 指示

【Claude作成】Claude Code 向け指示：r/ の仮置きへの回答を反映する（段位を名簿から・入賞をデータから・リーグの履歴の面をやめる・毎朝の再生成に足す。未公開・プレビューで止める） Chat-Ref: CHAT-1103-RYO-02 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1103-RYO-01 は判断待ちで止まっている。同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1103-ryo を続けて使う（RYO-01 の r/ の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/notes/static-generation.md`「ページの一覧」の冒頭の行など、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1103-RYO-01（判断待ち）の仮置きとデータの食い違いに平野さんが答えたので、r/ に反映する。公開はまだ行わない（#656）。
決定（2026-11-03、平野さん）

* 段位は連盟員名簿（`lib/meibo.py` が読む公式シート）から取る（手書きの「四段」をやめる）
* 「鳳凰戦の個人成績」の行の「最高 C1 · 出場 17期」はデータから出す（いまのまま）
* 入賞から「特別昇級リーグ」の2件（現行トップの「Tokusho League」）を除く。「タイトル戦」（title/ のデータ）に掲載があるものはデータから参照する。表記は日本語でよい
* 「鳳凰戦のリーグの履歴」の面はやめる。連盟プロの個人ページ（pro/）で参照できるものは、基本的にそちらに任せる
* `houou/results/平野良栄.json` に 36期後期〜39期後期の行が無いのは休場のため。データは正しく、直さない（「出場 17期」もそのまま）
* 毎朝の再生成の対象に r/（`ryoei_pages`）を足す

前提（チャット側。平野さんの決定ではない）

* 回答の無かった仮置きは、チャット側の推奨どおりにする（平野さんには「返事の無いものは推奨どおり」と伝えて選んでもらった。プレビューで覆ればまた直す）: 自己紹介の「ウェブサイト: ryoei.pro」の行を消す（肩書きは残す）／見出し「Facts」を日本語「数字で見る」にする／出演の WRC 2017 のインタビューの題を「【世界リーチ麻雀選手権2017インタビュー】」までに短くする。ほかの仮置き（面の並び・読み・押せないチップ・x.com・所属の手書き・1,098名・媒体の行・ICU の「第82回」・訳・フッターを移さない）はいまのまま
* 入賞の作り: `title/search.json` の `p` に平野良栄の決勝の行が2件ある（チャット側が実物で確認: `[259, 4]`＝第3期WRC-Rリーグ 4位〈2024〉、`[272, 2]`＝第29期新人王戦 2位〈2015〉。`m` の URL は `/title/jwrc-r/3.html`・`/title/shinjinou/29.html`）。この2件は pro/ の `title_wins()` と同じ読み方で名前から引き、題「第3期WRC-Rリーグ 4位」の形・2行目は年だけ（データに月が無い）・行全体を title/ の期のページへのリンク（›）にする。データに無い残り2件（「チャンピオンズリーグ 4位 · 2015年9月」「世界リーチ麻雀選手権 110位 · 2017年10月」）は手書きの定数のまま（仮置き。押せない行）。並びは新しい順。平野良栄の決勝の行が2件でなければ止まる
* 段位の作り: `lib/meibo.py` の `Member.rank` を名前（登録名）で引く。名簿を読むには鍵が要り、セッションの環境には無い（docs/notes/github-actions.md「regenerate-page.yml」）ので、生成は `regenerate-page.yml` を作業ブランチの ref・入力 `check`・`target_page: ryoei_pages` で手動実行して行う（名簿に平野良栄が1件でなければ止まる仕組みにする）。鍵の無い環境で `--check` が通るかの扱いは pro/（`generate_pro_pages.py`）と同じにする
* 毎朝の再生成: `.github/workflows/update-live-channel.yml` の `regenerate` ジョブの `target_page: live_pages title_pages pro_pages` に `ryoei_pages` を足す（`ryoei_pages` は `RUN_LAST` で `pro_pages` の後）。呼ぶ側は run-daily.yml（`workflow_call`、Worker からの予約の起動）と `workflow_dispatch`（要確認: `git grep -n update-live-channel .github/workflows/` で洗う）。`update-live-channel.yml` 自体の手動実行は行わない（`regenerate` ジョブは `apply: true` のときだけ動き、`apply: true` は本番の /live のシートへ書き込むため）。代わりに、変えた行が渡す値そのものを `regenerate-page.yml` の手動実行（上の段位の生成と同じ run でよい。`target_page: live_pages title_pages pro_pages ryoei_pages` にして4ページの順と成否を見る）で確かめ、ログに run の番号と結果を書く。docs/notes/github-actions.md「ワークフローの一覧」に r/ が毎朝の対象になる旨を足す。`簡略化:` のコメント（毎朝の対象外）は消す
* 画面の確かめ: 360px・390px の明暗で撮り直し、`docs/logs/CHAT-1103-RYO-02/` に置く。RYO-01 と比べて高さが減った分を報告に書く。ログに「この版で見えるはずの印」を1つ書く
* RYO-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-RYO-02` を足す

手順

1. #655 に着手中コメントを書く。上の「前提」の実物（`title/search.json` の2件・`Member.rank`・`update-live-channel.yml` の呼び出し元）を確かめ、食い違えば止まる。origin/cloudflare を取り込む
2. 決定と前提のとおりに `scripts/generate_ryoei_pages.py`・検査・`update-live-channel.yml`・docs を直し、`regenerate-page.yml` を作業ブランチで手動実行して `r/index.html` を生成する（手動実行が 403 なら平野さんに GitHub の「Run workflow」を頼む旨を書いて止まる）
3. 検査と画面の確かめを流し、残る仮置き（手書きの入賞2件など）をログに書く。ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* 前提が実物と食い違う（`title/search.json` の平野良栄の決勝が2件でない、名簿に平野良栄が1件でない、`update-live-channel.yml` の `target_page` の行が前提と違う）
* テスト・`--check`・`regenerate-page.yml` の手動実行が通らない（今回の変更によるもの。無関係な失敗は原因を書いて残りを進めてよい。自分の変更で落ちる検査は直してよい）
* 未マージの work/ ブランチが `.github/workflows/update-live-channel.yml`・`scripts/regenerate.py`・`scripts/lib/meibo.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 他セッションの着手中コメントが #655 にある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-RYO-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-RYO-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1103-ryo
- ログ: https://github.com/retroeater/mj/blob/work/1103-ryo/docs/logs/CHAT-1103-RYO-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-ryo
- 確認用URL:
- マージ: 未
- issue: #655
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 46a47f07）: https://github.com/retroeater/mj-logs/tree/main/guide/46a47f07

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad26c534.md
