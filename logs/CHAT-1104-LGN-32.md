# CHAT-1104-LGN-32

- 着手日時: 2026-11-04
- 対象issue: #601（コメントのみ）
- ブランチ: work/1104-lgn-gcp（LGN-29 の未マージのブランチを続けて使う。origin/cloudflare を merge で取り込んだ）
- 着手時HEAD: 12f23348e

## 指示

【Claude作成】Claude Code 向け指示：docs/notes/google-cloud.md を公開してよい設定だけで書く（LGN-29 の続き）
Chat-Ref: CHAT-1104-LGN-32
マージ: ドキュメントのみ（docs/notes・docs/handover.md・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1104-LGN-29 が判断待ちで止まった後。同じセッションでも新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-gcp への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lgn-gcp を続けて使う（LGN-29 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/handover.md` の関連文書の表に、ほかのセッションの行と同じ位置に足された衝突、`docs/decisions/*.md`・`docs/logs/` の末尾への追記どうしの衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1104-LGN-29 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-LGN-32` を足す。

## 目的
LGN-29 は、#601（認証情報の台帳。置き場所は未決）と主題が重なるため止まった。docs/notes は public の mj-logs に写る。サービスアカウントや API キーの名前・権限の範囲を載せると、攻撃の手がかりになる。そこで google-cloud.md には公開してよい設定だけを書き、Google Cloud の設定値を1か所にまとめる（LGN-29 の目的）。

### 決定（2026-11-04、平野さん）
- 案 (a): docs/notes/google-cloud.md は、公開してよい設定だけにする。載せるのは、有効にした API・割り当ての値・請求の形・無料の枠と単価・設定を変えたときの手順。サービスアカウント・API キー（名前・制限・共有先・Secret 名）の行は #601 の台帳に回す
- LGN-29 の決定（設定値を1つの文書にまとめる。GetPlaceRequest の1日の割り当てを 125,000 から 3,000 に下げた）はそのまま
- 11月の閉業の判定はやり直さず、2026-12-01 の予約に任せる（LGN-29 の判断が必要なこと (2) の答え。直しは CHAT-1104-LGN-31 が行う）

### 前提（チャット側。平野さんの決定ではない）
- 割り当ての値（平野さんが Google Cloud の画面で確かめた、2026-11-04）: GetPlaceRequest は1日 3,000・1分 600。2026-11-04 に、閉業の判定（LGN-28）の2回の本番の実行で、1日の 3,000 に 100% 達した（1分は 43%）。Text Search・Nearby Search の1日の値は「要確認（平野さんが画面で確かめる）」と書く。Nearby Search は 2026-10-29 の決定で使わなくなった
- 書かないもの: サービスアカウントの名前・メールアドレス、API キーの名前・制限、Secret 名、プロジェクトの ID・番号、スプレッドシートの ID、共有先。これらに触れる必要があるところは「#601 の台帳」と書く
- 各所の文書にある、公開してよい設定（割り当て・無料の枠・単価など）の記述は、google-cloud.md への参照に置き換える。サービスアカウント・鍵の記述は、この指示では動かさない（#601 で扱う）。動かさなかった記述の場所はログの表に残す
- #601 に、google-cloud.md を公開してよい設定だけで作ったこと、サービスアカウント・鍵の行は台帳に回したこと、ログの洗い出しの表（ファイル名と節の名前だけ）をコメントする
- 知見 → 選んだ層 → 理由（handover.md に行を足すため。CLAUDE.md「Chat-Ref」節の求め）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| Google Cloud の設定が各所の文書に分かれていて、割り当てなどの今の値を探しにくい（2026-11-04、GetPlace の割り当ての確認で、平野さんが画面を開くまで値が分からなかった） | docs/notes/google-cloud.md（新しい文書。公開してよい設定だけ）と handover.md「6. 関連文書」の1行 | 設定の画面はセッションから見られず、テスト・hook では確かめられないので、cloudflare.md と同じく手で直す文書が合う。認証情報は #601 の台帳に分ける（public に写るため）。CLAUDE.md・chat-side-operations.md には足さない（上限があり、場面限定の内容のため） |

## 手順
1. LGN-29 の洗い出しの表から、公開してよい設定と、#601 に回すものを分けてログに書く
2. docs/notes/google-cloud.md を書く。handover.md の表に行を足し、各所の重なる記述を参照に置き換え、docs/decisions に決定を足す。#601 にコメントする
3. 書いた文書に、メールアドレス・ID・鍵の値・Secret 名・サービスアカウント名が無いことを `git grep` で確かめ、結果をログに書く。完了報告のうえ cloudflare へ入れる

## 止まる条件
- ガイド文（CLAUDE.md・handover.md・chat-side-operations.md）が、容量の上限の警告域に入る（足す前に止まって報告する）
- 書くのに、メールアドレス・ID・鍵の値・サービスアカウント名・Secret 名が要る（書かずに止まる）
- 冒頭に書いた以外の取り込みの衝突がある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。要確認の値（Text Search・Nearby Search の1日の割り当て）を、平野さんが確かめる項目として「判断が必要なこと」に書く（確かめる画面: https://console.cloud.google.com/apis/api/places.googleapis.com/quotas ）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-32.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-32 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1104-LGN-32` のコミットなし。雛形の行の欠けなし。知見 → 選んだ層 → 理由の表あり。CHAT-1104-LGN-29 のログの状態を「判断待ち / 続き: CHAT-1104-LGN-32」にした
- 作業ブランチ: ローカルの work/1104-lgn-gcp は origin/work/1104-lgn-gcp と同じ（847866317）。`git checkout work/1104-lgn-gcp` → `git merge --ff-only origin/work/1104-lgn-gcp`（変化なし）→ cloudflare が祖先でなかったので `git merge origin/cloudflare`（衝突なし、12f23348e）
- 手順1の前提との違い: LGN-29 は手順1の洗い出しの表を作る前に止まったので、「LGN-29 の洗い出しの表」は無い。この指示で洗い出した（下の表）
- 洗い出し（`git grep` で、コード・ワークフローが呼ぶ Google の API と、docs の Google Cloud の設定の記述）:

| 種類 | 場所（ファイル・節） | 扱い |
|---|---|---|
| コードが呼ぶ API | scripts/lib/sheets.py・sheets_write.py・workers/ask/src/weekly.mjs（Sheets）、fetch_x_posts.py・lib/ryoei_book.py（Drive）、lib/gcal.py・sync_birthday_calendar.py（Calendar）、fetch_gsc.py（Search Console）、fetch_youtube_channels.py・fetch_youtube_meta.py・fetch_live_channel_raw.py（YouTube Data）、convert_x_posts.py（Places API (New)） | 公開してよい → google-cloud.md「コードが呼ぶ API」 |
| Places の割り当て | docs/decisions/operations.md の 2026-10-19（CHAT-1019-LOG-02、Text Search・Nearby Search の1日）・2026-11-04（CHAT-1104-LGN-28、GetPlace） | 公開してよい → google-cloud.md「割り当て」（decisions は記録なので動かさない） |
| GetPlace の1日の割り当てと月の無料の枠の値 | docs/notes/log-page.md「データの持ち方」の外す店の項（LGN-31 で書いた運用の注意） | 値を google-cloud.md への参照に置き換えた |
| 請求の形（従量課金・月額のプランを使わない） | docs/decisions/operations.md の 2026-10-17（CHAT-1017-MCK-15） | 公開してよい → google-cloud.md「請求」 |
| 無料の枠と単価 | どの文書にも無かった（LGN-28 の指示文の前提の「月 5,000回」だけ） | Google の料金表（最終更新 2026-11-02 UTC）と Data fields のページで 2026-11-04 に確かめて google-cloud.md「無料の枠と単価」に書いた（Text Search Essentials (IDs Only) は無制限、Place Details Pro は月 5,000回・$17.00/1,000回、`businessStatus` は Place Details Pro） |
| サービスアカウント・鍵・API キーの記述（名前・Secret 名・共有先・制限） | docs/handover.md、docs/notes の abema-book.md・ask-worker.md・birthday-calendar.md・books-calendar.md・cloud-sessions.md・cloudflare.md・dojo-guest-calendar.md・github-actions.md・handover-archive-2026.md・live-channel-write.md・live-page-design.md・pro-pages.md・ryoei-world.md・security.md・sheets-reader.md・sns-book.md・static-generation.md・video-wayhome.md・yotei-sheet.md、docs/decisions/operations.md の 2026-10-17（`GOOGLE_PLACES_API_KEY` の制限） | #601 に回す。この指示では動かしていない |
| YouTube・Sheets の使用量（ユニット・429） | live-channel-write.md・live-page-design.md・yotei-sheet.md（ユニットの使用量）、sheets-reader.md「残り」・static-generation.md（Sheets の1分の 429） | 設定値でなく使用量・挙動の記述なので動かしていない。google-cloud.md には「記録していない」と #662 の参照だけ |

- 書いたもの（de63dfd8f）: docs/notes/google-cloud.md（新規）、docs/handover.md「6. 関連文書」の表に1行（cloudflare.md の次）、docs/notes/log-page.md（割り当て・無料の枠の値を google-cloud.md への参照に）、docs/decisions/operations.md（LGN-29・32 の決定）
- 容量: handover.md 24,518 バイト（警告域 26,624 の外）、CLAUDE.md・chat-side-operations.md は変えていない
- 書かないものの確かめ（docs/notes/google-cloud.md に対して `git grep -nE`）: メールアドレスの形 0件、`AIza…`・40字以上の英数字・`iam.gserviceaccount`・`projects/<数>`・project id 0件、`*_KEY`・`*_TOKEN`・`*_SECRET`・`*_SALT`・`SA_KEY` 0件、サービスアカウントの役割の名前（sheets-reader・birthday-calendar・gsc-export・live-channel-writer・mj-ask、大文字小文字を問わず）0件。書く途中で、表の「Worker `mj-ask`」（Worker の名前がサービスアカウントの名前と同じ）と `scripts/check_sheets_reader.py`（ファイル名がサービスアカウントの名前を示す）に気づき、書き換え・削除した
- #601 にコメント（google-cloud.md を公開してよい設定だけで作ったこと、サービスアカウント・鍵の行は台帳に回したこと、書いている所のファイル名）

## 報告

- 状態: 完了
- ブランチ: work/1104-lgn-gcp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-LGN-32.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-gcp
- 確認用URL: なし（docs のみ）
- マージ: 済（docs のみ。docs/notes・docs/handover.md・docs/decisions・docs/logs〈LGN-29・32〉）
- issue: #601（コメントのみ）
- 判断が必要なこと: Places の Text Search（SearchTextRequest）・Nearby Search（SearchNearbyRequest）の1日・1分の割り当ての今の値を、平野さんが https://console.cloud.google.com/apis/api/places.googleapis.com/quotas で確かめる（google-cloud.md の表は「要確認」。2026-10-19 の決定では変換が済んだら 1,000 に戻す。分かったら表を直す）
- 未確認の項目: 「有効な API」の画面の一覧（コードが呼べていることから有効と書いた。画面はセッションから見られない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ada6990d）: https://github.com/retroeater/mj-logs/tree/main/guide/ada6990d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ada6990d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
