# CHAT-1021-MCK-21

- 着手日時: 2026-10-21
- 対象issue: #304
- ブランチ: work/1021-mck-304
- 着手時HEAD: 0b519499b

## 指示

【Claude作成】Claude Code 向け指示：#304（月次運用チェック）の本文に、Workers Paid の使用量の確認（Workers Builds のビルド1回の所要時間を含む）を項目として取り込む Chat-Ref: CHAT-1021-MCK-21 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1021-mck-304 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1021-mck-304 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-mck-304 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-21 に Cloudflare のアカウントが Workers Paid になり、CHAT-1021-WKP-01 が #304 に「追加の項目」のコメント（Workers Paid の使用量の確認）を置いた。#304 の担当は MCK 系列なので、そのコメントを本文のチェック項目の一覧に取り込む。あわせて、BIL のチャットからの伝言のとおり、同じ項目の子として「直近のビルド1回の所要時間」を入れる。
決定・依頼（2026-10-21）

* #304 の担当は MCK 系列のチャット（2026-10-13 の申送り。2026-10-17 の分割後も同じ）
* BIL のチャットからの伝言（平野さん経由、CHAT-1021-BIL-04 で #304 の本文は直さずこちらへ回した）: 本文へ取り込むときに、同じ項目の子として「直近のビルド1回の所要時間（Workers & Pages → mj → Deployments）」も入れる。理由: 10月はビルドの回数がほぼ同じまま、1回の時間が 33 秒（9/29）から 3〜4 分（10/21）に伸び、Free の枠（月3,000分）を使い切りかけた。使用量だけを見ると伸びに気づくのが遅れる（BIL 系列の振り返り・Fable 5.1 のレビュー）
* 2026-10-21 に Workers Paid（月6,000分、期間は毎月9日〜翌月9日）へ切り替え済み（記録は docs/notes/cloudflare.md「ビルド時間の見積もり」）

前提（チャット側。平野さんの決定ではない）

* WKP-01 のコメント: https://github.com/retroeater/mj/issues/304#issuecomment-6306369107 。文面は WKP-01 の指示の案では「Cloudflare の Billing（Workers Paid の使用量: Workers Builds のビルド分〈月 6,000 分〉・Workers Logs の件数〈月 2,000 万件〉・Workers のリクエスト・D1）を見て、枠の 8 割を超えた項目があれば #363 にコメントする」（実際のコメントの文面を読んで、それを正とする。要確認）
* 本文の今の一覧は (1)〜(12)（カレンダー「【R#304】月次運用チェック」の説明による。要確認）。取り込む項目は (13) を推す。番号・書き方は本文の今の形に合わせる
* 子の項目の文面（チャット側の案）: 「直近のビルド1回の所要時間（Workers & Pages → mj → Deployments）を控え、前月より大きく伸びていれば #363 にコメントする」。伸びの目安（何倍・何分）は書かない（目安が決まっていないため。必要なら「判断が必要なこと」に書く）
* 取り込んだら、WKP-01 のコメントに「本文の (13) に取り込んだ」と返信する（コメントは消さない）

手順

1. #304 の本文と WKP-01 のコメントを読み、他セッションの着手中コメントが無いことを確かめる。本文の今の一覧の番号と書き方をログに書く
2. #304 の本文に、WKP-01 のコメントの項目と、その子として「直近のビルド1回の所要時間」を足す（ほかの項目・ラベル・状態は変えない）。変える前と後の該当箇所をログに引用する。WKP-01 のコメントに取り込んだ旨を返信する
3. 決定・依頼を docs/decisions/operations.md に記録する。「判断が必要なこと」に、ビルド時間の伸びの目安を決めるかを書く（決めなくてもよい）

止まる条件

* #304 が Closed、または他セッションの着手中コメントがある
* WKP-01 のコメントが見つからない、または本文にすでに同じ項目がある（どこにあるかを書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-MCK-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-MCK-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5175e9bd）: https://github.com/retroeater/mj-logs/tree/main/guide/5175e9bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0b519499.md
