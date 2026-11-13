# CHAT-1113-SEO-27

- 着手日時: 2026-11-13
- 対象issue: #458, #565, #680
- ブランチ: work/1113-seo27
- 着手時HEAD: 0778da8633

## 指示

【Claude作成】Claude Code 向け指示：SEO 系列（SEO-17〜26）の振り返りの知見を、手順で確かめられる形で資料に書き残す（申送り）
Chat-Ref: CHAT-1113-SEO-27
マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/decisions・docs/notes/chat-routines.md・docs/notes/chat-side-operations.md・docs/notes/handover-archive-2026.md だけ。ページ・コード・ワークフローは直さない）
貼る時機: いつでも（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1113-seo27〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-seo27 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-seo27 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-seo27 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
SEO 系列の 2026-11-12〜13（CHAT-1112-SEO-17〜CHAT-1201-SEO-26 の指示）の振り返りと、そのレビュー（Fable 5.2）で出た知見のうち、手順で確かめられるものだけを資料に書き残す。抽象的な心得は書かない。

### 決定（2026-11-13、平野さん）
- 振り返りとレビューの後、申送りを行う（「申し送り」）。知見の中身と層の選び方は下の表（チャット側の案）で、平野さんが個々に決めたものではない

### 知見 → 選んだ層 → 理由（チャット側の案）

| # | 知見（起きたこと） | 選んだ層 | 理由 |
|---|---|---|---|
| 1 | URL 検査 API の1日の上限の区切りを、SEO-19 の報告の「見込み」（日本時間17時）のまま SEO-20・21 の前提に写した。実際は直近24時間の幅で、429 で実行が2回止まり、指示が1本（SEO-23）増えた | ガイド文（docs/notes/chat-routines.md「指示の種類ごとの確かめ」に1項目） | 外の API の上限の数え方は、テストやワークフローでは確かめられない（公式の文書か実測だけ）。受け手の手順にしても、前提を書くのはチャット側 |
| 2 | 外の API を約2,400回呼ぶスクリプトを作らせる最初の指示（SEO-19）に、応答の時間切れ・5xx の再試行を書かなかった（続きから取る入口は書いた）。1,686件で止まり、指示が1本（SEO-21）増えた | ガイド文（#1 と同じ項目にまとめる） | スクリプトごとのテストは今回 SEO-21 で足した（計算的センサーは個別には有る）。次に別の API で同じ種類のスクリプトを作らせるときに効くのは、最初の指示の前提の書き方 |
| 3 | 月次の対象の選び方（順位1〜4）を、手元にあった件数（未登録 1,516・上限 1,900）を当てはめずに案にした。登録済みの見直しが月15件しか回らないことを Code のレビューが見つけ、指示が1本（SEO-25）増えた。同じとき、平野さんへの問いに Actions の使用量（毎月約200分、#298）を添えなかった | ガイド文（docs/notes/chat-routines.md「指示の種類ごとの確かめ」に1項目） | 案を出すのはチャット側で、機械的に止められない。`--plan` の内訳（計算的センサー）は SEO-24 で作ったので、「案を出す前に件数を当てはめる・定期の実行を足す案には1回の分数を問いに添える」を手順として書ける |
| 4 | ワークフロー（fetch-gsc.yml）の入力を変える指示（SEO-24）で、差分に入れてよいファイルに docs/notes/github-actions.md を入れなかった。説明が実物と合わなくなることをレビューが見つけたが範囲の外で直せず、次の指示に回った | 推論的センサーの候補を #565 にコメント＋ガイド文（#3 と同じ文書に1項目） | 文書と実物の食い違いは #565（文書参照の検査テスト）で拾える可能性がある。拾えるかは #565 の本文しだいなので、事例としてコメントするだけにする（実装しない）。当面はチャット側が範囲に入れる |
| 5 | マージの可否を単独で聞かず、「異論がなければ推奨どおり」の一覧に入れた項目（「マージと一緒に直す」）から承認を読み取った（SEO-25）。docs/notes/chat-side-operations.md「マージの可否は、ほかの問いと分けて単独で聞く。別の問いの答えから承認を推さない」に反する | ガイド文の書き方の直し（同じ行の中の置き換え。純増させない） | 規則はあるのに守られなかったので、内容ではなく書き方を疑う（docs/notes/chat-routines.md「申送り」の観点）。「異論がなければ」の形で添えた項目も承認にならない、と同じ行に入る形で書ければ足す。純増するなら足さず、docs/decisions に事実だけ書く |
| 6 | 推測（title/ のリンクは JS が描いている）を、生成物を1ページ見ずに指示の目的に書いた（SEO-22）。外れた | 足さない | docs/notes/chat-routines.md「どの種類の指示でも、前提にデータ・コード…を書くとき: 該当の行を開き、場所を添える」が既にある。守られなかった事実だけを docs/decisions に書く |

