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

- 着手前の確認:
  - 同じ Chat-Ref のコミット・ログは無い。`RSC` は続き（30）
  - 作業ブランチ: origin に work/1115-rsc が無かったので、origin/cloudflare から作った（`git checkout -b work/1115-rsc origin/cloudflare`、8d9cccb68）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順・作業ブランチ・作業ブランチの片付け・取り込みの衝突）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致
- 未マージの `work/` ブランチ: 直す文書（`docs/decisions/site-review.md`・`houou.md`・`automation.md`）を変えるものは無い

### 1. 検索

`docs/`（`docs/logs/` を除く）・CLAUDE.md・docs/handover.md で「支出上限」「月次クレジット」「月次の付与」「プロモーションクレジット」「$100 へ戻す」「$100 に戻す」「毎月12日」「確認日」を検索した。あわせて docs/notes・CLAUDE.md・handover.md で「クレジット」「12日」も検索した:

| ファイル・行 | 要約 | 扱い |
|---|---|---|
| docs/decisions/site-review.md:281（「## 2026-11-13（CHAT-1113-TYP-01）」の節） | 「組織の月間支出上限を $100 から $200 に上げた（2026-11-17 に $100 へ戻す）」 | 今の事実として読まれるので注記を足した |
| docs/decisions/houou.md:485（「## 2026-10-11（CHAT-1011-AIC-01）」の節、データ・コンシェルジュ） | 「費用の囲い: Console の組織の月間支出上限は $100。…」 | 今の値として読まれうるので注記を足した |
| docs/decisions/operations.md:2219・2227・2238（「## 2026-11-13（CHAT-1113-CRD-01）」の振り返り） | 上限 $100 を見落とした経緯、確認日を動かすかを聞かなかったこと、約束 (j) | 振り返りの記述（その時点の事実）なので変えない |
| docs/decisions/automation.md:749・816（RSC-14・NWS の節） | 失効の近いプロモーションクレジットを使う決定 | その時点の決定なので変えない |
| docs/decisions/operations.md:462 | 「予約実行の…効果の確認日」 | 関係なし |
| docs/notes/live-page-design.md:79、docs/review-followup-instructions.md:44・169・298 | 「最終確認日」「確認日時」など | 関係なし |
| docs/notes/sns-book.md:26・saikyo-page-design.md:696 | X API の「クレジット切れ」 | 関係なし（X API） |
| docs/notes/books-covers.md・books-freeze.md | 楽天の「クレジット表記」 | 関係なし |

- CLAUDE.md・docs/handover.md: 当たりなし
- 「確認日は毎月12日」: docs/ のどこにも書かれていない（チャット側の控えの決定とみられる）
- 前提との食い違い:
  - 前提は「`docs/decisions/houou.md` の CHAT-1113-TYP-02 の節」と書いていた。実物の houou.md:485 は「## 2026-10-11（CHAT-1011-AIC-01）」の節で、データ・コンシェルジュ（#542）の費用の囲いの記述だった
  - CHAT-1113-TYP-02 の節は `docs/decisions/site-review.md` にあり、上限の記述は無い
  - site-review.md の TYP-01 の節は前提のとおりだった
  - この指示と矛盾する、より新しい決定（2026-11-15 以降の節）は無い

### 2. 記録

- 書き場所: `docs/decisions/site-review.md` の末尾に「## 2026-11-15（CHAT-1115-RSC-30）」の節を足した。理由は、上限の引き上げの決定（CHAT-1113-TYP-01）が同じファイルにあり、今の記述に一番近いため
- 11/13 の決定の控え（同じ趣旨の記述）は docs/decisions に無い。置き換え・拡張はせず、新しい節に決定3つと平野さんの申告と理由を書いた
- 過去の節の本文は書き換えていない。次の2行の末尾に「（2026-11-15 に置き換え。CHAT-1115-RSC-30 の節）」を足しただけ:
  - site-review.md「- 組織の月間支出上限を $100 から $200 に上げた（2026-11-17 に $100 へ戻す）（2026-11-15 に置き換え。CHAT-1115-RSC-30 の節）」
  - houou.md「- 費用の囲い: Console の組織の月間支出上限は $100。この機能専用のワークスペースを作って上限 $30 を付け、そのワークスペースのキーを使う（2026-11-15 に置き換え。CHAT-1115-RSC-30 の節）」

### 3. docs/notes

- docs/notes/ に、今の値としての「上限 $100」「確認日12日」は書かれていない（上の検索）。直していない
- CLAUDE.md・docs/handover.md も当たりなし

### マージ

- ドキュメントのみ（このログ・`docs/decisions/site-review.md`・`docs/decisions/houou.md`）。`git push origin work/1115-rsc:cloudflare` で入れた
  - push 直前に再 fetch して `merge-base --is-ancestor` を確かめた
  - 範囲のコミットの件名・本文に、閉じるキーワードの直後の `#番号` が無いことも確かめた
  - この行を含むコミットが範囲の終わり

## 報告

- 状態: 完了
- ブランチ: work/1115-rsc（cloudflare にマージ済み。ドキュメントのみ）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1115-RSC-30.md
- 比較URL: なし（マージ済み）
- 確認用URL: なし
- マージ: 済み（ドキュメントのみ。範囲は「マージ」の行）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目:
  - billing の画面の値（クレジット2つの残り・付与日・期限、購入クレジット $4.81、自動チャージはオフ）は平野さんの申告。ここからは検証できない
  - 「クレジットを超えて請求されることはない」は見込み（未確認）
  - 前提の「houou.md の TYP-02 の節」は、実物では CHAT-1011-AIC-01 の節だった（経過の「前提との食い違い」）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8b94f7ee）: https://github.com/retroeater/mj-logs/tree/main/guide/8b94f7ee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8d9cccb6.md
