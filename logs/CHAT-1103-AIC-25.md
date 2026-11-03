# CHAT-1103-AIC-25

- 着手日時: 2026-11-03（JST）
- 対象issue: #654
- ブランチ: work/1103-aic
- 着手時HEAD: 454878245

## 指示

【Claude作成】Claude Code 向け指示：質問欄を1つの答え方（横断、`contest: "all"`）にまとめる。大会のページの欄は、ページの大会を `context` として渡し、見た目と今の答えを変えずに移す。cloudflare へマージする Chat-Ref: CHAT-1103-AIC-25 マージ: 承認済み（チャットで。2026-11-03、平野さんがこの指示について単独で答えた。作りの変更のため AIC 系列の例外ではなく個別の承認）。次の3つを1つでも満たさなければマージしない

* テストが通る
* 本番の問いの答えがデータと一致する
* 下の「答えが変わってよい問い」の外で、今答えられている問いの答えと、欄の見た目が変わらない 貼る時機: いつでも（CHAT-1103-AIC-23 のマージ〈22d902d01〉は済んでいる）。ただし下の「止まる条件」の `assets/houou_ask.js` の行に当たるときは止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。22d902d01 が origin/cloudflare の祖先であることを確かめる。AIC-23 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-AIC-25` を足す（AIC-23 に判断の残りは無い）。識別子 `AIC` はこのチャットの系列（AIC-01〜23 で使用。AIC-17 と AIC-24 は送る前に差し替えたため欠番）。作りを変える指示なので、CLAUDE.md「issueの着手ルール」のとおり、同じ目的の issue と未マージの `work/` ブランチを確かめる。0章ゲート（docs/notes/branch-operations.md「Chat-Ref の着手前の確認」）も行う。

目的
質問欄の答え方は今2つある。大会別（`contest` を省略・指定）と横断（`contest: "all"`、AIC-20）。AIC-21〜23 の直しは、両方の指示に入れて両方で確かめる必要があった。サイトのトップ（#101）の作り直しでは、最終的にサイト横断の質問欄を常に出す形に移る。
そこで、大会のページの欄も横断で送り、ページの大会を `context` として渡す形にまとめる。見た目と今の答えは変えない。
AIC-24（予定に答える型）は送る前に差し替えた（欠番）。予定の型は、この指示の後に横断の側だけに足す。
決定（2026-11-03、平野さん）

* 質問欄は、大会ごとに別に保守するのをやめ、1つの答え方（横断）にまとめる。大会のページから呼ばれたときは、ページの大会を文脈（`context`）として渡す。今の見た目を変えずに移す（平野さんの提案）
* 大会のページで、大会名の無い問い（「現タイトルホルダーは？」「歴代優勝者は？」など）は、その大会だけで答える（今の大会別の欄と同じ答え）。ほかの大会は、問いに大会名があるときだけ答える
* 選手名の問いは、記録のある大会を、ページの大会を先にして並べる（AIC-20 の横断の決まり。トップと同じ）
* 順番: まとめる作業（この指示）を先にし、予定に答える型（旧 AIC-24）は後で横断の側だけに足す
* マージは冒頭の「マージ:」の行のとおり（この指示について個別に承認）

前提（チャット側。平野さんの決定ではない）
チャット側が 2026-11-03 18:15 JST ごろ、origin/cloudflare（454878245）で開いて確かめた。着手時に今のコードで開き直す。

* 画面の側
   * 質問欄の JS は `assets/houou_ask.js` の1つ。houou/・ouka/・jwrc/・wrcr/・saikyo/・pro/ の `index.html` が読む
   * 288行: `body: JSON.stringify(root.dataset.contest ? { question: question, contest: root.dataset.contest } : { question: question })`
   * `data-contest` は生成スクリプトが付ける
      * `scripts/generate_houou_pages.py` 427行: 鳳凰戦（`HOUOU`）では付けない。ほかの大会は `CONTEST.dir`
      * `scripts/generate_saikyo_pages.py` 1720行: `data-contest="saikyo"`
   * pro/ にも `data-contest` は無い（今は鳳凰戦の扱い）。pro/ は大会のページではないので、`context` を付けずに横断にするのを第一の案とする。実物で決め、報告に書く（大会名の無い問いの答えが5大会になる。「答えが変わってよい問い」に入れる）
* Worker の側
   * `workers/ask/src/ask.mjs` 733行あたり `crossContests(deps, question, context)`
      * 問いに大会名があれば、その大会
      * 無ければ、拾った名前に記録がある大会（`context` の大会を先に）
      * どちらも無ければ、5大会すべて（`ordered`）
   * 直すこと: `context` があり、問いに大会名も記録のある名前も無いときは、`context` の大会だけを返す（決定の2つ目）。`context` が無いとき（トップ・pro/）は今のまま5大会
   * 答える大会が1つのときの応答（`answer`・`lead`・`notes`・`asof`・`sources`・`players`・選手のカード）と記録の `type` が、今の大会別の欄と同じになるかを確かめる。違えば、大会が1つのときは今の大会別の流れで答える形にする
      * 例: 大会名の見出し（「【鳳凰戦】」など）を付けない、`type` の扱い
      * 記録の `type` を `all:…` にするか、今の大会別の形にするかは実物で決め、報告に書く。週次の書き出しの見出しは変えない
   * 大会別の要求（`contest` を省略・大会の id）は残す（古い JS をキャッシュしている人のため）。消すかは後で決める（報告の「判断が必要なこと」に書く）
   * 案内（`guideAnswer()`、AIC-08・16・18・19）は、大会のページで「ほかの大会にだけ記録がある選手」を聞かれたときの答え。まとめた後は、記録のある大会の答えそのものになる見込み（決定の3つ目）。実物で確かめ、変わる答えを報告に書く
* 画面の JS
   * 288行を、`{ question, contest: 'all', context: <ページの大会 id。鳳凰戦は 'houou'。pro/ は付けない> }` にする
   * 横断の応答で答える大会が2つ以上のときの見せ方（大会ごとの見出し・`players[]`）は、TOP 系列が CHAT-1103-TOP-07 で `assets/houou_ask.js` に入れる見込みだった。この指示で大会のページにも同じ見せ方が要る。TOP-07 がまだマージされていなければ、この指示で入れる（見せ方は ask-worker.md の横断の節の項目の表に合わせる）
* 答えが変わってよい問い
   * 大会のページで選手名を聞いたときの答え。ページの大会に加え、記録のあるほかの大会の答えが並ぶ（今は案内の一文）
   * pro/ で大会名の無い問いを聞いたときの答え（鳳凰戦だけ → 5大会）
   * それ以外（大会のページの、大会名の無い問い・大会名のある問い・期の外・決勝だけの人の答え）は変えない
* Claude の呼び出しの回数
   * 大会が1つのときは今の大会別と同じ（1回目と2回目）
   * 大会のページで選手名を聞いて複数の大会に記録があるときは、横断と同じ（`CROSS_CALLS_MAX` の内）
   * 費用は、横断の約4倍を 2026-11-03 に受け入れている（AIC-20 の決定）。大会のページの問いのうち、選手名で複数の大会になる割合を、記録（ブックの先週の行など、読める範囲）から見積もって報告に書く
* 1日の上限（全体 200 問・同じ接続元 10 問）は今と同じ数え方（1問を1と数える）
* 選手の役職などの人の属性は、ログ・テストに書かない
* 本番の確かめは1日10問まで（同じ接続元から）。2026-11-03 は AIC-22・23 で使っている。上限に当たったら、送れなかった問いを「未確認の項目」に書く
* マージすると、サイト（`mj`）と `mj-ask` のビルドの終わる順が決まらない。新しい JS が古い Worker に当たる間は、大会名の無い問いが5大会で答えられる。短い間なので受け入れ、ビルドの時刻をログに書く

手順

1. 作る
   * Worker: 上の `crossContests()` の直しと、大会が1つのときの応答を今の大会別の形にそろえる
   * 画面の JS: 送る要求を横断に替え、2つ以上の大会の見せ方を入れる
   * docs/notes/ask-worker.md の横断の節と「5大会の応答ルール」を直す（大会のページの欄も横断で送ること、`context` の効き方、大会別の要求は残していること）
   * docs/decisions/houou.md に上の「決定」を足す
   * #654 に、まとめた形をコメントする（TOP 系列が読む）
2. テスト
   * 5大会のページの `context` で、大会名の無い問い（「現タイトルホルダーは？」「歴代優勝者は？」）が、その大会だけの答えになる（今の大会別の答えと同じ。応答の項目を比べる）
   * 大会名のある問い（houou/ で「女流桜花の歴代優勝者は？」）が、名前の大会で答える
   * 選手名（houou/ で「佐々木寿人の成績は？」）が、記録のある大会を鳳凰戦を先にして並べる
   * `context` が無いとき（トップ・pro/）は、AIC-20 の今の横断の答えのまま
   * 大会別の要求（`contest` を省略・指定）の答えが今と同じ
   * 変わる範囲の比較: 今までの比べ方（AIC-21〜23 の scratchpad の比べ方）で、大会別の要求と、`contest: "all"`＋`context` の要求の応答・記録・Claude に渡す結果を、5大会×型×名前で比べ、違いが「答えが変わってよい問い」の内だけであることを確かめる
   * 新しいテストは、直す前のコードで落ちることを確かめる
   * `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
   * 画面: houou/・saikyo/・pro/ を作業ブランチのプレビュー（Workers Builds の別名 URL）か `wrangler dev` で開き、欄の見た目が今と同じことを Playwright の画像で比べる（UA に `mj-internal`）。答えの表示は、プレビューの `Origin` が本番でないため Worker に断られるので、本番の確かめで見る
   * 新しい文脈のサブエージェントにレビューさせる（CLAUDE.md「判断・作業の原則」）
