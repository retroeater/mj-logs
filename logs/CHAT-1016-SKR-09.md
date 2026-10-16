# CHAT-1016-SKR-09

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-skr-build
- 着手時HEAD: 271498b2a

## 指示

【Claude作成】Claude Code 向け指示：本番のビルドが順番待ちで飛ばされる挙動と、実行中の check-run を本番・プレビューで見分けられないことを docs/notes/cloudflare.md に足す
Chat-Ref: CHAT-1016-SKR-09
マージ: ドキュメントのみ（docs/notes/ と docs/decisions/ とログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-skr-build の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1016-skr-build を使う。リモートに無ければ origin/cloudflare から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-10-16、最強戦の公開（CHAT-1016-SKR-06）で cloudflare へマージした後、本番が約1時間反映されず「止まった」ように見えた。原因は Workers Builds の順番待ちだった。次に同じことが起きたとき、壊れたのか待っているだけなのかをすぐ判断できるよう、`docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」に書き足す。

### 決定（2026-10-16、平野さん）
- 下の2点を `docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」に足す（CHAT-1016-SKR-08 の「判断が必要なこと」への回答）

### 前提（チャット側。平野さんの決定ではない）
- 足す事実（出典は CHAT-1016-SKR-06・SKR-08 のログと、2026-10-16 10:26 JST の平野さんの Build history のスクリーンショット〈チャットで受け取った。ここからは検証できない申告値〉）:
  1. 多くの作業ブランチが同時に push すると、プレビューのビルドが順番待ちになる。本番（cloudflare）のビルドが順番を待つ間に cloudflare へ新しい push が来ると、古い本番のビルドは飛ばされ（Build history の印は ⏩）、新しいものだけが残って走る。2026-10-16 は 09:19 JST の 1447edda1 の後、本番のビルドが続けて飛ばされ、10:33 JST の 5bf5d9b0f で反映された。待てば反映されるので「Retry build」は要らない
  2. 同じコミットには、作業ブランチのプレビューのビルドの check-run「Workers Builds: mj」も付く（fast-forward で cloudflare に入ったコミットなど）。本番のものかは、完了した check-run の出力（`output.summary`）に「Preview Alias URL」が無いことで見分ける。実行中（`in_progress`）の check-run の出力にはプレビューの URL が出ないので、完了するまで見分けられない（CHAT-1016-SKR-08 の報告）
- 書き方: 規則と理由の一句だけ。事例（日時・SHA）は1行にまとめるか、`docs/notes/handover-archive-2026.md` の該当の節へ（cloudflare.md の今の書き方に合わせる）
- 知見の層の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 本番のビルドは順番待ちの間に新しい push が来ると飛ばされ、待てば最新が走る | ガイド文（docs/notes/cloudflare.md） | Cloudflare 側の挙動で、テスト・hook・ワークフローでは確かめられない |
| check-run「Workers Builds: mj」の本番・プレビューの見分けは、完了後の出力の「Preview Alias URL」の有無で行う | ガイド文（docs/notes/cloudflare.md） | 確かめる手段はあるが、毎回の手順に入れる仕組み（センサー）は今は無い。センサーにするかは別に検討する（この指示では作らない） |

## 手順
1. 同じ論点の issue（「本番のビルド」「飛ばされ」「Queued」「Workers Builds」）を、クローズ済みも含めて検索し、同じ論点の open issue があれば止まって報告する。`docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」と、「チャット側が成否を確かめるとき」の今の内容を読む。`git branch -r --no-merged origin/cloudflare` の各ブランチがこの節を変えていないか確かめる
2. 上の2点を書き足す（同じ趣旨の記述があれば置き換え・拡張）。決定を docs/decisions/cloudflare.md に足す
3. ログを仕上げ、cloudflare へ入れる

## 止まる条件
- 同じ論点の open issue がある
- 追記先の今の内容が上の事実と矛盾し、どちらが正か判断が要る
- 未マージの work/ ブランチが同じ節を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く。両立する衝突〈追記どうし〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- `git log --all --grep=CHAT-1016-SKR-09` は0件。作業ブランチはローカル・リモートとも無し → `git checkout -b work/1016-skr-build origin/cloudflare`（271498b2a）
- このセッションは SKR-05〜08 と同じセッション

### 手順1

- 同じ論点の issue: GitHub MCP の `search_issues`（「Workers Builds 本番のビルド 飛ばされ Queued 順番待ち」、クローズ済みを含む）で #561（本番の Workers Builds の失敗を知らせる）・#334（check-run 成功を本番反映の合図にしてよいか）・#363（プレビューを確認用 URL に使う運用）。3件とも closed で、open の同じ論点の issue は無い
- 追記先の今の内容: 「チャット側が成否を確かめるとき」は「1つのコミットに Worker ごとと本番・プレビューの別で複数付く」「失敗を伝える前に本番かプレビューかを書く（見分け方は『ビルドが Cloning で失敗したとき』）」。その節に「本番かプレビューかは `.output.summary` に `Preview URL` があるかで見分ける（成功した本番には `Version ID` だけ）」がある。成否の項目には「check-run が success になった時点で本番は反映済み」。**飛ばされる挙動と、実行中の check-run を見分けられないことは書かれていない。矛盾は無い**（「success＝反映済み」が本番の check-run に限ることを書き添えた）
- 未マージの work/ ブランチ: `docs/notes/cloudflare.md`・`docs/decisions/cloudflare.md` を変えているものは無い

### 手順2（f16797478）

- `docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」の成否の項目（「check-run が success になった時点で本番は反映済み」）の直後に2項目を足した:
  - 同じコミットにプレビューのビルドの check-run も付く。本番かは完了した check-run の `output.summary` に `Preview URL`・`Preview Alias URL` が無いことで見分ける（「success＝反映済み」は本番の check-run に限る）。実行中は summary にプレビューの URL が出ないので見分けられない
  - 本番のビルドが順番を待つ間に新しい push が来ると古い本番のビルドは飛ばされ（⏩）、最新だけが走る。待てば反映されるので Retry build は要らない。事例は1行（2026-10-16、1447edda1 の後 → 5bf5d9b0f。⏩ は平野さんの申告値と明記）
- `docs/decisions/cloudflare.md` に 2026-10-16（CHAT-1016-SKR-09）の節
- 事例は1行にまとめたので `docs/notes/handover-archive-2026.md` には移していない

## 報告

- 状態: 完了
- ブランチ: work/1016-skr-build
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-SKR-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-skr-build
- 確認用URL: なし
- マージ: 済（ドキュメントのみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b77586e6）: https://github.com/retroeater/mj-logs/tree/main/guide/b77586e6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
