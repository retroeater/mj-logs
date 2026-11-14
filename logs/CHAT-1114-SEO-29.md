# CHAT-1114-SEO-29

- 着手日時: 2026-11-14
- 対象issue: なし
- ブランチ: work/1114-seo29
- 着手時HEAD: ec1ef47122

## 指示

【Claude作成】Claude Code 向け指示：「いついつ以降に」の待ちはすべてチャットの予約（send_later）で自動で再開する、という規則を docs/notes/chat-routines.md「期日とカレンダー」に足す
Chat-Ref: CHAT-1114-SEO-29
マージ: ドキュメントのみ（docs/notes・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（SEO-28 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1114-seo29〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1114-seo29 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1114-seo29 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1114-seo29 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで docs/decisions/*.md・docs/logs/ の末尾の追記どうし、または両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
「12/1 の 10:00 以降に貼ってください」のように時機を待つものを、平野さんが覚えておく形をやめ、チャット側が予約（send_later）を入れて自動で再開する。チャット全体の規則として docs に書く。

### 決定（2026-11-14、平野さん）
- 「いついつ以降に（貼る・確かめる・伺う）」のように時機を待つものは、すべてそのチャットに届く予約（send_later）を入れ、自動で再開（リマインド）する。平野さんに時機を覚えておいてもらう形にしない

### 前提（チャット側。平野さんの決定ではない）
- 今の docs/notes/chat-routines.md「期日とカレンダー」（mj-logs の guide/e8adaade の版でチャット側が読んだ）は、予約を「チャット側が自分で確かめられる後での確認（mj-logs・REST・本番の WebFetch・カレンダー）を、同じチャットで数分〜翌日に行うとき」に限り、「予約に向かないものは今までどおりカレンダーで扱う: 平野さんの手作業、数日より先の確認、ほかの系列のチャットが担当する確認…、Chrome でしか見られない確認」としている。上の決定はこの範囲を広げるので、この2項目と食い違う（今の内容は cloudflare で確かめること）
- 文案（チャット側の案。実物に合わせて変えてよい）:
  - 予約の項目の範囲を「数分〜翌日の、チャット側が自分で確かめられる確認」から「時機を待つものすべて（指示文の『貼る時機』・後での確認・後で伺う問い）」に広げる。予約が届いたら、チャット側が mj-logs 等で状態を確かめてから平野さんに伝える（何を貼る・何を見るかを書く）。予約の時刻は返答に書く
  - 「予約に向かないもの」の項目は、「予約に加えてカレンダーでも扱うもの」に置き換える: 平野さんの手作業、Chrome でしか見られない確認、ほかの系列のチャットが担当する確認（届くのは予約したチャットだけのため、担当の系列のチャットに予約を入れるか、カレンダーに書く）。「数日より先の確認」を外す
  - 「翌日以降の確認は、カレンダーにも予定を作り、説明に予約の日時を書く」はそのまま残す（期日の正はカレンダー）
  - 実例: CHAT-1201-SEO-26（12/1 10:00 以降に貼る）を、2026-11-14 に SEO 系列のチャットが予約した
- chat-routines.md は文書の上限の対象外（check-assets.yml の4文書ではない）。docs/notes/chat-side-operations.md は変えない（chat-side-operations.md の表の「期日・カレンダー・確認の予約」は chat-routines.md「期日とカレンダー」を指しているので、そのままでよい。食い違えば止まる）

## 手順
1. 同じ論点（チャットの予約・send_later・リマインド・貼る時機）の issue を、open・closed とも検索する。同じ論点の open issue があれば止まって報告する（closed は見つけたものをログに書いて進めてよい）
2. docs/notes/chat-routines.md「期日とカレンダー」の今の内容を読み、上の決定と前提の文案で直す（同じ趣旨の記述は置き換え・統合する。直した前後をログに引用する）。CLAUDE.md・docs/handover.md・docs/instruction-template.md・docs/notes/chat-side-operations.md に「予約は数分〜翌日」「数日より先の確認はカレンダー」と同じ趣旨の記述が無いかを grep し、あれば直さずにログに書く。docs/decisions/operations.md に決定を足す（docs/decisions/README.md の書き方に従う）
3. 完了報告のうえ `git push origin work/1114-seo29:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。ログを仕上げる

## 止まる条件
- 手順1で同じ論点の open issue があった
- chat-routines.md の今の記述が上の決定と矛盾し、どちらが正か判断が要る（範囲を広げるだけなら止めずに直してよい）
- 差分（cloudflare との）に、docs/notes/chat-routines.md・docs/decisions/operations.md・docs/logs/ 以外のファイルがある
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が chat-routines.md の「期日とカレンダー」の節を変えている（同じファイルの別の節の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1114-SEO-29.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1114-SEO-29 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべて揃っている
- Chat-Ref の重複: `git log --all --grep="CHAT-1114-SEO-29"` は0件。SEO 系列の続きで、識別子の重複確認は対象外
- 作業ブランチ: `work/1114-seo29` はローカル・リモートとも無し。`origin/cloudflare`（ec1ef47122）から作った
- **規則の表の確認で止まった。** この指示は docs/notes/chat-routines.md「期日とカレンダー」（チャット側のガイド文）の規則を広げるものだが、指示文に「知見 → 選んだ層 → 理由」の表が無い。
  CLAUDE.md「Chat-Ref」の「ガイド文（CLAUDE.md・handover.md・docs/notes/ のチャット側の文書）に規則を足す指示に『知見 → 選んだ層 → 理由』の表が無ければ、着手せず報告して止まる」（#492）に当たる。
  平野さんの決定から来る規則でも、表の要否の例外は CLAUDE.md に無い。同じ止まり方の前例は CHAT-1104-MNL-01（docs/notes/chat-side-operations.md に規則を1項目足す指示）・CHAT-1103-PLG-02
- 手順 1〜3（issue の検索、chat-routines.md の確認・編集、ほかの文書の grep、docs/decisions への追記、マージ）はどれも行っていない。docs/decisions/ への決定の追記もしていない（着手していない指示のため）


### 1. ログのマージ

- push 直前に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真、差分が docs/logs/CHAT-1114-SEO-29.md だけであることを確かめ、`git push origin work/1114-seo29:cloudflare` → ec1ef47122..ddc26ebeaf

## 報告

- 状態: 判断待ち
- ブランチ: work/1114-seo29
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1114-SEO-29.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1114-seo29
- 確認用URL: なし
- マージ: 済（ログのみ。ddc26ebeaf）
- issue: なし
- 判断が必要なこと:
  - 着手前に止まった。docs/notes/chat-routines.md「期日とカレンダー」の予約の規則を広げる指示だが、「知見 → 選んだ層 → 理由」の表が無い（CLAUDE.md「Chat-Ref」、#492）。表を付けて新しい番号で貼り直してほしい（SEO-29 は欠番）。
    検討の材料（docs/notes/chat-routines.md「申送り」の観点のチェックリストの順）: 計算的センサー（テスト・hook・ワークフロー）は、チャット側が予約を入れたかを見られない。推論的センサーの候補は、チャット側の返答の前の確かめ（「貼る時機」が「いつでも」以外の指示文を出すときに予約を入れたか）で、Code の着手前の確認には載らない。ガイド文を選ぶなら、表の「理由」にこの2つを外した理由を書く
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fadaee0e）: https://github.com/retroeater/mj-logs/tree/main/guide/fadaee0e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fadaee0e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fadaee0e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fadaee0e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fadaee0e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fadaee0e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fadaee0e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6aa6642b.md
