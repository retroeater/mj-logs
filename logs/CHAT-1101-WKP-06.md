# CHAT-1101-WKP-06

- 着手日時: 2026-11-01
- 対象issue: #564
- ブランチ: work/1101-wkp
- 着手時HEAD: a265aa1ab

## 指示

【Claude作成】Claude Code 向け指示：WKP-05 の続き（run-daily の sns / update の失敗を無関係として扱い、run-monthly の確かめとマージへ進む）
Chat-Ref: CHAT-1101-WKP-06
マージ: 承認済み（チャットで）。条件は「止まる条件」のとおりで、1つでも当たればマージせずに止まる
貼る時機: CHAT-1101-WKP-05 が判断待ちで止まった後（WKP の同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-wkp を続けて使う（WKP-05 の成果物 f43ebab5e・a7370d27c を続けるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行。例: work/1031-pro-sched がマージ済みなら docs/notes/github-actions.md の update-live-channel の行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-WKP-05 のログの `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、WKP-05 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1101-WKP-06` を足す。

## 目的
CHAT-1101-WKP-05 の判断待ちへの回答。止まった手順の残り（手順2 (b) の run-monthly の確かめ・仕上げ・マージ）を行う。

### 決定（2026-11-01、平野さん）
- 作業ブランチの run-daily の手動実行（41252379802）の失敗は、今回の変更と無関係として扱い、先へ進む。シートの名前の重複は別に直す
- マージは、run-monthly の作業ブランチでの確かめ（redirects の push が再試行で通ること）が success で、ほかの止まる条件に当たらなければ行ってよい

### 前提（チャット側。平野さんの決定ではない）
- WKP-05 の止まる条件「手順2・3の手動実行が success にならない」の例外に、上の決定の失敗を足す形。例外の範囲は「止まる条件」に書いた（落ちたのが sns / update だけで、理由が同じ重複の行のとき）
- WKP-05 の指示文の手順2 (b)・手順3の残り（決定の記録・#564 へのコメント・マージ）と止まる条件は、この指示で書き直したところのほかはそのまま有効
- fetch-redirect-counts の 2026-11-01 のフォールバック実行は、WKP-05 の着手時（08:18 JST）には未実行だった。着手時にもう一度確かめる（要確認）
- この番号（WKP-06）は、前に 12/1 の「月次2本の schedule を外す指示」に予定していた番号。そちらは WKP-07 以降に送る（チャット側でカレンダーを直す）

## 手順
1. fetch-redirect-counts.yml の 2026-11-01 のフォールバック実行の有無・結論・run ID と、origin/cloudflare の docs/redirect-counts/2026-10.csv の有無を確かめ直す（待たない）。`git branch -r --no-merged origin/cloudflare` で、WKP-05 の後に増えたブランチを含めて重なりを見直す
2. WKP-05 の手順2 (b) を行う（run-monthly を `scheduled` 真で作業ブランチで起動し、issue へのコメントなど作業ブランチの外への書き込みを止める行だけを入れた一時のコミットで、gsc と redirects の2つのコミットが作業ブランチに入り、redirects の push が再試行で通ることを確かめる。そのあと、一時のコミットを戻すコミットの差分が止めた行だけであること、作業ブランチに入ったデータのコミットを revert してマージの差分に出ないことを確かめる。より軽い確かめ方で同じことが言えるなら、それでよい〈理由をログに〉）
3. 仕上げとマージ。決定（WKP-05・WKP-06 の両方）を docs/decisions/automation.md に足し、#564 に結果をコメントする（MZA-01 のコメントへの返事を兼ねる。文面は WKP-05・WKP-06 のログの `## 経過` から引用する。シートの名前の重複で毎朝の sns / update が止まっていることも事実として書く。名前は書かない）。止まる条件に当たらなければ cloudflare へマージする

## 止まる条件
- WKP-05 のログの `## 報告` の状態が「判断待ち」でない
- 手順2の run-monthly の作業ブランチでの確かめが success にならない、または redirects の push が再試行で通ったことをログで示せない（外のサイト・API の一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- 手順2で、作業ブランチの外（cloudflare・issue）への書き込みが起きた、または起きそうで止められない
- 取り込みの後にもう一度手動実行するとき、run-daily で落ちたのが sns / update のほかにある、または sns / update が「「プロ」「連盟プロ以外」で名前が重複しています」のほかの理由で落ちた（sns / update がその理由だけで落ちたときは止まらない）
- 未マージの work/ ブランチが、この作業で変えるワークフローの同じ行・同じジョブを変えている、または取り込みで衝突する（両立する文書の衝突は上の作業ブランチの行のとおり解いてよい。同じファイルの別の箇所の変更では止まらず、ログに書く）
- マージの差分に、WKP-05 の止まる条件に書いた種類のほかが出る。データ（docs/gsc・docs/redirect-counts など）の差分は 0 であること
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージの後の check-run の失敗は、今回の変更によるものなら止まり、無関係なら原因をログに書いて残りの手順（#564 へのコメント）を進めてよい。自分の変更で落ちると分かっているテストは直してよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-WKP-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-WKP-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1101-wkp/docs/logs/CHAT-1101-WKP-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-wkp
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d750c1c8）: https://github.com/retroeater/mj-logs/tree/main/guide/d750c1c8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
