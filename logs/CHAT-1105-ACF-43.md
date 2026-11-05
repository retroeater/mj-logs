# CHAT-1105-ACF-43

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1105-acf-ho
- 着手時HEAD: f0d9e1f8f3

## 指示

【Claude作成】Claude Code 向け指示：ACF 系列（#662・#552、CHAT-1104-ACF-36〜CHAT-1105-ACF-42）の振り返りと Fable 5.1 のレビューの申送り
Chat-Ref: CHAT-1105-ACF-43
マージ: ドキュメントのみ（ログ・docs/notes・docs/instruction-template.md・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-acf-ho の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1105-acf-ho を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-acf-ho origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
ACF 系列の振り返り（Sheets API の 429・#662 のキャッシュ・#552 のクローズ）と Fable 5.1 のレビューの結果を残す。機械的に確かめられる手順に落とせるものはガイド文に2行だけ足し、残りは docs/decisions/operations.md の振り返りの節に記録する。

### 決定（2026-11-05、平野さん）
- CHAT-1105-ACF-42 で docs/decisions/security.md に記録した「batchGet・Sheets API の上限の引き上げ申請・共通の同時実行の制限は今は行わない。キャッシュの後も 429 でページを飛ばしたら新しい issue で扱う」は、平野さんの決定として残す（ACF-42 の時点ではチャット側が選択肢の説明に足した方針だったが、2026-11-05 に単独の問いで確認した）
- 作業ブランチの全ページの再生成を減らす運用ルール（検証中の dry_run を変更に関わるページに絞る等）は作らない（キャッシュで全ページの再生成は約2分・読み取り105回になったため）
- `lib/net_retry.py` が再試行のときにブック ID を含む URL をジョブログに出す件は起票しない（ジョブログは private の mj の中で、公開の mj-logs へ自動で写る経路は無い。CHAT-1104-ACF-38 で確かめた）

### 前提（チャット側。平野さんの決定ではない。振り返りの中身はチャット側の案）
- 振り返りでチャット側が挙げた点（うまくいかなかったこと）:
  1. 全ページの再生成を待つ指示（ACF-37 の計測・ACF-39 のマージ後の push 検知）で、15分の待機の上限内に run が終わらず、往復が増えた（ACF-38 は表を作るためだけの指示になった）。ACF-40 では延長（合計45分）を書き、1本で済んだ
  2. ACF-36・37 の前提で、#552 の題（「fetch_tab() が再試行を通らない」）をコードを見ずに写した。8bcb3a6e2（2026-10-21）以降は当たらなかった（ACF-37 で Code が見つけた）
  3. ACF-42 の「決定」に、チャット側が選択肢の説明に足した方針を書いた（上の決定の1点目で 2026-11-05 に確認済み）
  4. 「後で伺う」と言った問い（運用ルール・net_retry の起票）を伺わずに落とした（上の決定の2・3点目で 2026-11-05 に確認済み）
  5. ACF-36 の手順に理由の無い禁止（「ワークフローは実行しない」）を書いた（CHAT-1023-ACF-35 の約束に反する。結果への影響は無し）
- Fable 5.1 のレビューで足された点:
  - ACF-40 のマージの条件 (2) が「旧い側〈キャッシュ無しのコード〉も飛ばしたページ0」を求めており、変更と無関係な 429（並走）で1回目が落ちた。「新しい側で飛ばしたページ0、両方が作ったページの差分0、旧い側が飛ばしたページは比べから外して一覧に書く」の形にできた
  - ACF-37 は出力を変えない変更で、テストの成否で合否が決まるのに「判断待ちで止まる」にしたため、マージだけの指示（ACF-39）が1本増えた。CHAT-1023-HTH-14 の決定（機械的に確かめられる直しは、条件を単独で伺って承認済みにしてよい）を使えた
