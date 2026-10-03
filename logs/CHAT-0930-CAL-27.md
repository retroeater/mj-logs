# CHAT-0930-CAL-27

- 着手日時: 2026-10-03 12:44（JST）
- 対象issue: なし
- ブランチ: work/1003-cal-mos
- 着手時HEAD: d92e94c5（origin/work/1003-cal-mos と同じ。d26166bc を含む）

## 指示

【Claude作成】Claude Code 向け指示：チャット側の手順書の「ブランチ名を具体的に書かない」の行を直し、申送り（CAL-25、work/1003-cal-mos）と合わせて cloudflare へ入れる Chat-Ref: CHAT-0930-CAL-27 マージ: 承認済み（チャットで、2026-10-03。work/1003-cal-mos を cloudflare へ。下の止まる条件に当たらない限り、確認を求めずに進めてよい） 貼る時機: いつでも（CAL-26 と並行で可。触るのは文書だけ） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1003-cal-mos を続けて使う（CHAT-0930-CAL-25 のコミット d26166bc があるため）。`git checkout -b work/1003-cal-mos origin/work/1003-cal-mos` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無い、または d26166bc を含まなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-25 のログの `## 報告` が「判断待ち」であることを確かめ、状態を「判断待ち → 続き: CHAT-0930-CAL-27」と直す。

目的
CAL-25 の報告の「食い違いの判断」を片付け、申送りの追記を本番に入れる。
決定（2026-10-03、平野さん）

* CAL-25 の差分（雛形・チャット側の手順書・handover）のとおりでマージしてよい。
* docs/notes/chat-side-operations.md「指示文の書き方・渡し方」の「ブランチ名を具体的に書かない。…」の行は、前半だけを「作業ブランチ名は書く（クラウドセッションでは `work/<…>` を指定する）」の趣旨に直す。後半（未マージのブランチとの重なりは名指しせず、`git branch -r --no-merged origin/cloudflare` で一覧を出させてから確かめさせる。既存ブランチを続ける指示は雛形の「作業ブランチ」の行）は今のまま残す。

手順

1. 直し: 上の決定どおりにその行を直す。ほかに「ブランチ名を書かない」趣旨の記述が CLAUDE.md・docs/instruction-template.md・docs/notes/cloud-sessions.md にあれば挙げ、同じ趣旨に直す（無ければ「無い」と書く）。文書の大きさの検査（あれば）を通す。差分をログに貼る。
2. マージ: 差分が CAL-25 のものと手順1の直しとログ・決定の記録のほかに無いことを確かめて、CLAUDE.md の手順どおり cloudflare へ入れる。写しの後、mj-logs の新しい `guide/<SHA>/` の instruction-template.md・chat-side-operations.md・handover.md に追記と直しが入ったことを確かめる。

止まる条件

* CAL-25 の `## 報告` が「判断待ち」でない。
* 同じ文書を触る未マージのブランチがある、またはマージで衝突する。
* 手順1で、直す記述が CLAUDE.md にあり、その規則の意味が変わる（CLAUDE.md は直さずに止まって報告する）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。決定は CLAUDE.md のとおり `docs/decisions/operations.md` に足す。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-27.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-27` は0件。ローカルの work/1003-cal-mos は origin/work/1003-cal-mos（d92e94c5、d26166bc を含む）と同じ
- 手順0: 指示欄の末尾は指示文の最後の行と一致。CAL-25 の `## 報告` は「判断待ち（追記まで。マージは未承認）」だったので「判断待ち → 続き: CHAT-0930-CAL-27」に直した（このコミットに含める）

## 報告

- 状態: 作業中
- ブランチ: work/1003-cal-mos
- ログ: https://github.com/retroeater/mj/blob/work/1003-cal-mos/docs/logs/CHAT-0930-CAL-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-cal-mos
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cf0f7e27）: https://github.com/retroeater/mj-logs/tree/main/guide/cf0f7e27

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0384cc68.md
