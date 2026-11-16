# CHAT-1116-LVP-22

- 着手日時: 2026-11-16
- 対象issue: #362・#590
- ブランチ: work/1105-lvp-auto
- 着手時HEAD: 8bb38d23d

## 指示

【Claude作成】Claude Code 向け指示：/live の掲載の自動（work/1105-lvp-auto）に帝王戦の「ベスト16・決勝」の規則の直しを入れ、条件を満たせば cloudflare へマージする Chat-Ref: CHAT-1116-LVP-22 マージ: 承認済み（チャットで）。条件は下の「決定」と「止まる条件」 貼る時機: CHAT-1116-LVP-21 がマージされた後（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lvp-auto の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-lvp-auto（CHAT-1116-LVP-20 の実装がある）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない。LVP-21 で cloudflare に入った ec29b3b71 の同じ変更との衝突は、cloudflare 側の形にそろえて解いてよい）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1116-LVP-21 がマージされていなければ止まる。

目的
CHAT-1116-LVP-20 で止まった /live の掲載の自動を、平野さんの答えに合わせて直し、本番へ入れる。
決定（2026-11-16、平野さん）

* 帝王戦の「ベスト16・決勝」の1日通しの生配信が「ベスト16」だけのページに入る件は、【2】の規則（`lib/live_extract.py`）を直す: 題名の「ベスト16・決勝」を、まとめ単位「ベスト16、決勝」にする（両方のページに載る。今後の帝王戦も手入力が要らない）
* マージの条件は、一致率ではなく不一致の率で見る（不一致が 2.5% 以下なら通す）。LVP-20 で一致率が下がったのは決めた規則どおりで、過去の149行が N から保留へ移ったため（【3】に値があるので知らせには出ない）
* 帝王戦のページが増えるのは、平野さんが帝王戦の「自動掲載」を Y にした結果として受け入れる。帝王戦のほかにページが増える・減るときは、マージせずに止まる

前提（チャット側。平野さんの決定ではない）

* LVP-20 の報告: 帝王戦で増えるページは10（第2期 `2.html`・`2/b16-a〜d.html`・`2/b16.html`・`2/f.html`、第3期 `3/b16.html`、第5期 `5.html`・`5/b16.html`）。規則を直すと、通しの生配信は `b16.html` と `f.html` の両方に入り、増えるページの組み合わせが変わる見込み（要確認）。第3期の公開版 `bVeavoP15PU` は【3】でまとめ単位が「決勝」に直してある（【3】が正なので、規則の直しでは変えない）
* 「ベスト16・決勝」の形の題名が帝王戦のほかの大会にもあれば、同じ規則が当たる（要確認。当たる行と、変わるページを書く）
* 「自動掲載」の列が欠けた・見出しが重複したときの扱い（LVP-20 は自動の読み取りで止め、毎日の【3】への追記も止まる形）は、追記は続けて自動と知らせだけを止める形のほうが安全と見ている（チャット側の案。変えるなら理由を書く）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。origin/cloudflare を取り込む。上の決定どおり【2】の規則を直し、テストを足す。「自動掲載」の列が欠けたときの扱いを、上の案に直すかを決めて実装する
2. LVP-20 の手順2と同じ形で試す（(a) 不一致の率、(b) 今【3】が空欄の行の Y・N・保留の全行の表、(c) 増える・減るページの URL の全件と生成の警告、(d) 保留の知らせの文面）。規則の直しで【2】のまとめ単位が変わる行（帝王戦のほかも）を全件書く。全ページを再生成し、テストを回す
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、cloudflare で `update-live-channel.yml` を apply なしで1回手動実行して、知らせの段の dry-run の出力を書く。マージ後、本番の `live/teiou/` の増えたページの HTML を確かめる（CLAUDE.md「判断・作業の原則」の確認範囲で書く）。上の「決定」を docs/decisions/live.md に足し、#590 に、帝王戦が自動の範囲に入ったことと増えたページをコメントする

止まる条件

* (a) の不一致の率が 2.5% を超える（不一致の上位の理由を書いて止まる）
* (b) で、「自動掲載」が空の大会（日本シリーズ2つ・紅龍戦など）の行に値が付く
* (c) で、帝王戦のほかにページが増える・減る、または生成の警告が出る（マージせずに止まる）
* 規則の直しで、帝王戦のほかの大会の【2】のまとめ単位が変わり、ページが増減する（マージせずに止まる）
* 【2】【3】の読み込みが失敗する、または読み直すたびに行数が変わる（件数を書いて止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/lib/live_layer3.py`・`scripts/lib/live_candidate.py`・`scripts/lib/live_extract.py`・`scripts/write_live_channel_candidate.py`・`.github/workflows/update-live-channel.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後の手動実行が今回の変更で失敗する（原因を書いて止まる）
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、保留が実際に出たときの issue の作成・コメント・閉じる動きと、コメントの通知メールが届くか（平野さんの GitHub の通知の設定）を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-LVP-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-LVP-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1116-LVP-22` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1116-LVP-21 は cloudflare にマージ済み（2d66bbdac は origin/cloudflare の祖先）。作業ブランチ work/1105-lvp-auto はリモートにある
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-16 11:06 JST

## 報告

- 状態: 作業中
- ブランチ: work/1105-lvp-auto
- ログ: https://github.com/retroeater/mj/blob/work/1105-lvp-auto/docs/logs/CHAT-1116-LVP-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lvp-auto
- 確認用URL: なし
- マージ: 未
- issue: #362・#590
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2cedda30）: https://github.com/retroeater/mj-logs/tree/main/guide/2cedda30

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
