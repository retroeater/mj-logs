# CHAT-1013-BLD-01

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-bld
- 着手時HEAD: 7320c0714

## 指示

【Claude作成】Claude Code 向け指示：本番の Workers Builds が Cloning で失敗した件の記録と、検知の issue の有無の確認
Chat-Ref: CHAT-1013-BLD-01
マージ: ドキュメントのみ（docs/notes・docs/logs・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1013-bld〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-bld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-10-13 13:15〜14:20 JST に本番（cloudflare）向けの Workers Builds が「Cloning repository...」で 5 回続けて失敗した。次に起きたときに短時間で見分けられるよう、事実と調べ方を docs/notes/cloudflare.md に残す。あわせて、ビルド失敗の検知（通知）を扱う issue が既にあるかを確かめて報告する（起票はしない）。

### 決定（2026-10-13、平野さん）
- なし（この指示は記録と確認だけ）

### 前提（チャット側。平野さんの決定ではない）
- チャット側が確かめた事実（GitHub の check-runs と、Chrome でダッシュボードの API を GET で読んだもの。Code からはダッシュボードに到達できないので、check-runs で確かめられる範囲だけ照合する）:
  - 本番向けの失敗は 5cbc83eb（04:15Z）・9bba7d55（04:26Z）・acb5aa04（04:57Z と、Retry の 05:12Z）・023175b4（05:09Z）の 5 回。いずれも Initializing は成功し、Cloning で「Failed: error occurred while fetching repository」。失敗までの時間は 154〜466 秒でばらばら（決まった上限で切れてはいない）
  - 86979e3d（05:20Z）で本番向けの mj は成功（Cloning 6 秒、Version 88bdd84b）。同じコミットの check-run には mj・mj-ask・mj-scheduler の 3 本が付き、failure は mj-scheduler（Cloning で 11 分半）
  - 同じ 86979e3d の work/1012-dsn 向けプレビューは 23 秒で成功。失敗は cloudflare ブランチ向け（mj・mj-scheduler）に限られ、同じコミットのプレビューは通っている。原因は不明。リポジトリの大きさや設定の変更では説明できない（本番トリガーの設定は失敗の前後で同じ）
  - 10/9〜10/13 の mj の約 400 回のうち本番の失敗はこの 5 回だけ。成功した本番ビルドは 1 回 19〜102 秒、大半は 20〜40 秒
  - 10/12 の f09571e4 の本番ビルドも約 30 分かけて失敗した、という申告がある（WHS チャット。チャット側は未確認）
- 見分け方（文書に書く内容の案）:
  - 1 つのコミットに Workers Builds の check-run が複数付く（mj・mj-ask・mj-scheduler）。本番かプレビューかは `.output.summary` に「Preview URL」があるかで見分ける（本番には無い）
  - check-run の `started_at` と `completed_at` は同じ時刻（完了時に書かれる）なので、所要時間は読めない。所要時間とログはダッシュボードの Build history（平野さん）か、チャット側が Chrome でダッシュボードの API を読む（`accounts/{id}/builds/builds/<Build ID>` と同 `/logs`。Build ID は check-run の `.output.summary` にある）
  - 対処: 設定は変えない。次の cloudflare への push か「Retry build」で通る。通るまで本番は前の版のまま（失敗で壊れはしない）
- 文書の置き場所の案: docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」の末尾に小見出しを 1 つ足す。「Chrome からダッシュボードの API を読む」の表に builds のパスを 1 行足す。同じ趣旨の記述（「check-run が queued のまま・見当たらない場合」、「ビルド時間の見積もり」の最後の Cloning の事例）があれば置き換え・拡張してよい

## 手順
1. `gh issue list --state all --search "<語>"` で「Workers Builds」「ビルド 失敗」「通知」「デプロイ 失敗」を検索し、ビルドの失敗を検知・通知する論点の issue（クローズ済みを含む）があれば番号と状態を報告に書く。あっても止まらない（起票はしない）
2. 前提の check-runs の事実を `gh api repos/retroeater/mj/commits/<sha>/check-runs?filter=all` で照合する（5 回の失敗、86979e3d の 3 本と「Preview URL」の有無）。食い違えば止まる。ダッシュボード由来の値（所要時間・Cloning の秒数・Version ID・10/12 の申告）は「チャット側の読み」「申告値」と明記して書く
3. docs/notes/cloudflare.md に前提の内容を足す（文書の上限は無いが、既存の記述の置き換え・拡張を優先し、同じ内容を 2 箇所に書かない）。ログに差分の要点を書く

## 止まる条件
- 手順 2 の照合が食い違う
- 未マージの work/ ブランチが docs/notes/cloudflare.md の同じ節を変えている、または取り込みで衝突する（別の節の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-BLD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-BLD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認（unshallow 後）: `CHAT-1013-BLD` のコミットなし。`BLD` は他のセッション・`docs/logs/` の履歴で未使用。`work/1013-bld` はローカル・リモートとも無く、`git checkout -b work/1013-bld origin/cloudflare` で作成
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順は揃っている（作業ブランチは共通手順の行の中に書かれている）
- 0章: 「指示」欄の末尾は指示文の最後の行と一致
- 重なり: 未マージの `work/` ブランチで docs/notes/cloudflare.md を変えているものは無い

