# CHAT-1016-SKR-07

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-skr-sched
- 着手時HEAD: 2536efebb

## 指示

【Claude作成】Claude Code 向け指示：チャット側が後で行う確認に「このチャットに届く予約（send_later）」を使う規則を、チャット側の文書に足す
Chat-Ref: CHAT-1016-SKR-07
マージ: ドキュメントのみ（docs/notes/ と docs/decisions/ とログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1016-SKR-06 の続きとは別のセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-skr-sched の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1016-skr-sched を使う。リモートに無ければ origin/cloudflare から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
チャット側（claude.ai）は、指定した時刻に同じチャットへメッセージを届ける予約（send_later。届いたチャットが作業を再開する）を使える。2026-10-16 に SKR のチャットで、本番のビルドの反映を20分後に確かめるのに初めて使った。チャット側が後で行う確認をこれで行う規則を、チャット側の文書に足す。

### 決定（2026-10-16、平野さん）
- 「明日の XX:XX 以降に確認してください」のような、チャット側が後で行う確認には、このチャットに届く予約（send_later）を使う。規則としてチャット側の文書（docs/notes/chat-side-operations.md から引ける場所）に書く
- 期日の記録の正はカレンダー「ryoei.pro」のまま。予約を入れた確認は、カレンダーの予定の説明に予約があることを書き添える（片方だけになって抜けたり重なったりしないように）

### 前提（チャット側。平野さんの決定ではない）
- 書き場所の案: 規則の本文は `docs/notes/chat-routines.md`「期日とカレンダー」に足す（期日の規則がそこにあり、上限が無い）。`docs/notes/chat-side-operations.md`「場面ごとに読む文書」の表の行「issue の期日・カレンダーの予定」を「issue の期日・カレンダーの予定、後で確かめる予約」のように直して引けるようにする（chat-side-operations.md は上限のある文書で、場面限定の手順は表の文書に置く規則のため）。純増した分の扱いは chat-side-operations.md の冒頭の規則に従う
- 規則の文面の案（規則と理由の一句だけ。整えてよい）:
  - チャット側が自分で確かめられる確認（mj-logs・REST・本番の WebFetch・カレンダー）で、同じチャットの中で数分〜翌日に行うものは、そのチャットに届く予約（send_later）を入れる。返答に予約の時刻を書く。届いたら確かめ、結果に合わせて次の指示文まで用意する（平野さんが「確かめて」と送る手間をなくすため）
  - 翌日以降の確認は、カレンダー「ryoei.pro」にも予定を作り、説明に予約の日時を書く。当日中の確認はカレンダーに作らなくてよい（期日の正はカレンダー）
  - 予約に向かないもの: 平野さんの手作業、数日より先の確認・ほかの系列のチャットが担当する確認（届くのは予約したチャットだけで、文脈が途切れる）、Chrome でしか見られない確認。これらは今までどおりカレンダーで扱う
  - 予約が要らなくなったら取り消す（チャット側のツールの delete_trigger）
- ガイド文に規則を足すので、知見の層の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| チャット側の後での確認は、そのチャットに届く予約で行い、翌日以降はカレンダーにも書く | ガイド文（docs/notes/chat-routines.md） | チャット側のツールの使い方で、テスト・hook・ワークフローでは確かめられない。Code の着手前の確認にも当たらない |

## 手順
1. 同じ論点の issue（「予約」「send_later」「スケジュール」「後で確認」）を、クローズ済みも含めて検索し、同じ論点の open issue があれば止まって報告する。追記先（`docs/notes/chat-routines.md`「期日とカレンダー」と `docs/notes/chat-side-operations.md`「場面ごとに読む文書」）の今の内容を読む。`git branch -r --no-merged origin/cloudflare` の各ブランチがこの2つの節を変えていないか確かめる
2. 上の案で書く。chat-side-operations.md の前後のサイズを測り、ログに書く（上限・警告域との差）。決定を docs/decisions/operations.md に足す
3. ログを仕上げ、cloudflare へ入れる

## 止まる条件
- 同じ論点の open issue がある
- 追記先の今の内容が上の案と矛盾し、どちらが正か判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい）
- chat-side-operations.md が警告域（26KB）を超える
- 未マージの work/ ブランチが同じ節を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く。両立する衝突〈追記どうし〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2536efeb）: https://github.com/retroeater/mj-logs/tree/main/guide/2536efeb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
