# CHAT-1104-ACF-36

- 着手日時: 2026-11-04
- 対象issue: なし（起票予定）
- ブランチ: work/1104-acf
- 着手時HEAD: 499f5e29a

## 指示

【Claude作成】Claude Code 向け指示：Sheets API の1分あたりの読み取り上限（429）で本番の再生成がページを飛ばした件を issue に起票する Chat-Ref: CHAT-1104-ACF-36 マージ: ドキュメントのみ（ログ・docs/notes・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-11-04 02:20〜03:58 JST に、cloudflare の再生成3本が Sheets API の読み取りの上限（HTTP 429「Read requests per minute per user」）でページを飛ばした（04:20 JST の再生成で回復済み）。2026-10-22 の決定（docs/decisions/security.md の CHAT-1022-PRV-09「予約実行が長引いたときに別の issue を起票する」）の条件を超えたので、原因と対策の候補をまとめた issue を起票する。対策はこの指示では決めない・実装しない。
決定（2026-11-04、平野さん）

* Sheets API の 429 の件を、別の issue として今起票する

前提（チャット側。平野さんの決定ではない）

* 飛ばしたページ・理由・回復の事実は CHAT-1104-MEW-01 のログ（docs/logs/CHAT-1104-MEW-01.md）の「手順2 (A)」で確かめ済み。issue にはその節から引用する（要約し直さない）
   * 41683310617（fetch-youtube.yml、workflow_dispatch）: houou_pages
   * 41689008141（regenerate-page.yml、push）: pro_pages
   * 41693768390（regenerate-page.yml、push）: jpml_pros・saikyo_pages
   * 回復: 41702345684（regenerate-page.yml、push、04:20 JST）で4ページとも作り直し済み
* チャット側が gh api で見た事実（要確認。手順2で確かめる）:
   * 同じ時間帯（02:28〜02:57 JST ごろ）に、作業ブランチの regenerate-page.yml の workflow_dispatch が多数並走していた（work/1104-ttl・work/1029-lgn-page・work/1104-prp-unpub・work/1104-top・work/1103-ryo8 など）。work/ 側でも 429 が出ている（CHAT-1104-TOP-13 のログの run 41687676575 で title_pages）
   * fetch-youtube の 41683310617 の所要時間は 23分52秒（直近の success は 5〜10分）
   * run-daily.yml の所要時間: 11/02 26分34秒 → 11/03 29分22秒 → 11/04 37分41秒（docs/notes/sheets-reader.md の「全ページの生成は約10分、2026-10-21」と比べる）
   * lib/sheets.py は 429 で待って繰り返す（最大7回、64秒まで。docs/notes/static-generation.md）が、上の3本はそれでも失敗した
* 見立て（チャット側。issue には「見立て」と書いて事実と分ける）: Sheets API の上限はサービスアカウント1つ（sheets-reader）単位で全ワークフロー共通のため、作業ブランチの全ページの再生成が並走すると本番の再生成の分まで食う
* 対策の候補（チャット側の案。決定ではないと issue に明記する。並べるだけで推奨は書かない）:
   * (a) シートを読むワークフローに共通の concurrency group を付け、同時に走らせない（作業ブランチは待つ）
   * (b) 1回の生成の中で同じブック・タブの読み取りをまとめる（batchGet・実行内のキャッシュ）
   * (c) 作業ブランチの workflow_dispatch の既定の対象を全ページでなく変更に関わるページに絞る
   * (d) Google Cloud で Sheets API の1分あたりの上限の引き上げを申請する（平野さんの手作業）
* 関連しそうな issue（要確認）: #552（title の fetch_tab() が再試行を通らない）・#456（全ページの再生成の差分）

手順

1. 同じ論点の issue を、クローズ済みとコメントまで含めて `gh api search/issues`（`is:issue`）で探す（語: 「429」「RESOURCE_EXHAUSTED」「Sheets API」「上限」「並走」「concurrency」）。open で同じ主題の issue があれば起票せず止まって報告する。あわせて #552・#456 の題と状態を確かめ、関連として書くかを決める
2. 起票の材料を確かめる（ワークフローは実行しない。読むだけ）:
   * 2026-11-03T17:00Z〜19:00Z の regenerate-page.yml・fetch-youtube.yml の run の一覧（開始・終了・ブランチ・契機・結論）を表で全件。うち 429 が出た run（`RESOURCE_EXHAUSTED` をログで grep）に印を付ける
   * run-daily.yml の直近7回の所要時間
   * シートを読むワークフロー（`SHEETS_READER_SA_KEY` を使うもの、`git grep -n SHEETS_READER_SA_KEY .github/workflows/`）ごとの `concurrency:` の設定の有無と group 名
   * 全ページの生成1回あたりの Sheets API の呼び出し数の見積もり（コードから。ページごとの内訳。測れなければ「未確認」として、測り方の案を書く）
3. issue を起票する。題の案: 「Sheets API の1分あたりの読み取り上限（429）で、並走する再生成がページを飛ばす」。本文は「事象（MEW-01 のログの手順2 (A) から引用＋手順2の表）」「影響（本番のページが回復までの約2時間古いまま）」「見立て」「対策の候補（チャット側の案。決定ではない）」「関連（10/22 の決定・#552・#456 など）」の節にする。ラベルは既存の慣例に合わせ、合うものが無ければ付けずに報告に書く。起票後、docs/notes/sheets-reader.md の「残り」（5.）の「予約実行が長引いたら別の issue を起票する」を、起票した issue の番号を指す形に直し、docs/decisions/security.md に上の「決定」を足す（追記先の今の内容を読んでから）