### 手順1: 検知・通知の issue

`gh` が無く、REST の search は「repository-scoped endpoints のみ」で拒否、MCP の `search_issues` は日本語の語でほぼ当たらなかった。
代わりに `repos/retroeater/mj/issues?state=all` の全 558 件（PR を除く）の題と本文を正規表現（Workers Builds／ビルド…失敗・通知・検知／デプロイ…失敗／Notifications など）で調べた。

- **Workers Builds のビルド失敗を検知・通知する論点の issue は無い**
- 近いもの:
  - #118（closed）Cloudflare の通知（Notifications）を設定する。対象はドメイン・SSL・Security Events・Workers のエラー率で、ビルドの失敗は含まない。結果は Universal SSL Alert のみ（docs/notes/cloudflare.md「DNS・メール・通知の設定」）
  - #504（open）予約実行の起動。「起動の失敗や遅れを検知」は Actions の予約実行の検知で、Workers Builds は対象外
  - #291（open）check-run の判定（ビルド成功 / 失敗 / 判定不能）をスクリプト化する。検証の手順で、通知ではない
  - #170（open・保留）本番反映にゲートを設けるか
  - #334（closed）・#363（closed）は check-run の読み方・プレビューの運用で、通知ではない

### 手順2: check-runs の照合

`curl` で `repos/retroeater/mj/commits/<sha>/check-runs?filter=all` を引いた（`gh` の代わり、docs/notes/cloud-sessions.md）。

- 本番向け `mj` の failure は 5cbc83eb・9bba7d55・acb5aa04（2回）・023175b4 の5回。前提と一致（本番の check-run には `Preview URL` が無い）
- 86979e3d: `mj`（本番 success・`Version ID: 88bdd84b…`、`Preview URL` 無し／プレビュー success・`Preview URL` あり）・`mj-ask`（success）・`mj-scheduler`（failure）の3つの Worker。前提と一致
- 5cbc83eb・9bba7d55 も同じコミットのプレビューは success。023175b4 は本番の `mj-ask` が success
- **前提の案との差（照合の食い違いではない）**: 案は「`started_at` と `completed_at` は同じ時刻なので所要時間は読めない」。completed の check-run はそのとおりだが、
  1回のビルドごとに開始時の `in_progress` の check-run（`started_at` = 開始時刻、更新されずに残る）が別にあり、`Build ID` で対にすると所要時間を概算できた。
  失敗の5回は 434・403・467・410・158 秒で、チャット側の読み（154〜466 秒）とほぼ一致。`mj-scheduler` は約704秒（チャット側「11分半」）、本番 `mj` は25秒、プレビューは23秒（同「23秒」）。
  文書にはこの読み方を書いた（案を拡張。案と矛盾はしない）
- 86979e3d の本番 `mj` の開始は 05:34:19Z で、`mj-scheduler` の失敗（05:32:50Z）と `mj-ask` の後。同時1本のビルドで待たされた
- check-run に出ないもの（Cloning の段階・エラー文・Cloning の秒数・約400回の集計・本番トリガーの設定・10/12 の申告）は文書に「チャット側の読み」「申告値」と書いた

### 手順3: docs/notes/cloudflare.md の差分（fde92a210）

- 「ビルド成否と本番の確認範囲（check-runs）」の末尾に「#### ビルドが Cloning で失敗したとき（2026-10-13）」を足した: 見分け方（Worker ごとの check-run、`Preview URL` の有無、in_progress と completed の2本・Build ID で対にして所要時間）、check-run に出ないものの読み方、対処（設定は変えない・次の push か Retry・本番は前の版のまま・ビルド失敗の通知は無い）、事例の表と読みの出所
- 「Chrome からダッシュボードの API を読む」の表に `accounts/{id}/builds/builds/<Build ID>`・同 `/logs` の行を足した
- 「ビルド時間の見積もり」の最後の Cloning の事例（2026-09-29 の 668fdc0）は新しい小見出しへ移し、元の位置は参照にした（同じ内容を2箇所に書かない）
- 「check-run が queued のまま・見当たらない場合」はビルドがまとめられた場合の話で趣旨が違うため変えていない
- docs/notes/cloud-sessions.md「ローカル確認の代わりにプレビュー」の「check-run が `in_progress` で残ることがある」は、上の2本の作りで説明がつく可能性がある。今回の範囲外として変えていない

### 決定の記録

指示の「決定」は「なし」、作業中の平野さんの回答も無いため、docs/decisions には足していない。

### マージ

push 直前に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真であることを確かめ、`git push origin work/1013-bld:cloudflare` で fast-forward（7320c0714..813ab9ea6）。差分は docs/notes/cloudflare.md と本ログのみ（docs のみなので Workers Builds は走らない見込み）。

## 報告

- 状態: 完了
- ブランチ: work/1013-bld
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-BLD-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-bld
- 確認用URL: なし
- マージ: 済（813ab9ea6）
- issue: なし（検知・通知の issue は無い。近いものは #118〈closed〉・#504・#291・#170。詳細は経過の「手順1」）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ed93cb73）: https://github.com/retroeater/mj-logs/tree/main/guide/ed93cb73

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7320c071.md