- 前回までの約束の守り具合（チャット側の確かめ）:
  - CHAT-1023-ACF-35: 「測る候補を書く」は ACF-37 で守れた。「禁止の理由を書く」は ACF-36 で守れなかった（上の5）
  - CHAT-1104-PIL の (a)（写した数に出典と（要確認））は ACF-36 で守れた。(b)（条件に使う仕組みを書く前に確かめる）は compare_ref・dry_run で守れた（compare_ref は SHA で渡すと書いた）
  - CHAT-1102-AIC-15 の (a)（前提に開いた場所を添える）は守れたが、#552 の題（上の2）は開いた場所が issue の題で、実物ではなかった
  - TTL・LGN の約束は当たる場面が無かった
- ガイド文に足す2行（知見 → 選んだ層 → 理由）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 全ページの再生成を待つ指示は、並走があると15分を超え、往復が増える | ガイド文（docs/instruction-template.md の「待機の上限15分…指示文には例外だけ書く」の行に例を足す） | 待つ時間は並走で変わり、テスト・hook では決められない。例外を書くのは指示文の書き手（チャット側）なので、雛形の該当の行が効く |
| 「後で伺う」と言った問いが、返答の末尾の一覧に載らず落ちた | ガイド文（docs/notes/chat-side-operations.md「Claude Code とのやり取り」の、未完了の指示と手作業の残りを一覧で出す行に「伺うと言った問い」を足す） | チャット側の返答の形の規則で、機械的に検査できる場所が無い。既存の行に語を足すだけで、新しい項目は作らない（上限あり） |

- 足さないもの（理由）:
  - 選択肢の説明に方針を混ぜない: chat-side-operations.md「平野さんの判断とマージの許可」に既にある
  - 止まる条件が変更で制御できない要因で落ちない形にする: chat-side-operations.md「止まる条件と検証の指定」の「正しい理由で動く場合を想定して書く」に近い
  - 出力を変えない変更は条件つきで承認を伺う: CHAT-1023-HTH-14 の決定と chat-side-operations.md「平野さんの判断とマージの許可」の例外に既にある
- 約束（次の振り返りで確かめる。新しく立てず既存の約束の具体例として）: (a) 止まる条件・マージの条件に、変更で制御できない外部の要因（並走・外のデータの変化）で落ちるものを入れていないか (b) 出力を変えない変更（ログ・計測・docs）のマージを、書く前に条件つきで単独に伺ったか (c) 平野さんへの問いの選択肢に、問いの主題と別の方針を混ぜていないか

## 手順
1. 追記先の3文書（docs/instruction-template.md・docs/notes/chat-side-operations.md・docs/decisions/operations.md の末尾）の今の内容と、chat-side-operations.md の容量（上限・警告域との差。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」と chat-side-operations.md の冒頭の規則）を測る。chat-side-operations.md は純増したら同じ量を docs/notes/ の各文書へ移す規則（#492）なので、足す語の分の移し先も決める
2. 前提の表の2行を足す（規則と理由の一句だけ。事例は書かない）。instruction-template.md の例は「全ページの再生成・compare_ref の比べ・共有の lib を変えたマージ後の push 検知の再生成を待つ指示は、同じセッションで合計45分まで延長してよいと書く」の形を案とし、実物の行に合わせて直してよい。docs/decisions/security.md の #662 の節にある ACF-42 の決定の行に、上の決定の1点目（2026-11-05 に単独の問いで確認）を足す
3. docs/decisions/operations.md の末尾に「## 2026-11-05（CHAT-1105-ACF-43）」の節を足し、前提の「振り返りでチャット側が挙げた点」「Fable 5.1 のレビューで足された点」「前回までの約束の守り具合」「足したもの・足さないもの」「約束」と、上の決定の2・3点目を、直前の振り返りの節（CHAT-1104-LGN-33 など）と同じ形で書く（中身はチャット側の案と明記）

## 止まる条件
- 追記先の記述と前提が矛盾し、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張してよい。処理を報告に書く）
- chat-side-operations.md の容量が上限を超える、または移し先が決められない
- 未マージの work/ ブランチが、追記先の同じ節を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-ACF-43.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-ACF-43 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d7a17d1b）: https://github.com/retroeater/mj-logs/tree/main/guide/d7a17d1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7a17d1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7a17d1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7a17d1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7a17d1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7a17d1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d7a17d1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/04d7a070.md
