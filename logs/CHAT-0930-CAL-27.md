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

### 手順1: 直し

- `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」の先頭の行の前半を直した
  - 「ブランチ名を具体的に書かない。『ブランチ運用』の規則どおりと書く」を「作業ブランチ名は書く（クラウドセッションでは `work/<…>` を指定する。文面は雛形の『作業ブランチ』の行）」にした
  - 後半（重なりは名指しせず `git branch -r --no-merged origin/cloudflare` で一覧を出させる、既存ブランチを続ける指示は雛形の行）はそのまま
- ほかの同じ趣旨の記述:
  - **`docs/instruction-template.md` の注意書きの先頭「作業ブランチ名は具体名を書かず『ブランチ運用』の規則どおりとする」**: 同じ趣旨に直した
  - CLAUDE.md: 無い（「ブランチ運用」節は受け手側の規則で、指示文に書くかどうかは書いていない）
  - docs/notes/cloud-sessions.md: 無い
- 同じ文書を触るほかの未マージのブランチ: 無い（一覧に出たのはこのブランチ自身だけ）
- 大きさ: chat-side-operations.md 23,654（警告域 26,624・上限 28,672）、instruction-template.md 12,288（検査の対象外）。`check_asset_limits.py` OK
- 決定の記録: `docs/decisions/operations.md` に足した。CAL-25 の「未マージ」に済の印を付けた
- 差分:

```diff
diff --git a/docs/instruction-template.md b/docs/instruction-template.md
index 2ddfd197..728fe352 100644
--- a/docs/instruction-template.md
+++ b/docs/instruction-template.md
@@ -2,7 +2,7 @@
 
 チャット側（claude.ai）が Claude Code へ渡す指示文の骨組み（#294）。受け手側のルールは CLAUDE.md が正で、ここには複製しない。
 
-- 作業ブランチ名は具体名を書かず「『ブランチ運用』の規則どおり」とする
+- 作業ブランチ名は書く（クラウドセッションでは `work/<…>` を指定する。下の「作業ブランチ」の行）
 - 既存の作業ブランチを続けて使う指示は、「Chat-Ref」の次に「作業ブランチ: origin/cloudflare を起点に切った既存の work/<識別子> を続けて使う（〜のため）。着手時と作業中に origin/cloudflare が進んでいたら merge で取り込んでよい（push 済みなので rebase しない）」の1行を入れる。取り込みの可否を書かないと、祖先確認だけで中断する
 - クラウドセッション（Claude Code on the web）で実行する指示は、`/workspaces/mj` と worktree が無いため「作業ブランチ」の行を次のどちらかにする（読み替えは docs/notes/cloud-sessions.md）。判定の向きが2つで逆なので、式をそのまま書く
   - 新しく作る: 「作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる」
diff --git a/docs/notes/chat-side-operations.md b/docs/notes/chat-side-operations.md
index d361e5c1..d1602c50 100644
--- a/docs/notes/chat-side-operations.md
+++ b/docs/notes/chat-side-operations.md
@@ -121,7 +121,7 @@ Claude Code 側で確認できる範囲は `docs/notes/cloudflare.md`「ビル
 
 ### 指示文の書き方・渡し方
 
-- **ブランチ名を具体的に書かない。**「『ブランチ運用』の規則どおり」と書く。未マージのブランチとの重なりも名指しせず、
+- **作業ブランチ名は書く**（クラウドセッションでは `work/<…>` を指定する。文面は `docs/instruction-template.md`「作業ブランチ」の行）。未マージのブランチとの重なりは名指しせず、
   `git branch -r --no-merged origin/cloudflare` で一覧を出させてから確かめさせる（書いた後に増えたブランチを見落とさないため）。
   既存ブランチを続ける指示は `docs/instruction-template.md`「作業ブランチ」の行
 - **コミットSHAを固定して書かない。** 土台や比較対象は「その時点の`origin/cloudflare`」と書く。既存の特定コミットの参照（`git show <sha>:<path>` など）は書いてよい
```

### 手順2: マージ（衝突で止めた）

- 手順1のコミット（直し・決定の記録・ログ）を work/1003-cal-mos へ push した（75f9a066）
- push の前に `git merge origin/cloudflare` を実行したところ、**`docs/decisions/operations.md` で衝突した**
  - origin/cloudflare に、並行の指示 CHAT-1003-INV-05 のコミット（a6097335 `docs: add rules from the open issue inventory`・8f3bcfe5）が入っていた
  - INV-05 も operations.md の末尾に「## 2026-10-03（CHAT-1003-INV-05）」を足していて、CAL-25・CAL-27 の見出しと同じ場所で重なった
  - `docs/instruction-template.md`・`docs/notes/chat-side-operations.md`（INV-05 も追記している）・`cloud-sessions.md`・`handover-archive-2026.md` は、自動で合わさった
- 止まる条件「マージで衝突する」に当たるため、`git merge --abort` で取り込みをやめた。cloudflare へは入れていない
  - 作業ブランチは 75f9a066 のまま（衝突を解いたコミットは無い）
- 衝突の中身: どちらも末尾への追記で、内容は食い違わない。両方の見出しを並べれば解ける（日付の古い順の決まりでは、どちらも 2026-10-03）
- 同じ文書（雛形・chat-side）を触る未マージのブランチは、手順1の時点では無かった。INV-05 は手順1の後に cloudflare へ直接マージされた
## 報告

- 状態: 判断待ち → 続き: CHAT-0930-CAL-28
- ブランチ: work/1003-cal-mos（未マージ。75f9a066）
- ログ: https://github.com/retroeater/mj/blob/work/1003-cal-mos/docs/logs/CHAT-0930-CAL-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-cal-mos
- 確認用URL: なし
- マージ: 未（衝突で止めた）
- issue: なし
- 判断が必要なこと:
  - **衝突の解き方**: `docs/decisions/operations.md` の末尾で、CAL-25・CAL-27 の決定の見出しと、INV-05 の見出し（「INV の振り返りの申送り」）が重なった
    - どちらも追記だけで食い違わないので、両方を残して並べれば解ける
    - 解いてマージしてよいかを決めてほしい（次の指示で「衝突は operations.md の末尾の追記同士なら両方残して解いてよい」と書けば進める）
  - INV-05 は雛形・chat-side にも規則を足している（自動で合わさった）。CAL-25 の規則と重なる・食い違うかは、まだ読み比べていない
    - 次の指示で、取り込みの後の雛形・chat-side を読み比べる手順を入れるとよい
  - 手順1の直し（「作業ブランチ名は書く」）は作業ブランチにある（chat-side・雛形の2か所）
- 未確認の項目: INV-05 の追記と CAL-25・CAL-27 の追記の重なり（取り込みをやめたため、合わせた後の文書は読んでいない）
- エラー: なし（衝突は止まる条件に当たったため取り込みをやめた）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 77c35579）: https://github.com/retroeater/mj-logs/tree/main/guide/77c35579

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d7dac40b.md
