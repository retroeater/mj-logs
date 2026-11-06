# CHAT-1106-MFQ-06

- 着手日時: 2026-11-06
- 対象issue: #671
- ブランチ: work/1105-mfq
- 着手時HEAD: 8ab5b10b28

## 指示

【Claude作成】Claude Code 向け指示：title/houou/ に「鳳凰位」を入れた work/1105-mfq を cloudflare へマージし、本番を確かめる（#671） Chat-Ref: CHAT-1106-MFQ-06 マージ: 承認済み（チャットで） 貼る時機: CHAT-1105-MFQ-05 の判断待ちの後（いつでも） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 下の「作業ブランチ」の行 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-mfq の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-mfq を続けて使う（CHAT-1105-MFQ-05 の成果物をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-MFQ-05 のログの「## 報告」の状態の末尾に `/ 続き: CHAT-1106-MFQ-06` を足す（このログのコミットと同じでよい）。

目的
CHAT-1105-MFQ-05 の判断待ち3点に平野さんがすべて OK と答えたので、work/1105-mfq を cloudflare へマージし、本番の `title/houou/` を確かめる。
決定（2026-11-06、平野さん）

* マージしてよい
* 文面は MFQ-05 のプレビューのまま確定: title「鳳凰戦 歴代鳳凰位・優勝者（第1期〜第42期） | 日本プロ麻雀連盟 | ryoei.pro」、description「鳳凰戦について、歴代鳳凰位（歴代優勝者、第1期〜第42期）をまとめています。現鳳凰位は白鳥翔。」
* 同じ説明文が og:description とページ末尾の説明文（`.mj-lead`）にも出てよい（3か所を同じ文にする既存の作りのまま）

前提（チャット側。平野さんの決定ではない）

* #671 は閉じない。効果（「鳳凰位 歴代」の着地が `title/houou/` に移ったか）は次の Search Console の月次の取得の後に見る。カレンダーの予定はチャット側が作る
* MFQ-05 のログ: 成果物のコミットは c0c9a47fb（生成スクリプト・検査）・b6ae37741（再生成）・306bed8c1（docs/notes/title-pages.md）。生成物の差分は `title/houou/index.html` と `sitemap-title.xml` の lastmod だけ

手順

1. #671 の状態を確かめ（open のまま進む）、着手中のコメントを書く。origin/cloudflare を取り込み、衝突したら CLAUDE.md「ブランチ運用」（docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」）に従う。両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。取り込んだ後に `python3 -m unittest discover -s scripts/tests` を流し、取り込みで `scripts/generate_title_pages.py` が変わっていれば title_pages を再生成して差分を確かめる
2. CLAUDE.md「ブランチ運用」のとおりマージする（push 直前に再fetchし `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1105-mfq:cloudflare`）。マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗を分けて扱う。無関係なら原因を報告に書いたうえで残りの手順を進めてよい
3. 本番（https://ryoei.pro/title/houou/、UA に mj-internal）の `<title>`・meta description・og:description・`.mj-lead` が決定の文面になったこと、`title/judan/`・`title/oui/` が今のまま（「歴代優勝者」「現タイトルホルダー」）であることを確かめる。check-runs の成功だけで本番反映とは書かない（CLAUDE.md）
4. #671 にマージと本番の確かめのコメントを書く（閉じない。効果は次の Search Console の取得の後に見る、と書く）。末尾に `Chat-Ref: CHAT-1106-MFQ-06`。ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する

完了条件: 最終報告の最後の行が `Chat-Ref: CHAT-1106-MFQ-06`

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 599356aa）: https://github.com/retroeater/mj-logs/tree/main/guide/599356aa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/599356aa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/599356aa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/599356aa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/599356aa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/599356aa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/599356aa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b9d20fc0.md
