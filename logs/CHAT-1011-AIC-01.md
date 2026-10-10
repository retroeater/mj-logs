# CHAT-1011-AIC-01

- 着手日時: 2026-10-11（JST）
- 対象issue: 未定（起票予定。親 #518）
- ブランチ: work/1011-aic
- 着手時HEAD: a72aaab4

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦の「データ・コンシェルジュ」（AI に言葉で質問できる欄）の実装の前に、起票と技術面の grill（/grill-me）を平野さんと行い、決定を docs/decisions に記録する（コードは変えない） Chat-Ref: CHAT-1011-AIC-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Claude API の月次クレジット（Max 5x、月 $100。Console の組織「Ryoei's Individual Org」に連携済み）を使い、houou/ のトップに、鳳凰戦のデータへ言葉で質問できる欄を置く。この指示では、起票と技術面の grill までを行う。実装は次の指示で行う。
決定（2026-10-10〜11、平野さん。チャットでの grill）

1. 置き場所: 未公開で開発中の houou/ の中で試作する（新サイト #296 送りにしない）。質問欄は houou/ トップのカード4枚の上に常に出す
2. 引いてよいデータ: houou/ の鳳凰戦のデータに加え、title/ と live/ の「鳳凰戦」のデータ。答えはデータにあることだけにし、出典のページへのリンクを付ける。データに無い評価や私生活には答えない
3. 作り方: Claude には「質問を検索条件に直す」ことと「プログラムが返した結果を文章にする」ことだけをさせる。数える・並べるのはプログラムが行う。検索条件に直せない質問には「答えられません」と返す。モデルは Claude Haiku 5.5
4. 上限: 全体で1日200問、同じ IP からは1日10問まで。上限に達したら翌0時（JST）まで受付を止め、その旨を表示する
5. 質問の記録: 質問文と結果（答えた／答えられなかった）だけを残す。IP は回数制限にだけ使い、残さない。記録は90日で消す。週に1回まとめて見られるようにする
6. 費用の囲い: Console の組織の月間支出上限は $100（2026-10-10 に平野さんが設定済み。メール通知は $50・$90）。この機能専用のワークスペースを作って上限 $30 を付け、そのワークスペースのキーを使う
7. 文言（平野さんが指定）:
   * 入力欄の中: 「鳳凰戦について質問してみてください（例：23期生で鳳凰位になった人は？）」
   * 正確さ: 「AI の回答には間違いが含まれている場合があります」
   * 個人情報: 「個人情報等は入力しないでください」
   * 保存: 「ご質問の内容はサービス改善のため利用させていただく場合があります」
8. 進め方: 技術面は Claude Code の /grill-me で平野さんと詰める。実装とマージは次の指示で行う

前提（チャット側。平野さんの決定ではない）

* 費用の見込み（チャット側の概算）: Haiku 5.5 の料金は、100万トークンあたり入力 $0.10・出力 $0.50（2026-10-10 の料金表、platform.claude.com/docs/en/about-claude/pricing）。1問で2回呼ぶと約 $0.002〜0.003 で、1日200問が毎日満杯でも月 $15〜20
* 月次クレジットの対象: API キーでの Claude API（Messages）の呼び出しは対象。ただし、連携した組織のキーに限る（support.claude.com の記事 17154008）。購入済みの残高は $4.81、自動チャージはオフ（2026-10-10 にチャット側が Console で確認）
* 既存のキー: Default ワークスペースの `mj-dojo-guest`（GitHub の Secret `ANTHROPIC_API_KEY`、道場部ゲストの取り込みで使う）。この機能には使い回さない（ワークスペースの上限で費用を囲うため）
* 学習: Anthropic は API の入出力を既定で学習に使わない（privacy.claude.com の記事 7996868）。文言には書かない
* データの範囲（チャット側が 2026-10-11 に origin/cloudflare で数えた。要確認）: `houou/results/*.json` の成績は第17期〜第43期前期で、そこで見える鳳凰位は第24期以降の11名。`houou/search.json` は692名で、入会期（例「40期」）を持つ。例の質問の答えは、吉田直（第36期）と白鳥翔（第42期）の2名。第23期以前の鳳凰位は title/ にある
* 「期」の意味が2つある: 鳳凰戦の「第N期」と、入会期の「N期生」。検索条件の形で区別する必要がある
* houou/ は HOU 系の指示（CHAT-1011-HOU-13 など）が今も直している。この指示はコードを変えないので重ならない。ただし次の実装の指示は、houou/ のトップで重なりうる
* 配信は Cloudflare Workers（Free）の静的アセット（`wrangler.jsonc`）。Worker のスクリプト（`workers/`）の有無と使い方は要確認。チャット側の考え: 外部ドメインを増やさない方針（CSP）があるので、ブラウザからは同じドメインの Worker だけを呼び、Anthropic への呼び出しは Worker から行う
* 起票: 同じ主題の issue が無ければ、鳳凰戦の新ページの親 #518 の下に起票する（親子の付け方は既存の sub-issue に合わせる）

