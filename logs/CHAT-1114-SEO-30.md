# CHAT-1114-SEO-30

- 着手日時: 2026-11-14
- 対象issue: なし
- ブランチ: work/1114-seo30
- 着手時HEAD: 5236216911

## 指示

【Claude作成】Claude Code 向け指示：「いついつ以降に」の待ちはすべてチャットの予約（send_later）で自動で再開する、という規則を docs/notes/chat-routines.md「期日とカレンダー」に足す
Chat-Ref: CHAT-1114-SEO-30
マージ: ドキュメントのみ（docs/notes・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（SEO-29 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1114-seo30〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1114-seo30 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1114-seo30 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1114-seo30 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで docs/decisions/*.md・docs/logs/ の末尾の追記どうし、または両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
「12/1 の 10:00 以降に貼ってください」のように時機を待つものを、平野さんが覚えておく形をやめ、チャット側が予約（send_later）を入れて自動で再開する。チャット全体の規則として docs に書く。CHAT-1114-SEO-29 は「知見 → 選んだ層 → 理由」の表が無く着手前に止まった（SEO-29 のログの `## 報告`）ので、表を付けて出し直す。

### 決定（2026-11-14、平野さん）
- 「いついつ以降に（貼る・確かめる・伺う）」のように時機を待つものは、すべてそのチャットに届く予約（send_later）を入れ、自動で再開（リマインド）する。平野さんに時機を覚えておいてもらう形にしない

### 知見 → 選んだ層 → 理由（チャット側の案）

| # | 知見（起きたこと） | 選んだ層 | 理由 |
|---|---|---|---|
| 1 | 「12/1 の 10:00 以降に貼ってください」（CHAT-1201-SEO-26）のように時機を待つ指示を、平野さんが覚えておく形で渡していた。今の chat-routines.md「期日とカレンダー」は予約（send_later）を「数分〜翌日の、チャット側が自分で確かめられる確認」に限り、数日より先や平野さんの手作業はカレンダーだけにしている | ガイド文（docs/notes/chat-routines.md「期日とカレンダー」の予約の項目を広げ、「予約に向かないもの」の項目を置き換える。純増は小さく、上限の無い文書） | 計算的センサー（テスト・hook・ワークフロー）は、チャット側のツールで入れる予約を見られない（mj にも mj-logs にも予約の記録が残らない）。推論的センサー（Code の着手前の確認）も同じで、Code は予約の有無を確かめられない。チャット側の返答の前の確かめ（「貼る時機」が「いつでも」以外の指示文を出したら予約を入れる）が唯一の置き場で、それは chat-routines.md の手順として書くしかない |

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
3. CHAT-1114-SEO-29 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1114-SEO-30` を足す。完了報告のうえ `git push origin work/1114-seo30:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。ログを仕上げる

## 止まる条件
- 手順1で同じ論点の open issue があった
- chat-routines.md の今の記述が上の決定と矛盾し、どちらが正か判断が要る（範囲を広げるだけなら止めずに直してよい）
- 差分（cloudflare との）に、docs/notes/chat-routines.md・docs/decisions/operations.md・docs/logs/ 以外のファイルがある
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が chat-routines.md の「期日とカレンダー」の節を変えている（同じファイルの別の節の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1114-SEO-30.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1114-SEO-30 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。同じ Chat-Ref のコミット0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は全部ある
- ガイド文に規則を足す指示で、「知見 → 選んだ層 → 理由」の表がある（SEO-29 で止まった理由が解消）
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1114-seo30 origin/cloudflare`（5236216911）
- 未マージの work/ ブランチで docs/notes/chat-routines.md を変えているものは無い（`git branch -r --no-merged origin/cloudflare` の各ブランチと `git diff --quiet origin/cloudflare...<branch> -- docs/notes/chat-routines.md`）

### 1. 同じ論点の issue

`gh api -X GET search/issues -f q="repo:retroeater/mj is:issue <語>"` で「send_later」「予約 チャット」「リマインド」「貼る時機」「カレンダー 予約」を検索した。チャットの予約で待ちを再開する論点の issue は open・closed とも無かった。近いもの: #504（open、予約実行を Cloudflare の Worker から時刻どおりに起動する。ワークフローの予約実行で論点が別）、#649（closed、ryoei.net の移管の後の確認。send_later を確認に使った事例）

### 2. chat-routines.md「期日とカレンダー」の直し（28,271 → 28,449 バイト）

直す前（2項目）:

> - **チャット側が自分で確かめられる後での確認（mj-logs・REST・本番の WebFetch・カレンダー）を、同じチャットで数分〜翌日に行うときは、そのチャットに届く予約（send_later）を入れる。**
>   返答に予約の時刻を書き、届いたら確かめて、結果に合わせて次の指示文まで用意する（平野さんが「確かめて」と送る手間をなくすため。ワークフローの定期実行〈docs/notes/glossary.md〉とは別物）
>
> - **予約に向かないものは今までどおりカレンダーで扱う**: 平野さんの手作業、数日より先の確認、ほかの系列のチャットが担当する確認（届くのは予約したチャットだけで、文脈が途切れる）、Chrome でしか見られない確認

直した後:

> - **時機を待つものは、すべてそのチャットに届く予約（send_later）を入れる**（指示文の「貼る時機」・後での確認・後で伺う問い。数分後でも数週間後でも同じ。例: 12/1 10:00 以降に貼る CHAT-1201-SEO-26 を 11/14 に予約した）。
>   返答に予約の時刻を書く。届いたら mj-logs・REST などで状態を確かめてから、平野さんに何を貼る・何を見るかを伝え、結果に合わせて次の指示文まで用意する（平野さんに時機を覚えておいてもらわないため。ワークフローの定期実行〈docs/notes/glossary.md〉とは別物）
>
> - **予約に加えてカレンダーでも扱うもの**: 平野さんの手作業、Chrome でしか見られない確認、ほかの系列のチャットが担当する確認（届くのは予約したチャットだけで、文脈が途切れる。担当の系列のチャットに予約を入れるか、カレンダーに書く）

- 「翌日以降の確認は、カレンダーにも予定を作り、説明に予約の日時を書く」と「要らなくなった予約は取り消す」はそのまま
- 決定と矛盾する記述は無かった（範囲を広げただけ）

### 3. ほかの文書の grep

`grep -n -E '数分〜翌日|数日より先|予約に向かない'` と `grep -n -E 'send_later|予約（|確認の予約'` を CLAUDE.md・docs/handover.md・docs/instruction-template.md・docs/notes/chat-side-operations.md に: 同じ趣旨の記述は無し。chat-side-operations.md 73行目の表の行「期日・カレンダー・確認の予約 | `docs/notes/chat-routines.md`「期日とカレンダー」」は参照だけで、そのままでよい

### 4. その他

- docs/decisions/operations.md に「## 2026-11-14（CHAT-1114-SEO-30）」の節を足した
- SEO-29 のログの状態を「判断待ち / 続き: CHAT-1114-SEO-30」にした


### 5. マージ

- push 直前に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真、cloudflare との差分が docs/notes/chat-routines.md・docs/decisions/operations.md・docs/logs/（SEO-29・SEO-30）だけであることを確かめ、`git push origin work/1114-seo30:cloudflare` → 5236216911..52e1231a9b

## 報告

- 状態: 完了
- ブランチ: work/1114-seo30
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1114-SEO-30.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1114-seo30
- 確認用URL: なし
- マージ: 済（52e1231a9b、`git push origin work/1114-seo30:cloudflare`）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e026d556）: https://github.com/retroeater/mj-logs/tree/main/guide/e026d556

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e026d556/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e026d556/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e026d556/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e026d556/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e026d556/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e026d556/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6aa6642b.md