### 前提（チャット側。平野さんの決定ではない）
- 追記先: docs/notes/chat-routines.md は容量の上限が無い（同文書の冒頭）。docs/notes/chat-side-operations.md は上限あり（冒頭の決まり: 既存項目への統合・置き換え、純増したら同じ量を移す）。どちらも、足す前後のバイト数を測ってログに書く
- 書き方: 規則と理由の一句だけ。事例（上の表の「起きたこと」）は docs/notes/handover-archive-2026.md の該当の節へ（節が無ければ、既存の節の並びに合わせて足す）
- 文案（チャット側の案。既存の項目と重なるなら統合してよい）:
  - #1・#2: 「外の API を多数回呼ぶ仕組みを作らせる・動かす指示: 上限（件数と、日の区切りの数え方）は公式の文書か実測の場所を前提の行に添え、報告の『見込み』を事実として写さない。最初の指示の前提に、再試行（時間切れ・5xx）・続きから取る入口・上限のエラーで止まる形を書く（区切りの思い込みと再試行の抜けで、止まるたびに指示が1本増える）」
  - #3: 「枠・上限・優先順位を決める案: 平野さんに出す前に、手元の件数（あれば `--plan` などの内訳）を当てはめて結果の数を添える。定期の実行を足す・延ばす案は、1回の分数（Actions の使用量、#298）を問いに添える」
  - #4: 「ワークフローの入力・起動の形を変える指示: 差分に入れてよいファイルに、そのワークフローを説明する文書（docs/notes/github-actions.md の該当の項など）を入れる」
- 記録: docs/decisions/operations.md に `## 2026-11-13（CHAT-1113-SEO-27）` の節を、過去の申送りの節（例 `## 2026-11-01（CHAT-1101-SEO-15）`）と同じ書き方で足す（足したもの・足さなかったものと理由）
- あわせて: CHAT-1113-SEO-22 のログの `## 報告` の状態は「判断待ち / 続き: CHAT-1113-SEO-23」のまま。判断待ちの中身（直し方の案・月1回の定期取得）は CHAT-1113-SEO-24・25 で決定として記録済みなので、CLAUDE.md「作業ログ」節の状態の書き方に従って、答えが出たことが分かる形に直す（書き方の決まりが無ければ、末尾に ` / 続き: CHAT-1113-SEO-24` を足す）。#680 の男女の入口の相互リンク（未決）は、CHAT-1201-SEO-26 の報告で扱う、と書き添えてよい

## 手順
1. docs/notes/chat-routines.md「申送り」「指示の種類ごとの確かめ」、docs/notes/chat-side-operations.md の冒頭とマージの可否の行、CLAUDE.md「Chat-Ref」「CLAUDE.md / handover.md の更新ルール」、#565 の本文を読む。上の表の各知見について、既に同じ規則が無いかを検索し（あれば統合・置き換え）、足す前のバイト数を測る
2. 上の前提のとおり足し、事例を archive へ書き、docs/decisions/operations.md に節を足す。#565 に #4 の事例を1件コメントする（本文・状態・ラベルは変えない）。SEO-22 のログの状態を直す。足した後のバイト数を測る
3. ログを仕上げて `git push origin work/1113-seo27:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。`## 報告` に、知見ごとの「足した場所／足さなかった理由」を表で書く