止まる条件

* 手順1で同じ主題の open の issue が見つかった
* 手順2の事実が「前提」と大きく食い違い、見立て（並走で上限を食う）が成り立たない（例: 429 の run の時間帯にほかの run が走っていない）。そのときは起票せず、事実の表を報告に書いて止まる
* sheets-reader.md・security.md の追記先が「前提」と矛盾し、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張してよい）
* 未マージの work/ ブランチが docs/notes/sheets-reader.md の「残り」の節、または docs/decisions/security.md の末尾を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号を「issue:」の行に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-ACF-36.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-ACF-36 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- Chat-Ref の重複: `git log --all --grep='CHAT-1104-ACF-36'` は0件。`ACF` は既存（CHAT-1013-ACF-07 など）で、連番36は最初の指示ではない
- 0章ゲート: 実装を含まない（ドキュメントと issue の起票のみ）ため対象外
- 指示文の issue の state: 着手対象の issue は無い（#552・#456 は手順1で確認）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行がそろっている
- 0.: このログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 作業ブランチ: ローカル・リモートとも `work/1104-acf` は無かった → `git checkout -b work/1104-acf origin/cloudflare`（499f5e29a）

### 手順1: 同じ論点の issue

`gh api search/issues`（`repo:retroeater/mj is:issue`）で「429」「RESOURCE_EXHAUSTED」「"Sheets API"」「上限」「並走」「concurrency」「"Read requests"」「quota」「クォータ」「sheets-reader」を検索した。同じ主題（Sheets API の読み取りの上限・並走）の issue は open・closed とも無し。

- 近いもの: #263（closed、regenerate-page.yml に concurrency。push の競合が主題）、#472（closed、外部からの読み取りの再試行）、#564（open、Actions の整理）、#298（open、Actions の使用量）。主題が違うので関連にだけ書く
- #552（open）「title の fetch_tab() がフィルタの検知（#432）と再試行を通らずにシートを読む」→ 関連に書く（429 の再試行を通らない読み取りが残っている）
- #456（open）「全ページの再生成の差分を、コードによる変化とデータによる変化に分けて出す」→ 作業ブランチで全ページの再生成を回す理由の一つなので関連に書く

### 手順2: 起票の材料（読むだけ）

concurrency（`git grep -ln SHEETS_READER_SA_KEY .github/workflows/` の17本）:

| ワークフロー | concurrency の group |
|---|---|
| check-image-links.yml | なし |
| check-meibo.yml | なし |
| check-saikyo-unregistered.yml | なし |
| check-sheets-reader.yml | check-sheets-reader |
| convert-x-posts.yml | convert-x-posts |
| fetch-x-posts.yml | fetch-x-posts |
| fetch-youtube.yml | fetch-youtube |
| regenerate-page.yml | job に `regenerate-page-${{ github.ref }}`（queue: max）。ブランチごとの組なので、別のブランチどうしは並走する |
| regenerate-saikyo.yml | regenerate-saikyo |
| research-candidates.yml | research-candidates |
| run-daily.yml | run-daily |
| run-weekly.yml | run-weekly |
| sync-birthday-calendar.yml | sync-birthday-calendar |
| sync-dojo-calendar.yml | sync-dojo-calendar |
| sync-yotei.yml | sync-yotei |
| update-live-channel.yml | update-live-channel |
| update-sns-book.yml | update-sns-book |

ワークフローをまたぐ共通の group は無い。

呼び出し数（コードから）:

- `scripts/regenerate.py` の `run_page()` はページごとに生成スクリプトを別のプロセスで順に動かす。`lib/sheets.py` のキャッシュ（`_book`・`_values`・`check_not_filtered` の `lru_cache`）はプロセスの中だけで効く
- 1プロセスの中では、ブック1冊につき `spreadsheets.get` 1回、タブ1枚につき `values.get` が最大2回（`UNFORMATTED_VALUE` と `FORMATTED_VALUE`。`_select()` が両方読む）
- よって全ページの生成1回 ≒ Σ(ページごとの 読むブック数 ＋ 2×読むタブ数)。ページをまたいで同じタブを読むと、その分だけ繰り返し数えられる
- ページごとの内訳は未確認（タブ名が定数・lib のモジュール経由で決まり、静的に数え切れない。認証情報が無いので手元で動かして数えられない）。測り方の案: `lib/sheets.py` の `_get()` に呼び出しの数を数えてページの終わりに1行出す仕組みを足し、作業ブランチの `regenerate-page.yml`（dry_run・all）で1回動かす。または Google Cloud のコンソールの API の指標（Sheets API の「リクエスト」をメソッド別）で、全ページの生成1回の前後を比べる

## 報告

- 状態: 作業中
- ブランチ: work/1104-acf
- ログ: https://github.com/retroeater/mj/blob/work/1104-acf/docs/logs/CHAT-1104-ACF-36.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-acf
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 95028c1a）: https://github.com/retroeater/mj-logs/tree/main/guide/95028c1a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/95028c1a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
