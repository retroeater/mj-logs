# CHAT-1104-SKN-03

- 着手日時: 2026-11-05
- 対象issue: #641（コメントのみ）
- ブランチ: work/1104-skn
- 着手時HEAD: e12fac15e

## 指示

【Claude作成】Claude Code 向け指示：SKN-02 の続き（仮置きへの回答：勝敗の書き方・相手の写真・同卓の削除・写真だけ押せる・import の輪を lib/ で解く） Chat-Ref: CHAT-1104-SKN-03 マージ: 判断待ちで止まる 貼る時機: いつでも（SKN-02 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-skn を続けて使う（SKN-02 の実装の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-SKN-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-SKN-03` を足して push する。

目的
CHAT-1104-SKN-02 の「判断が必要なこと」への回答と、チャット側がプレビューで見つけた点の直し。
決定（2026-11-05、平野さん）

1. 「対戦成績」タブの勝敗は「2勝2敗2分」（選んでいる選手から見た数、0 の項目も出す）のまま（仮置き2を確定）
2. 「対戦成績」タブの行に、対戦相手の写真を入れる（ほかの行と同じ 40px の丸。写真の無い人は名前の頭の1字の灰色の丸）
3. 「対戦成績」タブの行から「同卓 n回」を削除する。並びは今のまま（対戦回数の多い順、同じ回数なら直近の対戦日が新しい順）
4. 年度・対局のページの選手の行には › を付けない。行全体ではなく、選手の写真（プロフィールのアイコン）を押したときだけ個人成績へ飛ぶ（仮置き6を置き換え）。卓の見出しの行の › と行き先は今のまま
5. 卓のページの「出場者」の写真カードも、写真を押したときだけ個人成績へ飛ぶ。X へのリンクはカードから外す（X へは個人成績のアイコンから行く）。司会・実況・解説のカードは今のまま（仮置き4を置き換え）
6. 「対戦成績」タブの行数は上限なし（仮置き5を確定）
7. import の輪（saikyo → pro_pages → live_pages → saikyo）は、アイコンの部品（`SNS_ICONS` など）を `scripts/lib/` へ移して解く。pro/ の生成もそこを読むようにする

前提（チャット側。平野さんの決定ではない）

* 仮置き1（タブの URL `?view=vs`・対戦回数＝同卓の回数）と仮置き3（アイコン 22px・押せる大きさ 44px・名前と「N回出場」の行の下）は、平野さんの明示が無いので仮置きのまま。報告の「判断が必要なこと」に残す
* 決定4・5の写真のリンクは、読み上げで行き先が分かる名前（例「<選手名>の個人成績」）を付け、押せる大きさ 44px 以上にする案。写真の見た目の大きさは変えない
* 決定7で pro/ の生成が変わるので、work/1101-prp-pub との重なりが増える。増えた分を SKN-02 で #641 に書いた重なりの一覧に追記する（新しいコメントで。本文・状態・ラベルは変えない）
* 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す（ブランチ名 `cloudflare` は解決できない。2026-11-05 の run 41855451367 の失敗、ACF からの伝言）

手順

1. 決定1〜7を入れ、テスト（scripts/tests/ の全体）を通す。決定7の移動では、pro/ の生成物が変わらない（差分 0）ことを確かめる。
2. saikyo_pages を再生成し、プレビュー（ブランチ名の別名 URL）で、年度・対局のページ（写真だけ押せる、› が無い）、卓のページの出場者（写真だけ押せる、X のリンクが無い）、個人成績の「対戦成績」タブ（写真・同卓の削除）を確かめる。スクリーンショットを docs/logs/CHAT-1104-SKN-03/ に置く。
3. docs/notes/saikyo-page-design.md・docs/decisions/saikyo.md を上の決定に合わせて直し、#641 に重なりの追記のコメントを書き、仮置き1・3を「判断が必要なこと」に残して止まる。

止まる条件

* SKN-01 の止まる条件（work/1101-prp-pub との重なりを除く）
* 決定7の移動で pro/ の生成物に差分が出た（出たファイルと行を全部書く）
* 「対戦成績」タブの勝敗の数が、対戦成績のページ（`?name=A,B`）の数と1組でも合わない（合わない組を全部書く）
* 再生成の差分に、この変更とその日のシートの変化で説明できないものがある（ページの種類ごとに変わった数をログに書く）
* マージは判断待ち。cloudflare には入れない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1104-skn
- ログ: https://github.com/retroeater/mj/blob/work/1104-skn/docs/logs/CHAT-1104-SKN-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-skn
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: #641
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fa43459d）: https://github.com/retroeater/mj-logs/tree/main/guide/fa43459d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1a06d89d.md