3. マージと本番
   * cloudflare へマージし、サイトと `mj-ask` のビルドの結果を確かめられる範囲でログに書く
   * 本番に送る問いは次の4つ（`Origin: https://ryoei.pro`、新しい JS と同じ要求の形で送る）。答えがデータと一致し、今の大会別の答えと同じか（変わってよい問いは、どう変わったか）を書く
      * houou/ の文脈で「現鳳凰位は？」（鳳凰戦だけ）
      * saikyo/ の文脈で「歴代最強位は？」（最強戦だけ）
      * houou/ の文脈で「佐々木寿人の成績は？」（鳳凰戦を先に、記録のある大会を並べる）
      * ask-worker.md「本番で送る固定の問い」から1問
   * 本番のページ（houou/・saikyo/）の HTML と JS を取得し、新しい JS が配られていることを確かめる（ブラウザでの見え方は確かめられない範囲として書く。CLAUDE.md「判断・作業の原則」）

止まる条件

* テストが通らない、本番の問いの答えがデータと一致しない、「答えが変わってよい問い」の外で今答えられている問いの答えか欄の見た目が変わる（どれか1つでもマージしない）
* 大会が1つのときの応答を今の大会別の形にそろえるのに、`ask.mjs` の今の大会別の流れ（1回目・検索・2回目）の中身を書き換える必要がある（作らずに、どこが違うかを書いて止まる）
* 未マージの `work/` ブランチ（とくに TOP 系列の CHAT-1103-TOP-07）が `assets/houou_ask.js` を変えている（止まって、どちらが入れるかの判断を求める）。`workers/ask/src/` の同じ関数・同じ行を変えているブランチがある、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 同じ目的の issue・未マージの `work/` ブランチがある
* 1問あたりの Claude の呼び出しが、大会が1つの問いで今より増える見込みになる（費用の見込みを書いて止まる）
* #654 に他セッションの着手中コメントがある
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-23 の「未確認の項目」4つは、この指示の「未確認の項目」にも写す
* 「判断が必要なこと」に、大会別の要求（`contest` を省略・大会の id）を消すかと、その時機の案を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-AIC-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-AIC-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0: 指示欄の末尾は指示文の最後の行と一致。22d902d01 は origin/cloudflare の祖先。origin/work/1103-aic はマージ済みで、ローカルの work/1103-aic は origin/cloudflare の祖先だったので `git merge --ff-only origin/cloudflare` で 454878245 へ進めた。AIC-23 のログの状態に「/ 続き: CHAT-1103-AIC-25」を足した。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4つともある
- 0章ゲート・同じ目的のブランチの確かめ（`git branch -r --no-merged origin/cloudflare` の各ブランチの `git diff --name-only origin/cloudflare...<branch>` とコミットの件名）:
  - **`origin/work/1101-prp-pub` が `assets/houou_ask.js` を変えている**（4e410b4c6「feat: link answer cards on pro/ top to player pages」、2026-11-01。答えのカードの行き先を、pro/ のトップでは連盟プロの個別ページにする `playerHref()`・`slugMap()` と `data-player-pages` の読み込み）。このブランチは未マージで、ログの状態は「判断待ち」（CHAT-1103-PRP-07 まで続いている）
  - `origin/work/1103-top`（CHAT-1103-TOP-07、状態「判断待ち」）は `assets/houou_ask.js` を変えていない。トップの横断の質問欄は新しいファイル `assets/top_ask.js`（261行）で作っている。指示の前提「TOP-07 で `assets/houou_ask.js` に入れる見込み」とは違う。大会ごとの見出し・`players[]` の見せ方は `top_ask.js` にある見込み（中身は読んでいない）
  - `workers/ask/src/` を変えている未マージのブランチは無い（work/1029-lgn-page は `workers/ask/data*.json` の生成物だけ）
