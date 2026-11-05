# CHAT-1106-LGV-04

- 着手日時: 2026-11-06
- 対象issue: なし（#640 は参照）
- ブランチ: work/1105-lgv
- 着手時HEAD: 58603d8f8

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の告知動画を確定し、制作のスクリプトと文書を cloudflare へマージする Chat-Ref: CHAT-1106-LGV-04 マージ: 承認済み（チャットで、2026-11-06、平野さん）。変えるのは告知動画の制作のスクリプトと文書だけで、log/ の検索・現在地・カテゴリの機能は変えない（告知の期間中の承認の対象外。対象の変更が混ざれば下の止まる条件で止まる） 貼る時機: いつでも（CHAT-1105-LGV-02 の続き。LGV-02 と同じセッションでよい。LGV-03 とは別のブランチ） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-lgv を続けて使う（CHAT-1105-LGV-01・02 の制作のスクリプトがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1105-LGV-02 のログの `## 報告` が「判断待ち」で、`scripts/promo_video/log/` が work/1105-lgv にあることを確かめ、そうでなければ何もせずに止まる。

目的
「ログ」（log/）のリニューアルの告知動画を第2版（CHAT-1105-LGV-02、35.5秒）で確定し、docs/notes/page-announcement.md「進め方」の手順5（制作のスクリプトと文書を cloudflare へマージ）を行う。
決定（2026-11-05〜06、平野さん）

* 告知動画は第2版（CHAT-1105-LGV-02、35.5秒）で確定（2026-11-05）
* 場面7は現在地の結果で止め、説明文までは送らない。テロップはそのまま（画面の下の件数は、それまでに何度も映っているため）。場面6はタグを外す手順を映す（2026-11-05）
* 投稿者は平野良栄・谷岡育夫の2人とも映す。谷岡育夫の許可は平野さんが取った（2026-11-05）
* 茗荷谷は映さない。場面5は「支那麺 はしご」（銀座）を「谷岡育夫」で絞る。現在地は新宿駅。曲は曲調 b（2026-11-05）
* X の投稿は 2026-11-06 18:00 に予約済み（平野さん）。投稿文は次のとおり（入力したとおり。チャット側の案から平野さんが直した）:


```
「ログ」をリニューアルしました。
https://ryoei.pro/log/

・店名・メニュー・駅・投稿者で検索
・「現在地から探す」ですぐに見つかる
・飲食店1700店以上、X投稿2400件以上
・新着・投稿回数の多い店をひと目で

```

* work/1105-lgv を cloudflare へマージしてよい（2026-11-06）

前提（チャット側。平野さんの決定ではない）

* 文書に足すもの（近い実例の書き方に合わせる。文は実物に合わせて変えてよい）:
   * docs/notes/page-announcement.md: 「投稿文の型」に上の投稿文を実例として足す（投稿の URL は投稿の後に分かるので、日時は「2026-11-06 18:00 に予約」と書く）。「実例」の表に「ログ log/（2026-11-06）」の行（35.5秒・白地・操作デモの流れ、駅「銀座」→ 店名「支那麺 はしご」→ 投稿者「谷岡育夫」→ タグを外して現在地〈新宿駅に固定〉、茗荷谷を映さない理由は書かず「映さない駅を決めた」とだけ書く、`scripts/promo_video/log/`）
   * docs/notes/log-page.md: 「告知動画」の節（制作のスクリプトの場所と作り直しの手順は README.md を指す。止まる確かめ〈映さない駅・読めない写真・タグの外れ〉）
* 告知の期間は投稿の完了（2026-11-06 18:00）まで続く。この指示の変更は告知で挙げる機能を変えないので、期間中でもマージしてよい

手順

1. CHAT-1105-LGV-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1106-LGV-04` を足す。上の文書を足し、`scripts/promo_video/log/README.md` が第2版の作り方（秒・場面）と合っているか確かめる
2. 差分（`git diff --stat origin/cloudflare...HEAD`）が下の範囲に収まることを確かめ、ファイルの一覧をログに書く。止まる条件に当たらなければ cloudflare へマージする（CLAUDE.md「ブランチ運用」）
3. マージの後、check-run と、push の検知で走る自動処理（再生成など）の結果を書く（log/ の生成物が変わったら、変わったファイルと理由を書く）

止まる条件

* 差分が `scripts/promo_video/log/`・`docs/notes/page-announcement.md`・`docs/notes/log-page.md`・`docs/decisions/`・`docs/logs/` の外に及ぶ（origin/cloudflare の取り込みによる差は除く）。とくに `assets/log.js`・`scripts/generate_log_pages.py`・`scripts/lib/`・`log/`・`style.css` に差分があれば止まる
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧。work/1105-lgv-amp を含む）が同じ文書の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗は原因を書いて進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1106-LGV-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1106-LGV-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1105-lgv
- ログ: https://github.com/retroeater/mj/blob/work/1105-lgv/docs/logs/CHAT-1106-LGV-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lgv
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 201acc32）: https://github.com/retroeater/mj-logs/tree/main/guide/201acc32

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/201acc32/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/201acc32/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/201acc32/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/201acc32/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/201acc32/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/201acc32/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/201acc32.md
