# CHAT-1107-LGV-16

- 着手日時: 2026-11-07
- 対象issue: #636（参照）
- ブランチ: work/1107-lgv-l3
- 着手時HEAD: 24631cfff

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の【3】の案を8列（「日付」を足す）で作り直し、【2】の「表示」と案のタブを書く（CHAT-1107-LGV-15 の続き）
Chat-Ref: CHAT-1107-LGV-16
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの mode は戻してからマージし、差分を docs だけにする）。【3】の差し替えは平野さんが案を見てから行う（この指示では差し替えない）
貼る時機: いつでも（CHAT-1107-LGV-15 の続き。LGV-15 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-l3 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1107-lgv-l3 を続けて使う（CHAT-1107-LGV-15 の一度きりの mode `l3plan` があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行。docs/decisions/*.md の末尾の節どうしを含む）は、両方を残して日付順に解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1107-LGV-15 のログの `## 報告` が「中断」で、work/1107-lgv-l3 に mode `l3plan` があることを確かめ、そうでなければ何もせずに止まる。あわせて LGV-15 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1107-LGV-16` を足す。

## 目的
CHAT-1107-LGV-15 は、7列の案で「日付」が12件変わり、それに伴って別名の並びが2件変わったため、止まる条件に当たって中断した（シートは書いていない）。【3】の列に「日付」を足した8列で案を作り直し、残りを LGV-15 の指示のとおりに進める。

### 決定（2026-11-07、平野さん）
- 【3】の列は「投稿ID」「表示」「判定」「駅」「店名」「メニュー」「place_id」「日付」の8つにする（LGV-15 の7列に「日付」を足す。案a）。日付が変わる12件・別名の並びの2件は、これで起きない
- そのほかは CHAT-1107-LGV-15 の「決定」のまま（【2】の古い行に今の規則を当て直す、案は別のタブに書いて平野さんが差し替える、「店URL」は持たずリンクが無くなる3件は issue でフォローアップ、#636 の (b) 23 は同じ issue に記録）

### 前提（チャット側。平野さんの決定ではない）
- LGV-15 の dry-run（run 73）の数: (a) N→Y 2,030・(b) 新しく載る行 0・(c) 案 1,840行（7列）・(d) 違いは店URL（検索の形へ 1,978、リンクが無くなる3件）と日付12件・別名2件。8列にすると、(c) は日付だけを補正している行の分（12件前後）増え、(d) の日付と別名の違いは0になる見込み
- 一度きりの mode の作り・シートに書く範囲・確かめ方・フォローアップの issue・止まる条件は CHAT-1107-LGV-15 の指示の「前提」「止まる条件」のまま（案のタブの見出しが8列になる以外）。フォローアップの issue の本文は、(1) リンクが無くなる3件（LGV-15 のログの表: 1615197432467566592 牡蠣屋うらら・1772580856504472001 為セバ成ル・1845395661509824631 かめや）と (3) #636 の (b) 23 の2つにする（(2) の日付は無くなる）

## 手順
1. `l3plan` の案の列に「日付」を足す。dry_run 真で手動実行し、(a)〜(d) をログに書く（LGV-15 の run 73 との差も書く）
2. (d) の違いが店URL だけ（検索の形に替わるものとリンクが無くなる3件）で、(b) が0件なら、dry_run 偽（`--apply`）で手動実行し、【2】の「表示」のセル（N→Y）と「【3】手動補正（案）」のタブを書く。N→Y にした投稿 ID の全件をログに残す。書いた後にもう一度 dry_run 真で回し、(a) が0件、(d) が手順1と同じことを確かめる
3. フォローアップの issue を作り（LGV-15 の前提のラベル）、#636 にこの issue へのコメントを足す。一度きりの mode を戻すコミットで消し、`git diff origin/cloudflare -- scripts/ .github/` が空であることを確かめてから、ログを cloudflare へマージする。判断待ちで止まる（平野さんが案のタブを見て差し替える）

## 止まる条件
- 手順1の (b) が1件以上、または (d) に店URL 以外の違いがある（全件を表にして止まる。【2】は書かない）
- 「【3】手動補正（案）」という名前のタブが既にある
- 一度きりの mode の実行が失敗する（一時的な接続の失敗は1回だけやり直してよい）。書き込みの途中で失敗したときは、書いたところまでをログに書いて止まる（やり直さない）
- 未マージの work/ ブランチが `convert_x_posts.py`・`convert-x-posts.yml` の同じ箇所を変えている、または取り込みで衝突する
- 戻した後の `git diff origin/cloudflare -- scripts/ .github/` が空でない
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが案のタブを見て差し替える手順（今の【3】を「【3】手動補正（旧）」に、案を「【3】手動補正」に名前を変える）と、差し替えた後に確かめる指示が要ることを書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1107-lgv-l3
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-l3/docs/logs/CHAT-1107-LGV-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-l3
- 確認用URL: なし
- マージ: 未
- issue: #636（参照）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a2e78691）: https://github.com/retroeater/mj-logs/tree/main/guide/a2e78691

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