## 止まる条件
- 作業ブランチに、冒頭の「マージ:」の行に挙げたファイル以外の変更がある
- docs/notes/chat-side-operations.md が足した後に純増し、同じ量を移せない（#5 を足さずに進め、報告に書く。止まらない）
- 上の表と食い違う既存の規則・決定が見つかった（その知見は足さずに、食い違いを報告に書いて進める。2つ以上あれば止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SEO-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SEO-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。同じ Chat-Ref のコミット0件。SEO 系列の続きで識別子の重複確認は対象外。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は全部ある
- ガイド文に規則を足す指示で、「知見 → 選んだ層 → 理由」の表がある（CLAUDE.md「Chat-Ref」）
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1113-seo27 origin/cloudflare`（0778da8633）
- 指示は「CHAT-1201-SEO-26」を挙げるが、全ブランチにこの Chat-Ref のコミットは無い（12/1 に出す予定の指示の番号と読んだ）

### 1. 読んだもの・既存の規則の検索

- docs/notes/chat-routines.md「申送り」「指示の種類ごとの確かめ」、docs/notes/chat-side-operations.md の冒頭（上限あり・純増したら同じ量を移す）と「平野さんの判断とマージの許可」の 100〜101 行目、#565 の本文
- 既存の規則の検索（`grep -E '見込み.{0,20}(事実|写さ)|再試行|日の区切り|呼ぶ仕組み|件数を当てはめ|分数|使用量|説明する文書|差分に入れてよい|範囲に.*文書'` を chat-routines.md・chat-side-operations.md・instruction-template.md に）: 一致なし。近いのは chat-routines.md「外部サービスの設定を頼むとき」の「外部サービスの設定値（割り当て・上限・単価）は指示文に値を写さず節を指す」で、自分たちの設定値の話のため別の項目にした（食い違いではない）
- #6 の既存の規則: chat-routines.md「指示の種類ごとの確かめ」の最初の項目「前提にデータ・コード・過去の指示の経緯を書くとき: 該当の行を開き、その場所を前提の行に添える」を確かめた
- #565 の検査の案は、参照（`docs/notes/<file>.md「見出し」`）の見出しが実在するかを見るもので、#4 の「説明の中身と実物の食い違い」はそのままでは拾えない。コメントにそう書いた
- 表と食い違う既存の規則・決定: なし

### 2. 足したもの・バイト数

| 文書 | 前 | 後 | 差 |
|---|---:|---:|---:|
| docs/notes/chat-routines.md（上限なし） | 23,912 | 25,095 | +1,183 |
| docs/notes/chat-side-operations.md（上限 28KB・警告域 26KB） | 22,469 | 22,466 | −3 |
| docs/notes/handover-archive-2026.md（上限なし） | 125,072 | 127,067 | +1,995 |

- chat-routines.md「指示の種類ごとの確かめ」の「外のサイト・API に触れるワークフローの手動実行…」の次に3項目（#1・#2 をまとめた項目、#3、#4）。文案はチャット側の案のまま、#3・#4 に理由の一句を足した
- chat-side-operations.md 101行目（#5）: 「**マージの可否は、ほかの問いと分けて単独で聞く。別の問いの答え（「進む」など）から承認を推さない**（推すと、承認されていない変更までマージされる）。」（222バイト）→「**マージの可否は単独で聞く。別の問いの答え（「進む」・「異論がなければ推奨どおり」）から承認を推さない**（承認されていない変更までマージされる）。」（219バイト）。同じ行の中の置き換えで、純増なし
- archive: 「## docs/notes/chat-side-operations.md から」の節の後、「## docs/notes/cloudflare.md・chrome-reading.md から」の前に、新しい節「docs/notes/chat-routines.md・chat-side-operations.md の規則の事例（2026-11-13 の申送り、CHAT-1113-SEO-27）」を足し、#1〜#5 の事例を書いた（既存の「## docs/notes/<文書> から」の並びに合わせた）
- docs/decisions/operations.md に「## 2026-11-13（CHAT-1113-SEO-27）」の節（CHAT-1101-SEO-15 と同じ書き方）
- #565 に #4 の事例をコメントした
- SEO-22 のログの状態を「判断待ち / 続き: CHAT-1113-SEO-23 / 続き: CHAT-1113-SEO-24」にした。続きの書き方は「状態の末尾に ` / 続き: CHAT-…`」（docs/notes/branch-operations.md「作業ログの寿命」）で、2つ目の続きの決まりは無いので、指示どおり末尾に足した。なお `scripts/cleanup_logs.py` は最初の「続き」（SEO-23、完了）で読むので、SEO-22 は SEO-23 の完了の時点で片付けの対象になっていて、この直しで判定は変わらない。#680 の男女の入口の相互リンク（未決）は、12/1 の SEO-26 の報告で扱う


### 3. マージ（止まった）

- push 直前に `git fetch origin cloudflare` → `git merge-base --is-ancestor origin/cloudflare HEAD` が偽（cloudflare が進んでいた。同時に送った `git push origin work/1113-seo27:cloudflare` は non-fast-forward で拒否。権限の拒否ではない）
- `git merge origin/cloudflare` で3ファイルが衝突した。cloudflare 側は CHAT-1113-GDR（4文書の整理、#492。f45aaef6a1「compress chat-side-operations.md and move situational steps」・85e4fccd33「record text removed in the 2026-11-13 guide cleanup」ほか）
  - `docs/notes/chat-side-operations.md`: マージの可否の行（#5）を両側が変えた。cloudflare 側は「**マージの可否は、ほかの問いと分けて単独で聞く。別の問いの答え（「進む」など）から承認を推さない**（承認されていない変更までマージされる）。」（理由の括弧の「推すと、」を外した）、こちらは「**マージの可否は単独で聞く。別の問いの答え（「進む」・「異論がなければ推奨どおり」）から承認を推さない**（承認されていない変更までマージされる）。」。同じ行の中身を両側が変えた衝突
  - `docs/notes/handover-archive-2026.md`: 同じ位置（「## docs/notes/cloudflare.md・chrome-reading.md から」の前）に、こちらは新しい節「docs/notes/chat-routines.md・chat-side-operations.md の規則の事例…」を、cloudflare 側は「### 2026-11-13 の整理で4文書から外した記述（#492）」を足した。追記どうしで両立する形だが、docs/decisions・docs/logs の外
  - `docs/decisions/operations.md`: 末尾の追記どうし（解いてよい種類）
  - `docs/notes/chat-routines.md` は衝突なし（cloudflare 側は 27,088 バイトに増えていた）
- この指示に衝突の解き方の指定は無く、CLAUDE.md「ブランチ運用」で解いてよいのは docs/decisions・docs/logs の末尾の追記どうしだけなので、`git merge --abort` で取り込みをやめ、マージせずに止まった。作業ブランチは 03f7e5357e（取り込む前）のまま
- cloudflare の chat-side-operations.md は 22,041 バイト（GDR の整理の後）。こちらの #5 の書き方を cloudflare 側の行に当て直すと、「（「進む」など）」→「（「進む」・「異論がなければ推奨どおり」）」で約 +33 バイトの純増になり、同じ量を移す必要がある

## 報告

- 状態: 判断待ち
- ブランチ: work/1113-seo27
- ログ: https://github.com/retroeater/mj/blob/work/1113-seo27/docs/logs/CHAT-1113-SEO-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-seo27
- 確認用URL: なし
- マージ: 未（cloudflare の取り込みで、解いてよい種類の外の衝突が出たため止まった）
- issue: #565（コメントのみ）
- 知見ごとの扱い:

  | # | 足した場所 ／ 足さなかった理由 |
  |---|---|
  | 1・2 | docs/notes/chat-routines.md「指示の種類ごとの確かめ」に1項目（外の API を多数回呼ぶ仕組みの指示: 上限の数え方の出典・見込みを写さない・最初の指示に再試行・続きの入口・上限で止まる形） |
  | 3 | 同じ節に1項目（枠・上限・優先順位の案に件数を当てはめる・定期の実行の案に1回の分数を添える） |
  | 4 | 同じ節に1項目（ワークフローの入力・起動を変える指示の範囲に説明の文書を入れる）＋ #565 に事例をコメント（#565 の案では拾えないことも書いた） |
  | 5 | docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」の同じ行の中で置き換え（222→219バイト、純増なし） |
  | 6 | 足さない（chat-routines.md「指示の種類ごとの確かめ」の最初の項目が既にある）。docs/decisions/operations.md に守られなかった事実だけ書いた |
- 判断が必要なこと:
  - cloudflare の取り込みの衝突の解き方（CHAT-1113-GDR の4文書の整理と重なった）。案: (a) chat-side-operations.md は cloudflare 側の行を採り、#5 の「・「異論がなければ推奨どおり」」だけを当て直す（約 +33 バイトの純増。同じ量をほかの文書へ移すか、純増するなら #5 は足さずに docs/decisions に事実だけ書く〈指示の表の #5 の扱い〉）。archive は両方の節を残し、cloudflare 側の「### 2026-11-13 の整理で4文書から外した記述」を先、こちらの節を後に並べる。docs/decisions/operations.md は両方の節を残す。(b) #5 は今回は足さず、chat-side-operations.md は cloudflare 側のままにする（docs/decisions に事実だけ）。推し: (b)（GDR の整理の直後で、同じ行を続けて変えない方が読み比べが楽）
  - chat-routines.md の3項目（#1〜#4）は衝突しておらず、解き方が決まればそのまま入る
- 未確認の項目: なし
- エラー:
  - `git merge origin/cloudflare` で docs/notes/chat-side-operations.md・docs/notes/handover-archive-2026.md・docs/decisions/operations.md が衝突（`git merge --abort` 済み）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 97603d46）: https://github.com/retroeater/mj-logs/tree/main/guide/97603d46

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/97603d46/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/97603d46/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/97603d46/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/97603d46/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/97603d46/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/97603d46/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0778da86.md
