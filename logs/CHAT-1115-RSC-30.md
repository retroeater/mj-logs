# CHAT-1115-RSC-30

- 着手日時: 2026-11-15
- 対象issue: なし（記録のみ）
- ブランチ: work/1115-rsc
- 着手時HEAD: 8d9cccb68

## 指示

【Claude作成】Claude Code 向け指示：API の月次クレジットと支出上限の決定（2026-11-13・11-15）を docs/decisions に記録する
Chat-Ref: CHAT-1115-RSC-30
マージ: ドキュメントのみ（ログ・docs/decisions・docs/notes）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1115-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Claude API のプロモーションクレジット（月次の付与）と Console の組織の月間支出上限について、平野さんがチャットで決めたことを docs/decisions に記録する。今の事実としてもう合わない記述（「2026-11-17 に $100 へ戻す」など）も直す。
決定（2026-11-13、平野さん）

* 月次クレジットが付与されないまま残高が尽きそうなときは、自動チャージを入れず、購入クレジットを手動で $10 だけ買う

決定（2026-11-15、平野さん）

* 組織の月間支出上限は、11/17 には $100 に戻さない。2026-12-17 まで $200 のままにし、12/17 に $100 に戻すか別の額にするかを決め直す（2026-11-13 の「2026-11-17 に $100 へ戻す」を置き換える）
* 月次クレジットの付与を確かめる日を、毎月12日から毎月15日に変える。付与されていなければ翌日へ繰り越す（2026-11-13 の「確認日は毎月12日のまま」を置き換える）

前提（チャット側。平野さんの決定ではない）

* 2026-11-15 朝の billing の画面の平野さんの申告（スクリーンショット。ここからは確かめられない。記録には「平野さんの申告」と書く）:
   * プロモーションクレジットは2つ。1つ目は $109.24 のうち残り $67.64、付与日 2026-10-14、期限 2026-11-17 00:00 UTC。2つ目は $200.00 のうち残り $200.00、付与日 2026-11-14、期限 2026-12-17 00:00 UTC。どちらも適用対象は Agent SDK・API・Batch API・Playground・Managed Agents
   * 購入クレジットは $4.81。自動チャージはオフ
   * 11月分が付与済みなので、2026-11-13 の決定の「$10 を手動で購入」は、11月は要らなかった
* 上限を $200 のままにする理由（チャット側の説明。平野さんはこれを見て選んだ）: 2つ目の $200 を上限で止めずに使えるようにするため。購入クレジットが $4.81 で自動チャージもオフなので、クレジットを超えて請求されることはないという見込み（未確認）
* 確認日を15日にした理由（チャット側の案。平野さんはこれを見て選んだ）: 11月分の付与日が 14日だったので、1日の余裕をとった
* 2026-11-13 の上限の引き上げは `docs/decisions/site-review.md` の「## 2026-11-13（CHAT-1113-TYP-01）」の節に「（2026-11-17 に $100 へ戻す）」とある（チャット側が mj-logs の写しで見た。要確認）。`docs/decisions/houou.md` の CHAT-1113-TYP-02 の節の「Console の組織の月間支出上限は $100」は、そのときの決定の記録（要確認）
* 書き場所は Code が決めてよい（site-review.md・automation.md・houou.md のうち、今の記述に近いところ。選んだ場所と理由をログに書く）
* カレンダー（11/17 の予定の説明の書き直し、12/17 の予定の作成）はチャット側が済ませた。Code は触らない

手順

1. `docs/` と CLAUDE.md・docs/handover.md で「支出上限」「月次クレジット」「プロモーションクレジット」「$100 へ戻す」を検索し、当たった行の一覧をログの `## 経過` に書く（ファイル・行・要約）。前提の2か所の記述が食い違えば、食い違いをログに書いて進めてよい
2. 上の決定3つと、前提のうちの申告（「平野さんの申告」と書いて）を docs/decisions の1つの節「## 2026-11-15（CHAT-1115-RSC-30）」に記録する。同じ趣旨の記述（11/13 の決定の控え）があれば、置き換え・拡張してよい（処理をログに書く）。過去の節の本文は書き換えず、手順1で当たった記述のうち今の事実として読まれる記述（「2026-11-17 に $100 へ戻す」「確認日は毎月12日」など）にだけ、「（2026-11-15 に置き換え。CHAT-1115-RSC-30 の節）」の注記を足す
3. docs/notes/ に今の値として「上限 $100」「確認日12日」が書かれていれば、新しい決定に合わせて直す（直した行をログに引用する）。CLAUDE.md・docs/handover.md に当たれば直さず、ログに書く

止まる条件

* 差分がこのログ・docs/decisions・docs/notes 以外に出る
* 手順1で、月次クレジット・支出上限についてこの指示と矛盾する、より新しい決定（2026-11-15 以降の節）が見つかる
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が、直す文書の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* コミットの件名・本文に、閉じるキーワード（close・fix・resolve とその活用形）の直後の `#番号` を書かない（push の前に確かめる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-RSC-30.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-RSC-30 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1115-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1115-rsc/docs/logs/CHAT-1115-RSC-30.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-rsc
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8d9cccb6）: https://github.com/retroeater/mj-logs/tree/main/guide/8d9cccb6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d9cccb6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d9cccb6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d9cccb6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d9cccb6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d9cccb6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d9cccb6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8d9cccb6.md
