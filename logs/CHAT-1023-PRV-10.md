# CHAT-1023-PRV-10

- 着手日時: 2026-10-23
- 対象issue: #557
- ブランチ: work/1023-prv
- 着手時HEAD: c49be44e0

## 指示

【Claude作成】Claude Code 向け指示：SRG・PRV 系列の振り返り・レビューの申送り（古くなった `--check` の手順の直し、ブックの廃止の確かめ、シートの ID の検知の調べ）
Chat-Ref: CHAT-1023-PRV-10
マージ: docs のみ（docs/decisions/・docs/logs/ を含む）の変更なので、完了報告のうえ cloudflare へ入れてよい。手順3の検知の仕組みは作らず、案だけを書く
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1023-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
SRG 系列（CHAT-1018-SRG-01〜CHAT-1023-SRG-06）と PRV 系列（CHAT-1018-PRV-01〜CHAT-1022-PRV-09）の振り返りと Fable 5.1 のレビュー（2026-10-23、チャット）の申送り。機械的に確かめられる手順に落とせるものだけを書き残す。

### 決定（2026-10-23、平野さん）
- 申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案）

### 前提（チャット側。平野さんの決定ではない）
知見と、選んだ層・理由（docs/notes/chat-routines.md「申送り」の観点）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| A. 2026-10-21 の Sheets API への切り替え（PRV-06）の後、Code の環境には鍵が無く `python3 scripts/regenerate.py --check` を直接回せない。docs/notes/chat-routines.md「指示の種類ごとの確かめ」の「データが変わった後にページを生成する指示では、手順1を `python3 scripts/regenerate.py --check <ページ>`」と、docs/notes/static-generation.md「平野さんがシート（タブ）を用意した・直したと言ったとき」の同じ記述が古い（チャット側が ee47bdbe のガイドで確かめた。行番号は要確認） | ガイド文（置き換え。純増しない） | 規則の追加ではなく、今の手段（`regenerate-page.yml` の check・dry_run を作業ブランチの ref で手動実行する）への書き換え。センサーにする対象ではない |
| B. Drive でブックを削除してもゴミ箱にある間は、共有を受けたサービスアカウント（sheets-reader）から読める（CHAT-1023-SRG-05 で中断）。廃止は「ゴミ箱からも完全に削除」まで | 推論的センサー（Code の手順） | 「シート・スプレッドシートを…廃止すると言われたら」の手順に、「廃止の確かめは sheets-reader で読めない（404・403）ことを手動実行で確かめる。読めたら止めて、ゴミ箱からの完全な削除を頼む」を足す。SRG-05・06 で実際に検知できた確かめ方 |
| C. チャット側の指示文にシートの ID を書き、public の mj-logs のログに写った（CHAT-1018-SRG-01 のログに新旧の ID。chat-side-operations.md「指示文はログの『指示』欄にそのまま写り、public の mj-logs に出る…シートの ID・URL は書かない」に反した） | 計算的センサー（案だけ。この指示では作らない） | 規則はすでにあり、守れなかった。規則を足すより、ログを mj-logs へ写す前・`security-check.yml` の走査でスプレッドシートの ID の形（44文字）を検知するほうが効く見込み。仕組みを変えるので、案を出して平野さんの判断を待つ |

書かないもの（抽象的な心得で、機械的に確かめられない）: 外部サービスの仕組みを公式ドキュメントで確かめずに断定した件（chat-routines.md「外部サービスの設定を頼むとき」に既にある）、本番のシートで確かめようとした件、移動を頼むときに外すものを挙げなかった件、止まる条件が厳しすぎた件、英語で返信した件。

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確かめる。「regenerate.py --check」「ゴミ箱」「完全に削除」「スプレッドシートの ID 検知」等）、あれば止まって報告する。#557 に着手中のコメントを残す。追記先（chat-routines.md・static-generation.md）の今の内容と、上限・警告域との差を測ってログに書く。
2. A と B を書く。A は、chat-routines.md と static-generation.md の `python3 scripts/regenerate.py --check` を Code の環境で回す前提の記述を、今の手段に置き換える（ほかの文書に同じ前提の記述があれば一覧にして同じく直す。`git grep -n "regenerate.py --check"` でリポジトリ全体を範囲にし、docs/decisions/・docs/logs/ と、スクリプトの説明そのもの〈`--check` の機能の説明〉は除く）。B は、static-generation.md「タブ・列を変えた、シート・スプレッドシートを移した・消した・廃止すると言われたら」の行を拡張する（規則と理由の一句だけ。事例は書かない）。純増したら、同じ量を docs/notes/ の各文書へ移す規則（chat-side-operations.md 冒頭）に従う。
3. C の調べ: `security-check.yml`（#300 の代替の履歴の走査）と、ログを mj-logs へ写す仕組み（mj-logs 側の `sync-from-mj.yml` など）が、スプレッドシートの ID の形を検知するかを確かめる。検知しないなら、足すときの案（どこに足すか、ID の形の正規表現、誤検知〈YouTube の ID・カレンダーの ID など〉の扱い、見つけたときに止めるか伏せるか）をメリット・デメリットと推奨つきで「判断が必要なこと」に書く。仕組みは作らない。docs/decisions/operations.md に、この申送りの節（上の決定と、A・B を書いたこと、C は案を出して判断待ち）を足す。cloudflare へマージする。

## 止まる条件
- 同じ論点の open issue がある
- 追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めずに置き換え・拡張してよい。処理を報告に書く）
- 差分に docs 以外の変更が含まれる
- 未マージの work/ ブランチが、変える文書の同じ行・同じ節を変えている、または取り込みで衝突する。表・追記の続く文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-PRV-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-PRV-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1023-PRV-10` のコミットは 0 件。`PRV` は同じセッションの PRV-09 の続き
- 作業ブランチ: `work/1023-prv` はローカル・リモートとも無く、origin/cloudflare（c49be44e0）から作った

## 報告

- 状態: 作業中
- ブランチ: work/1023-prv
- ログ: https://github.com/retroeater/mj/blob/work/1023-prv/docs/logs/CHAT-1023-PRV-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-prv
- 確認用URL: なし
- マージ: 未
- issue: #557
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c49be44e）: https://github.com/retroeater/mj-logs/tree/main/guide/c49be44e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