手順

1. 起票: 同じ主題（AI・質問・コンシェルジュ・Claude API）の issue を、クローズ済みも含めて検索する。あれば止まる。無ければ起票し、本文に上の「目的」と「決定」の要約を書き、着手中コメントを残す
2. grill: /grill-me（grilling skill。Cloudflare まわりは cloudflare skill も使う）で、実装に要る技術面を平野さんと1問1答で詰める。各問に推奨案を付ける。実物（コード・`wrangler.jsonc`・データ）で答えが分かることは、聞かずに調べる。少なくとも次を扱う: a. 検索条件の形と、用意する条件の種類。引くデータ（houou/ の成績と索引、title/ の鳳凰戦、live/ の鳳凰戦）、2つの「期」の区別、改名の名寄せ（HOU-13 の決定に合わせる）、答えに添えるデータの時点を含める b. Worker の形。既存の Worker に足すか新しく作るか、Workers Free の範囲で回数制限・1日の上限・質問の記録（D1・KV など）を置けるか、0時（JST）のリセットをどうするか c. キーの置き場所（Worker の Secret）。専用ワークスペースとキーの作成・登録を平野さんが行う手順（Console と Cloudflare ダッシュボードの画面の名前は、公式の文書で確かめて書く） d. 週に1回の質問のまとめをどこに出すか（常設 issue など） e. 試し方（未公開の houou/ での確かめ、費用の実測）と、実装の指示をいくつに分けるか
3. 記録: 上の「決定」と grill の答えを docs/decisions/houou.md に足す（docs/decisions/README.md のとおり）。実装の案（作るファイル、変えるファイル、平野さんの手作業の一覧）をログに書く。起票した issue に、決定の要点と、このログの SHA を固定した permalink をコメントする

止まる条件

* 同じ主題の issue がある。または、その issue に他セッションの着手中コメントがある
* 「前提」の事実と実物が大きく食い違い、決定どおりに作れない（例: Workers Free では回数制限や記録を置けない）。食い違いを書いて止まる（調べ終えた所までログに書く）
* ログと docs/decisions 以外（コード・ワークフロー・設定・シート・生成物）を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（実装の判断が要る点は「判断が必要なこと」に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-AIC-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-AIC-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`--unshallow` の後）: `CHAT-1011-AIC-01` のコミットなし、識別子 `AIC` のコミット・ログなし
- 作業ブランチ: ローカル・リモートとも `work/1011-aic` が無いため `git checkout -b work/1011-aic origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも揃っている
- 0. 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致

## 報告

- 状態: 作業中
- ブランチ: work/1011-aic
- ログ: https://github.com/retroeater/mj/blob/work/1011-aic/docs/logs/CHAT-1011-AIC-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-aic
- 確認用URL: なし
- マージ: 未
- issue: 未定
- 判断が必要なこと: 未定
- 未確認の項目: 未定
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a72aaab4）: https://github.com/retroeater/mj-logs/tree/main/guide/a72aaab4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a72aaab4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a72aaab4.md
