# CHAT-1125-LVP-37

- 着手日時: 2026-11-25
- 対象issue: #403・#688
- ブランチ: work/1125-lvp
- 着手時HEAD: 2a1c87497

## 指示

【Claude作成】Claude Code 向け指示：決勝が1日の大会で対局日が空のまま残る行と、麻雀日本シリーズ 2023 の「第3節」が無い理由を調べ、規則の漏れなら直す Chat-Ref: CHAT-1125-LVP-37 マージ: 承認済み（チャットで）。生成物・【2】の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1125-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1125-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1125-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1123-LVP-36 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1125-LVP-37` を足す。

目的
CHAT-1123-LVP-36 の後にチャット側が本番とシートで見つけた2点の原因を調べ、【2】の規則の漏れなら直す。
決定（2026-11-25、平野さん）

* 下の2点を、#401 の4人の反映より先に調べる

前提（チャット側。平野さんの決定ではない）

* 対局日: 2026-11-24 の毎朝の取り込みの後の【2】で、決勝が1日の大会（王位戦・WRCリーグ・麻雀マスターズ・新人王戦・リーチ麻雀世界選手権・小島武夫杯帝王戦・紅龍戦・昇龍戦）の決勝段階（ステージに「決勝」「決定戦」を含む）で、掲載が Y（【3】か、空なら【2】の自動）の動画（種別「動画」）は231行。そのうち対局日（【3】、空なら【2】）が入っているのは186行、空は45行。例: 王位戦 第45期 `EuJraTL8m4E`・`n3pEeKU5je4`、紅龍戦 第2期 `a-_3BPgVmJg`、リーチ麻雀世界選手権 第2回 `ZMG8JubQgek`・`HUGUDFB5pR8`。LVP-36 の見込みでは、写せないのはチャンピオンズリーグ 第29・30期と世界選手権 第1・2回の14行だけだった。チャット側の数え方（ステージの語・種別・掲載の見方）が LVP-36 と違う可能性もある
* 第3節: 2026-11-25 の本番 `live/nihon-series/2023.html` は「第1節 → 第2節 → 第4節 → … → 第7節 → プレーオフ第1節 → プレーオフ第2節 → 決勝」で、「第3節」が無い（2024 は第1〜7節がそろう）

手順

1. #403・#688 に着手中のコメントを書く。対局日: 上の45行（数え方を LVP-36 とそろえて数え直す）を、空のままの理由ごとに分ける（「タイトル」の決勝日が `YYYY-MM-DD` でない・期の対応が取れない・【3】の対局日が別の値で上書き・規則の判定から漏れた など）。規則の漏れで埋められる行は【2】の規則を直し、テストを足す。「タイトル」の値の不足・【3】の値が原因の行は、平野さんが直す値の表（行・動画ID・列・値・理由）にする
2. 第3節: 麻雀日本シリーズ 2023 の動画を raw・【2】・【3】で洗い、第3節の動画が無いのか（チャンネルに無い・限定・削除など）、判別から漏れたのか（題名の書き方・別の節・予選に入った など）を書く。漏れなら【2】の規則を直し、テストを足す。ほかの年・女流で同じ形の欠け（節の番号が飛ぶ）が無いかも全件見る
3. 規則を直したときは全ページを再生成して生成物と【2】の差分を書き、テストを回す。止まる条件に当たらなければ CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる。調べた結果と直したことを #403・#688 にコメントし、docs/decisions/live.md に足す

止まる条件

* 規則の変更で、上の45行と日本シリーズの節のほかに【2】の値が変わる行がある（全件を書いて、マージせずに止まる）
* 生成物の差分が、日本シリーズの節のページと、それに伴う一覧・件数・pro/ のほかに出る（マージせずに止まる）
* 【2】【3】・「タイトル」シートの読み込みが失敗する
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/lib/live.py`・`scripts/lib/live_extract.py`・`scripts/lib/live_layer3.py`・`scripts/generate_live_pages.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #403・#688 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが直す値の表（あれば）と、第3節の欠けの理由を書く
* マージは冒頭の「マージ:」の行のとおり（規則を直さなかったときはドキュメントのみのマージ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1125-LVP-37.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1125-LVP-37 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 786c1633）: https://github.com/retroeater/mj-logs/tree/main/guide/786c1633

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/786c1633/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/786c1633/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/786c1633/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/786c1633/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/786c1633/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/786c1633/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2a1c8749.md
