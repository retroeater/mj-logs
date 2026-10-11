# CHAT-1011-RDN-14

- 着手日時: 2026-10-11
- 対象issue: #536（起票する2件）
- ブランチ: work/1011-rdn-issues
- 着手時HEAD: 85c264ee

## 指示

【Claude作成】Claude Code 向け指示：#536 のクローズ時に残った作業を issue 2件に起票する（成績8列の定義の見直し、Ampai の URL の複数期の管理と通知）
Chat-Ref: CHAT-1011-RDN-14
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1011-RDN-13 と並行してよい。別のブランチ）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-rdn-issues の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1011-rdn-issues を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-rdn-issues origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#536（「プロ」シートの冗長な列の廃止）は 2026-10-11 に閉じたが、クローズの時点で残る作業を別の issue に起票していなかった（CLAUDE.md・chat-side-operations.md の決まり）。2件を起票する。コードは変えない。

### 決定（2026-10-11、平野さん）
- 成績8列の定義は「まず今の数式のまま移す」とし、見直しは列を消した後に別の指示で行う（2026-10-10 の決定。`docs/decisions/pros.md`）→ issue にする
- 「鳳凰Ampai」「桜花Ampai」タブを、複数の期にわたってすべての URL を管理する形に変え、選手の期・リーグ・組に基づいて適切な URL を選ぶ。あわせて、新しい期が始まって Ampai の URL が無いときは知らせる → issue にする

### 前提（チャット側。平野さんの決定ではない）
- 8列の今の定義と所見は CHAT-1010-RDN-01 のログ `## 経過`「手順2」の数式・「手順5」の所見、実装は CHAT-1010-RDN-07 のログ（`lib/` の集計の関数。名前は実物で確かめる）。見直す点は RDN-01 の所見の3つ: (1) 鳳凰出場は表示 Y の期だけ・桜花出場は進行中の期も数える違いを揃えるか、(2) 最強出場は卓の数のままか年度の数にするか、(3) 放送対局の集計元を旧「対局」タブの部分一致のままにするか /live の層に替えるか（替えると約400名の値が変わる。リンク先 `video_live.html` も）
- Ampai の今の形は RDN-07 のログ: 「鳳凰Ampai」「桜花Ampai」タブの先頭の列の見出しが期の名前（「鳳凰43後」「桜花21」）で、名前で XLOOKUP。生成は最新の期と見出しを照合し、合わなければリンクを付けないだけで知らせない（RDN-07 の実装）。複数期に対応するなら、タブの形（期・リーグ・組・URL の列）と、新しい期で URL が無いときの知らせ先（常設 issue か生成ログか）を issue の本文で決める項目にする

## 手順
1. 同じ主題の issue をクローズ済みも含めて検索し（「鳳凰出場」「桜花出場」「放送対局 集計」「Ampai」「鳳凰Ampai」）、あれば起票せずにその issue へのコメントに替える（どちらにしたかを報告に書く）。
2. 起票1「jpml_pros の成績8列の定義を見直す（#536 の残作業）」: 本文に上の3つの見直し点、今の定義の参照先（RDN-01・RDN-07 のログの URL と節）、決めることの一覧。ラベルは `分野: 整理・保守`・`対象: jpml_pros`。
3. 起票2「鳳凰Ampai・桜花Ampai の URL を複数期で管理し、新しい期で URL が無ければ知らせる」: 本文に上の決定、今の形、タブの形の案を決める項目として書く。ラベルは `分野: 自動化`・`対象: jpml_pros`。両方の issue のコメント末尾に Chat-Ref の行を書き、#536 に「残作業を #<番号>・#<番号> に起票した」とコメントする。

## 止まる条件
- 同じ主題の open の issue があり、起票かコメントかを決められない（候補を書いて止まる）
- 変更が `docs/logs/`・`docs/decisions/` の外に及びそうになった

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ログのみ）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-RDN-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-RDN-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き
- ブランチ: work/1011-rdn-issues はローカル・リモートとも無し。`git checkout -b work/1011-rdn-issues origin/cloudflare`（85c264ee）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致
- 1. 検索（クローズ済みを含む）: 「鳳凰出場 桜花出場 最強出場」「放送対局 集計」「鳳凰Ampai 桜花Ampai」「Ampai」「jpml_pros 成績の列 見直し」。同じ主題の issue は無し（近いのは #416〈列見出しの略語に凡例〉で主題が違う）→ 2件とも起票（コメントへの代替は無し）
- 2. 起票1: #547「jpml_pros の成績8列の定義を見直す（#536 の残作業）」。ラベル `分野: 整理・保守`・`対象: jpml_pros`。本文に見直す点3つ・参照先（RDN-01 の「手順2」「手順5」、RDN-07 と `scripts/lib/pro_stats.py`・`add_stats()`）・決めることの一覧、末尾に Chat-Ref
- 3. 起票2: #548「鳳凰Ampai・桜花Ampai の URL を複数期で管理し、新しい期で URL が無ければ知らせる」。ラベル `分野: 自動化`・`対象: jpml_pros`。本文に決定・今の形・決めること（タブの形の案〈期・リーグ・組・URL〉、知らせ先〈常設 issue か生成ログか〉ほか）、末尾に Chat-Ref
- #536 に「残作業を #547・#548 に起票した」とコメント（末尾に Chat-Ref）
- `docs/decisions/pros.md` に RDN-14 の決定を追記

## 報告

- 状態: 完了
- ブランチ: work/1011-rdn-issues
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1011-RDN-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-rdn-issues
- 確認用URL: なし
- マージ: cloudflare へマージ（ドキュメントのみ）
- issue: #547・#548 を起票（同じ主題の既存 issue は無く、コメントへの代替はしていない）。#536 にコメント
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d43b0f91）: https://github.com/retroeater/mj-logs/tree/main/guide/d43b0f91

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
