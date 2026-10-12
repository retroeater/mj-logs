# CHAT-1012-BOT-06

- 着手日時: 2026-10-13（JST）
- 対象issue: #304
- ブランチ: work/1012-bot
- 着手時HEAD: 4fc84963b

## 指示

【Claude作成】Claude Code 向け指示：2026-10-12 の BOT のチャットの申送り3件 — #304 に担当のチャット系列を書く、Chrome からダッシュボードの API を読む手順と check-run の待ち方を docs/notes に残す（コード・設定は変えない） Chat-Ref: CHAT-1012-BOT-06 マージ: ドキュメントのみ（docs/notes・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-bot を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-bot origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-12 の BOT のチャット（BOT-01〜05）の振り返りで、機械的な手順に落とせる知見を3つ書き残す。docs/notes/chat-side-operations.md は警告域まで残り1バイト（#492 のコメント、CHAT-1012-GCL-03）なので、このファイルには1文字も足さない。 docs/instruction-template.md も残りが少ない（14,013 / 警告 14,336）ので足さない。置き場所は #304 の本文と、上限の無い docs/notes/cloudflare.md・chrome-reading.md。
決定（2026-10-13、平野さん）

* 月次運用チェック（#304）は MCK 系列のチャットだけが行う。ほかの系列のチャットは手順を読んでも実施しない（2026-10-12 に ACF と BOT の2つのチャットが同じ回を重ねたため。カレンダーの繰り返しの予定の説明にはチャット側が書いた）
* 振り返りの残りの知見（指示文を作る直前の mj-logs の grep、カレンダーの主催者の確認）は、文書には足さない（前者は chat-side-operations.md に既にある規則で、容量も無い。後者はメモリーで足りる）

前提（チャット側。平野さんの決定ではない）

* 書く内容は規則と理由の一句だけ。事例（BOT-02〜05 の経緯）は `docs/notes/handover-archive-2026.md` へ（既存の「docs/notes/… から」の節の形に合わせる。無ければ節を作る）
* A（#304 の担当）: 本文の冒頭に「担当: MCK 系列のチャット。 ほかの系列は手順を読んでも実施せず、平野さんに MCK のチャットへ送るよう伝える」の1〜2行。着手中コメントの規則（CLAUDE.md「issueの着手ルール」）はそのまま
* B（Chrome からダッシュボードの API を読む、cloudflare.md）: 2026-10-12 にチャット側が実際に読めた方法。ダッシュボード（dash.cloudflare.com）にログインした Chrome のタブで、`javascript_tool` から `fetch('/api/v4/<path>', {credentials:'include', headers:{'x-cross-site-security':'dash'}})` を GET だけで呼ぶ。読めたもの: `zones/{id}/settings`（HTTP/3・0-RTT・Always use HTTPS・Rocket Loader・Early Hints・Email Obfuscation・Polish 等）、`zones/{id}/settings/speed_brain`、`zones/{id}/bot_management`（AI bot policies・AI Labyrinth〈crawler_protection〉・SBFM）、`zones/{id}/rulesets/phases/http_ratelimit/entrypoint`・`.../http_request_firewall_custom/entrypoint`（レート制限・カスタムルール）、`zones/{id}/flags/products/cache/changes`（Crawler Hints の適用履歴）、`accounts/{id}/registrar/domains/ryoei.pro`（自動更新・期限）、`accounts/{id}/workers/scripts` と `accounts/{id}/builds/workers/{tag}/triggers`（Workers Builds のトリガー。本番と非本番の2つ）。`zones/{id}/settings/crawlhints` は 400（設定名が違う）。規則として書くのは「月次の (9) の照合はこの方法で読める。GET だけ。書き込みはしない。戻り値の `?` `&` `=` は置き換えて返す（chrome-reading.md の注意と同じ）」。ゾーン ID・アカウント ID は書かない（mj-logs は public。ダッシュボードの URL から毎回取る）
* B の補足（chrome-reading.md）: 「1つの確認につき操作1回（タイムアウト4分）」の規則は GitHub の mj を読むときのもの。ダッシュボードの読みは 2026-10-12 に十数回の操作で問題なく、適用しない旨を1行足す（要確認: 今の文面）
* C（check-run の待ち方、cloudflare.md「work/ ブランチのプレビュー」か「ビルド成否と本番の確認範囲（check-runs）」の合う方）: 「ビルドが走るかを試す push は、前の push の『Workers Builds: mj』が completed になってから次を push する。付くまで15分は待つ（Workers Builds は順番待ちで遅れる。4分では付いていないように見えた例がある）」。BOT-04・05 の経緯は archive へ
* docs/notes/cloudflare.md の該当節の今の文面は要確認（置き場所は実物に合わせて変えてよい）

手順

1. 確かめる: #304 の本文の冒頭、docs/notes/cloudflare.md の「本番反映（デプロイ）の仕組み」「work/ ブランチのプレビュー」「ビルド成否と本番の確認範囲」、docs/notes/chrome-reading.md「使い方」、handover-archive-2026.md の節の構成を読む。同じ趣旨の記述（Chrome からの API 読み・check-run の待ち方）が既にあれば置き換え・拡張してよく、どう処理したかを報告に書く。「月次 担当」「dashboard API」「check-run 待ち」の語で open issue を検索し、同じ論点があれば止まる
2. 書く: A を #304 の本文に（コメントで何を変えたかも1行）。B・C を docs/notes/cloudflare.md に、B の補足を chrome-reading.md に。事例を archive に。各ファイルの前後のサイズを報告に書く（cloudflare.md・chrome-reading.md・archive は上限なし。chat-side-operations.md・instruction-template.md・CLAUDE.md・handover.md は変えない）
3. 決定を `docs/decisions/` の合う分野（operations.md・cloudflare.md の見込み）に足す。handover.md「関連文書」の cloudflare.md の行に「設定の照合（Chrome）」の語を足す必要があるかを見て、足すなら容量を測る（警告域なら足さず報告）

止まる条件

* chat-side-operations.md・instruction-template.md に1バイトでも足す必要が出た（足さずに止まる）
* 同じ論点の open issue がある
* 追記先に、この決定と矛盾する記述がある（同じ趣旨なら止めず、置き換え・拡張してよい）
* 未マージの work/ ブランチが cloudflare.md・chrome-reading.md の同じ節、`docs/decisions/` の同じファイルの末尾、または #304 の本文を変えている（`git branch -r --no-merged origin/cloudflare` と #304 の着手中コメントで確かめる）、または取り込みで衝突する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-BOT-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-BOT-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-bot
- ログ: https://github.com/retroeater/mj/blob/work/1012-bot/docs/logs/CHAT-1012-BOT-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-bot
- 確認用URL: なし
- マージ: 未
- issue: #304
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3ce5d549）: https://github.com/retroeater/mj-logs/tree/main/guide/3ce5d549

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3ce5d549/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3ce5d549/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3ce5d549/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3ce5d549/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3ce5d549/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3ce5d549/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/62f48a8b.md
