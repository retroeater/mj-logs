# CHAT-1109-AIC-43

- 着手日時: 2026-11-09（JST）
- 対象issue: #654
- ブランチ: work/1109-aic
- 着手時HEAD: 791d381ac

## 指示

【Claude作成】Claude Code 向け指示：（AIC 系列の振り返り、その1）ガイド文の2か所を直す。レビューの往復2回の後に足した直しはマージしない（docs/notes/self-review.md）。指示文の前提にコードの振る舞いを書くときは関数名と行番号を添える（docs/notes/chat-side-operations.md）。docs のみ
Chat-Ref: CHAT-1109-AIC-43
マージ: ドキュメントのみの変更なので、完了を報告のうえ cloudflare へマージしてよい（CLAUDE.md「ブランチ運用」）
貼る時機: いつでも（CHAT-1109-AIC-42 のマージ〈e01a391cf〉は済んでいる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1109-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1109-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。e01a391cf が origin/cloudflare の祖先であることを確かめる。AIC-42 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1109-AIC-43` を足す（AIC-42 に判断の残りは無い）。識別子 `AIC` はこのチャットの系列（AIC-01〜42 で使用。AIC-17・24・31 は欠番）。

## 目的

AIC 系列（質問欄、#654）の 2026-11-03〜09 の指示（AIC-21〜42）を、チャット側で振り返り、Fable にレビューさせた。そのうち、規則にする2点をガイド文に入れる。規則でない学びは、別の指示（CHAT-1109-AIC-44）で docs/notes/ask-worker.md に入れる。

### 決定（2026-11-09、平野さん）

- 振り返りとそのレビューを、2本の指示に分けて記録する（規則にするもの＝この指示、規則でない学び＝AIC-44）

### 知見 → 選んだ層 → 理由（CLAUDE.md「Chat-Ref」、#492）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| レビューの往復2回の後に足した直しを、レビューを受けずにマージした回が2回あった（CHAT-1105-AIC-37 の 759e6347e、CHAT-1106-AIC-39 の fc2a6b735）。AIC-37 の分は翌日 CHAT-1105-AIC-38 でレビューさせ、6件の指摘が出て直した。docs/notes/self-review.md「収束」は「2回で収まらなければ、残った指摘を…移して止まる」とあるが、2回目の後に自分で直してマージする道を塞いでいない | docs/notes/self-review.md「収束」の節に1行（例「2回目のレビューの後にコードを直したら、その直しはレビューを受けていないので、マージせずに判断待ちで止まる」）。CLAUDE.md は変えない（「判断・作業の原則」の行が self-review.md を参照している） | センサー（hook・テスト）は、「最後のレビューの後にコミットがあるか」をログの表の書式に頼らずに判定できず、作りに見合わない。Code が読む self-review.md の今の節を締める形が最小。CLAUDE.md は 23.4KB で警告域（26KB）に近いので足さない |
| 指示文の前提に、実物を開かずにコードの振る舞いを書いた回があった（CHAT-1103-AIC-24 の `generate_top_page.py` の関数〈すでに無かった〉、CHAT-1109-AIC-40 の名字の候補〈`nameCandidates()` ですでに入っていた〉）。docs/notes/chat-side-operations.md「書く前に実物で確かめる」の「事実は実物で確かめたものだけを断定する」はあったが守れなかった。Code は前提の食い違いで正しく止まった（CLAUDE.md「Chat-Ref」） | docs/notes/chat-side-operations.md「書く前に実物で確かめる」の1つ目の項目の文に、「コードの振る舞いを前提に書くときは、開いた関数名と行番号を添える。添えられないものは（要確認）を付ける」を足す（新しい項目にせず、今の項目に足す） | 同じ規則を言葉を変えて足しても守れない（Fable のレビュー）。行番号を書く形にすると、開かずに書いたことが書く側にも読む側にも見える。Code 側は CLAUDE.md「Chat-Ref」の「前提と実物が食い違ったら…中断して報告」で受けているので変えない。docs/instruction-template.md は 13.6KB で警告域（14KB）なので触らない |

### 前提（チャット側。平野さんの決定ではない）

チャット側が 2026-11-09 20:30 JST ごろ、mj-logs の guide/e01a391c で読んだ。着手時に今の文書で開き直す。

- docs/notes/self-review.md 36〜46行あたり「## 収束」: 「往復は2回まで。…」「2回で収まらなければ、残った指摘を `## 報告` の「判断が必要なこと」に移して止まる」「同じ種類の指摘が別の指示でも出たら、…層の候補を書く…」
- docs/notes/chat-side-operations.md 81〜85行「### 書く前に実物で確かめる」の1つ目の項目「**事実は実物で確かめたものだけを断定する**（…）。「目的」「前提」の事実は、そのチャットで実物を読んだもの以外に「（要確認）」を付けて Claude Code に確かめさせ、…」
- 大きさ（作業ブランチの上で測る。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）: chat-side-operations.md は 22,476 バイト（上限 28KB・警告域 26KB）。self-review.md は上限なし
- 2つの文が、ほかのガイド文（CLAUDE.md・handover.md・docs/notes/branch-operations.md・docs/instruction-template.md）の記述と重ならないか、食い違わないかを確かめる。重なるなら片方を参照にする（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の「同じ内容を2箇所に書かない」）
- 文面は上の例のとおりでなくてよい。今の文の調子にそろえ、決めた文をログに書く

## 手順

1. 直す: 上の2か所を直す。docs/decisions/ には、上の「決定」と表を `docs/decisions/README.md` の書き方で足す（分野は実物で選ぶ。運用の決定の置き場所があればそこ）
2. 確かめる: chat-side-operations.md の大きさ（作業ブランチの上で）、`check-assets.yml` が見る上限に収まるか。2つの文の重なり・食い違いの確かめの結果をログに書く
3. マージ: ドキュメントのみなので、完了を報告のうえ cloudflare へマージする（CLAUDE.md「ブランチ運用」「マージするとき」）

## 止まる条件

- 2つの文のどちらかが、今のガイド文の別の記述と食い違う（直さずに、どこと食い違うかを書いて止まる）
- chat-side-operations.md が警告域（26KB）を超える
- 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-42 の「未確認の項目」の持ち越しの一覧は、この指示の「未確認の項目」にも写す
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1109-AIC-43.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1109-AIC-43 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e01a391c）: https://github.com/retroeater/mj-logs/tree/main/guide/e01a391c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e01a391c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e01a391c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e01a391c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e01a391c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e01a391c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e01a391c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b912fa9a.md
