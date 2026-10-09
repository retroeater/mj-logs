# CHAT-1009-NEN-14

- 着手日時: 2026-10-09
- 対象issue: なし
- ブランチ: work/1009-nen-memo
- 着手時HEAD: 1ddf8321

## 指示

【Claude作成】Claude Code 向け指示：NEN のチャット（#277）の申送り。指示文の止まる条件の書き方2点と、新しいページの grill の最初の問いを文書に足す Chat-Ref: CHAT-1009-NEN-14 マージ: ドキュメントのみ（docs/ と docs/logs・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-nen-memo の作成と push、cloudflare へのマージ（docs のみ）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1009-nen-memo を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-nen-memo origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#277 の作業（CHAT-1008-NEN-01〜CHAT-1009-NEN-13）で、チャット側の指示文の書き方による手戻りが3種類あった。機械的に確かめられる規則として文書に残す（事例の詳細は書かない）。
決定（2026-10-09、平野さん）

* 振り返りの提案のうち、次の3点を申送りとして文書に書き残す

前提（チャット側。平野さんの決定ではない）

* 書く規則（文面は追記先に合わせて短くしてよい。規則と理由の一句だけ）:
   1. 新しいページ・機能の grill は、最初の問いを「目的（誰が何をしに来るか）」と「既存のページの拡張で足りないか」にする。 issue の本文が書いている形（例「年表」）から論点を作らない（#277 は形から詰めて試作を6本重ね、目的を確かめた後に入口の拡張へ変えた）。書き先の候補: docs/new-page-checklist.md「作る段の確かめ」の先頭
   2. 未マージの `work/` ブランチとの重なりで止まる条件は、ファイル単位ではなく「同じ行・同じ関数を変えている、または取り込みで衝突する」の形で書く。 ファイルが同じでも行が離れた変更で止まると往復が増える（#277 で2回）。書き先の候補: docs/instruction-template.md の注意の箇条書き（「止まる条件」の書き方の近く）
   3. マージ後の check-run の失敗で止める条件は、「今回の変更による失敗」と「無関係な失敗」を分けて書く。 無関係な失敗（例: 別のページのデータ）なら、原因を報告に書いたうえで残りの手順（本番の確かめ・issue のクローズ）を進めてよい、とする（#277 で2回止まった）。あわせて、「自分の変更で落ちると分かっているテスト」は直してよいと書く（同じく1回止まった）。書き先の候補: docs/instruction-template.md の注意の箇条書き
* 追記の前に追記先の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張でよい（docs/instruction-template.md の注意書き）。chat-side-operations.md に書く場合は警告域（26,624 バイト）の外であることを確かめる。追記先の残り容量（追記の前後のバイト数）を報告に書く
* docs/decisions/operations.md に上の「決定」を「2026-10-09（CHAT-1009-NEN-14）」として足す

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。検索語に「止まる条件」「未マージ」「grill」「目的」「check-run」を入れる）、あれば止まって報告する
2. 追記先を読み、上の3点を書く。容量を測る
3. マージする（CLAUDE.md「ブランチ運用」）。結果をログに書く

止まる条件

* 同じ論点の open issue がある
* 追記先に、上の規則と矛盾していてどちらが正か判断が要る記述がある（同じ趣旨なら止まらず置き換え・拡張する）
* chat-side-operations.md が警告域に入る
* docs 以外を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、書いた場所と文面、追記先の前後のバイト数を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-NEN-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-NEN-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。このセッションは NEN-01〜13 から続けて受けたもの
- 識別子: `git log --all --grep="CHAT-1009-NEN-14"` は0件
- ブランチ: `work/1009-nen-memo` はローカル・リモートとも無かったため `git checkout -b work/1009-nen-memo origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1009-nen-memo
- ログ: https://github.com/retroeater/mj/blob/work/1009-nen-memo/docs/logs/CHAT-1009-NEN-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-nen-memo
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f6f2125c）: https://github.com/retroeater/mj-logs/tree/main/guide/f6f2125c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eabe7134.md
