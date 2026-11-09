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

## 経過

### 着手前の確認

- `## 指示` 欄の末尾は指示文の最後の行と一致。同じ Chat-Ref のコミット・ログ: なし
- e01a391cf は origin/cloudflare の祖先。作業ブランチ: origin/work/1109-aic はマージ済み、ローカルの work/1109-aic は origin/cloudflare（791d381ac）と同じなので、`git merge --ff-only origin/cloudflare`（変化なし）で使った
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 規則を足す指示で、「知見 → 選んだ層 → 理由」の表がある（CLAUDE.md「Chat-Ref」、#492）
- AIC-42 のログの状態に「/ 続き: CHAT-1109-AIC-43」を足した（判断の残りは無いので回答は書いていない）

### 1. 直した文（cda324e9d）

- docs/notes/self-review.md「## 収束」の2つ目の項目の後に1行:
  「2回目のレビューの後にコード・生成物を直したら、その直しはレビューを受けていないので、マージせずに `## 報告` の「判断が必要なこと」に書いて判断待ちで止まる（レビューは続きの指示で受ける）」
- docs/notes/chat-side-operations.md「### 書く前に実物で確かめる」の1つ目の項目の、「怪しいものは「〜を確かめ、食い違えば止まる」の形にする。」の後に:
  「コードの振る舞いは開いた関数名と行番号を添えて書く（無ければ「（要確認）」）。」
- chat-side-operations.md の冒頭の規則「純増したら、足した分と同じ量を docs/notes/ の各文書へ移す」（`check-assets.yml` の「chat-side-operations.md の純増を確認」）に合わせ、純増しないようにした:
  - 「本番へまとめて要求を送る指示（一括取得・HEAD の確かめ・Playwright での巡回）では、UA に `mj-internal` を含めさせる（#568 の集計で人として数えないため。`scripts/fetch_redirect_counts.py` の `BOT_UA_PATTERNS`）」の項目を、「本番へまとめて要求を送るときの UA（`mj-internal`）は CLAUDE.md「判断・作業の原則」が Code に課している（#568）」に置き換えた（CLAUDE.md「判断・作業の原則」に同じ規則がある。同じ内容を2箇所に書かない）
  - 足した文を短くした（指示の例「コードの振る舞いを前提に書くときは、開いた関数名と行番号を添える。添えられないものは（要確認）を付ける」から、意味を変えずに詰めた）

### 2. 確かめ

- 大きさ（作業ブランチの上で）: chat-side-operations.md 22,476 → 22,470 バイト（純増なし。上限 28KB・警告域 26KB、`check-assets.yml` の純増の警告の FLOOR 24,576 の下）。self-review.md は上限なし
- 重なり・食い違い:
  - self-review.md の1行: CLAUDE.md「判断・作業の原則」は self-review.md を参照するだけ（「往復は2回まで」）、handover.md の文書の表も参照だけ。CLAUDE.md「ブランチ運用」の「承認済みでも、確認が1つでも通らない・止まる条件に当たった・前提が崩れたときはマージせずに報告する」、docs/notes/branch-operations.md「マージするとき」とも食い違わない（2回目の後の直しはマージしない側に足すだけ）
  - chat-side-operations.md の文: docs/instruction-template.md の「前提」の欄の書き方（「〜を確かめ、食い違えば止まる」の形）、CLAUDE.md「Chat-Ref」の「前提と実物が食い違ったら…中断して報告」と食い違わない。行番号を添える規則はほかの文書に無い（重ならない）
- docs/decisions/operations.md に「2026-11-09（CHAT-1109-AIC-43）」を足した（運用の決定の置き場所）
- テスト: `python3 -m unittest discover -s scripts/tests` OK（docs のみ）

## 報告

- 状態: 判断待ち / 続き: CHAT-1109-AIC-44（「判断が必要なこと」は CHAT-1109-AIC-44 の時点でチャット側の回答なし）
- ブランチ: work/1109-aic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1109-AIC-43.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1109-aic
- 確認用URL: なし（docs のみ）
- マージ: 済（docs のみ、完了の報告のうえマージ）
- issue: なし（docs のみ。#654 にはコメントしていない）
- 判断が必要なこと:
  - 指示に無かった変更: chat-side-operations.md の冒頭の規則（純増したら同じ量を移す）に合わせ、UA の項目を CLAUDE.md への参照の1行に置き換えた（CLAUDE.md「判断・作業の原則」に同じ規則があるため）。足した文も指示の例より短くした。このままでよいか
  - 状態を判断待ちにしたのは、下の「未確認の項目」（持ち越しの一覧）が残るため
- 未確認の項目:
  - 持ち越す言い回しと見送った問い（AIC-42 から写した。次の週次の書き出し〈2026-11-16〉で、実際に聞かれたか見る）:
    - AIC-19: 決勝だけの答えの「成績」「記録」の重なり、「WRCリーグ決勝の第10期に4位」の語順
    - AIC-29: 「4位の古川孝次さんから10位の杉浦勘介さんまでは残留です」（11〜13位も残留）
    - AIC-37: 予定の略称（「グランプリ」「日本シリーズ」）、title/ に無い大会と title/ の大会を両方書くと後者だけ、件名の「決勝進出者決定戦」が決勝になる、候補が最初の大会の名簿から
    - AIC-38: 予定の問いで過去・否定でも段を読む、「決勝進出者決定戦はいつ？」で1回目の stage を使う
    - AIC-39: 近道に入らない予定の言い回し（「王位戦、次はいつ？」「王位戦の次の放送は？」「王位戦っていつ？」など）、「直近」の過去の意味
    - AIC-40: 作り直しの JSON が null のとき、空白入り・漢数字の「検索で 43 件」
    - AIC-42: 名字だけの1文字の問い（「勝」）で、大会によって1回目が選手名の問いと読まない
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fc46d17c）: https://github.com/retroeater/mj-logs/tree/main/guide/fc46d17c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b912fa9a.md