- 止まる条件「未マージの `work/` ブランチ（とくに TOP 系列の CHAT-1103-TOP-07）が `assets/houou_ask.js` を変えている（止まって、どちらが入れるかの判断を求める）」に当たるので、作る前に止まった。コード・#654 の着手中コメントは触っていない

## 報告

- 状態: 判断待ち / 続き: CHAT-1103-AIC-26
- ブランチ: work/1103-aic
- ログ: https://github.com/retroeater/mj/blob/work/1103-aic/docs/logs/CHAT-1103-AIC-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-aic
- 確認用URL: なし（作る前に止めた）
- マージ: 未（作る前に止めた）
- issue: #654（コメントは止めた旨の1件だけ）
- 判断が必要なこと:
  - 未マージの `work/1101-prp-pub`（PRP 系列、判断待ち）が `assets/houou_ask.js` を変えている（pro/ のトップの答えのカードを個別ページへ送る `playerHref()`・`slugMap()`、4e410b4c6）。この指示も同じファイルの送る要求（288行あたり）と、2つ以上の大会の見せ方を変える。どちらを先に入れるか: (1) PRP のその変更を先に cloudflare へ入れ、この指示はその後に取り込んで作る、(2) この指示を先に入れ、PRP 側が取り込みで合わせる、(3) この指示で PRP の変更を待たずに、送る要求の1行だけを変え、見せ方は別の指示にする → 回答（2026-11-03、平野さん）: Worker の側だけを先に入れ（CHAT-1103-AIC-26）、画面の JS は PRP と TOP-07 のマージの後に別の指示で行う
  - 2つ以上の大会の見せ方は、TOP-07 が `assets/houou_ask.js` ではなく新しい `assets/top_ask.js` に作っている（指示の前提と違う）。大会のページにも同じ見せ方を入れるとき、`top_ask.js` の作りを `houou_ask.js` に写すか、`houou_ask.js` を `top_ask.js` に寄せて1つにするか（TOP-07 も判断待ちで未マージ） → 回答（2026-11-03、平野さん）: 後で `top_ask.js` に寄せて1つにする方向。細部はその指示を書くときに決める
  - 大会別の要求（`contest` を省略・大会の id）を消すかと時機の案（完了条件）: まとめた後も、古い JS をキャッシュしている人のために残す。消す時機は、新しい JS の配信から JS のキャッシュの寿命（assets のキャッシュ設定、`_headers`）を過ぎ、週次の記録で大会別の型（`ouka:…` など `all:` で始まらない型）の行が0になった週の後を案とする（作っていないので、今の判断のための案） → 扱い（2026-11-03、チャット側が案を受けた）: 案のとおり。この指示（AIC-26）では消さない
- 未確認の項目:
  - 2026-11-09（月）03:00 JST の週次の書き出しが動き、ブックに 11/02〜11/08 の行が入るか。あわせて、2026-11-02 10:09 JST ごろの「現女流桜花は？」の「引数」の列に from・to が入っているか（AIC-23 から写した）
  - 地域で断られた問いが、新しい結果（`region`、ブック「地域で使えない」）で記録に入るか（AIC-23 から写した）
  - 横断の記録（型 `all:…`）が週次の書き出しのブックに入るか（2026-11-09 03:00 JST の書き出しで。AIC-23 から写した）
  - 期の外で期を外して答える問い（例「22期の和久津晶成績」）の答えに、冒頭の文と重なる「第N期の記録はありません」が出ないか（週次の記録で見る。AIC-23 から写した）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 45487824）: https://github.com/retroeater/mj-logs/tree/main/guide/45487824

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
