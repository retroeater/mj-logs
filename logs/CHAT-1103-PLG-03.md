# CHAT-1103-PLG-03

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1103-plg
- 着手時HEAD: ad26c5345

## 指示

【Claude作成】Claude Code 向け指示：使っていない公式プラグイン 5 件を外したことを記録し、plugin の記述を今の 2 件に合わせる（PLG-02 の貼り直し） Chat-Ref: CHAT-1103-PLG-03 マージ: ドキュメントのみ（docs/notes・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（新しいセッションは任意。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1103-PLG-02 は、docs/notes/design.md に規則の 1 行を足す手順に「知見 → 選んだ層 → 理由」の表が無く、着手前に止まった（欠番）。その 1 行は外し、規則を足さない形で貼り直す。 hookify に続き、使われていない公式プラグイン 5 件を claude.ai のアカウントから外した。決定を記録し、docs/notes/skills.md などの plugin の記述を、残る 2 件（security-guidance・claude-security）に合わせる。コード・`.claude/` は変えない。
決定（2026-11-03、平野さん）

* 次の 5 件を claude.ai のアカウントから外した（平野さんの手作業、済み。プラグインの画面で残りが Security Guidance・claude-security の 2 件であることをチャットで確認）: code-review・code-simplifier・claude-code-setup・claude-md-management・frontend-design
* 残すのは security-guidance（編集ごとのパターン照合）と claude-security（深い診断。回し方は docs/notes/security.md の 2026-10-20 の決定のまま）
* 理由（チャットで示した材料）:
   * code-review: クラウドセッションで返り値が空で、2026-10-28（CRV）に使わないと決め、差分の自己レビュー（docs/notes/self-review.md）で代えている
   * code-simplifier: 使った記録が無い
   * claude-code-setup: 2026-10-20 の提案（CHAT-1020-PLG-01）で用が済んだ（読み取り専用）
   * claude-md-management: 2026-10-20 の監査（CHAT-1020-PLG-02）1 回だけ。年数回の監査（CLAUDE.md の容量の整理など）のときに入れ直す
   * frontend-design: 「思い切った、テンプレートらしくない見た目」へ引っ張る skill で、mj の既決のデザイン（docs/notes/design.md：静か・白基調・装飾を削ぐ・transition なし・Web フォントを増やさない、新しいページは houou/ の基本の形にそろえる）と競合する。新しい UI を作る場面で頼まなくても自動で呼ばれ得る。トップの作り直し（#101）のように方向性そのものを決めるときに入れ直し、比較ページの案出しにだけ使う
* 新しいページのデザインで frontend-design を毎回呼ぶ運用にはしない（基準は docs/notes/design.md・docs/decisions/design-renewal.md）。これは docs/decisions/operations.md の決定の記録に残し、docs/notes/design.md には規則として足さない（frontend-design を外したので自動で呼ばれる経路が無く、テスト・hook でも確かめられないため。PLG-02 の報告の案）

前提（チャット側。平野さんの決定ではない）

* このセッションが外す前に始まっていても、途中で同期し直されて一覧から消えることがある（CHAT-1020-PLG-07、CHAT-1103-PLG-01）
* `/code-review` には Claude Code の組み込みのもの（plugin ではない）もある。docs/notes/self-review.md「`/code-review` を使わない理由」はそちらも含む記述なので、plugin を外したことで変える必要は無い（要確認）

手順

1. `claude plugin list` を取り、「Synced from claude.ai」の一覧をログの `## 経過` に写す（security-guidance・claude-security の 2 件だけであることを確かめる。ほかが残っていれば、その名前とセッションの開始時刻を書き、止まらない）。CLAUDE.md・docs/（docs/logs/ を除く）の中で、外した 5 件（`code-review`〈plugin としての記述〉・`code-simplifier`・`claude-code-setup`・`claude-automation-recommender`・`claude-md-management`・`claude-md-improver`・`frontend-design`）を参照している箇所を全部挙げる。
2. docs/notes/skills.md の「plugin の扱い」「コンテキストの負荷」などの plugin の件数・一覧を今の 2 件に直し、外した 5 件と入れ直す場面（claude-md-management＝年数回の監査、frontend-design＝方向性を決めるときの案出し）を 1〜3 行で書く。同じ趣旨の記述があれば置き換え・拡張する。手順 1 で見つけたほかの参照も、今の状態に合うように直す（歴史の記録〈いつ何をしたか〉は残し、今の状態を述べる文だけ直す）。docs/decisions/operations.md に決定を追記する（CLAUDE.md「作業ログ」節のとおり）。
3. CHAT-1103-PLG-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-PLG-03` を足す。変えた文書の上限（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の対象なら）を作業ブランチの上で測ってログに書き、冒頭の「マージ:」の行のとおり cloudflare へマージする。

止まる条件

* この指示はガイド文に規則を足さない（docs/notes/skills.md の plugin の件数・一覧・入れ直す場面は今の状態の記述、docs/decisions/operations.md は決定の記録）。規則を足す必要が出たら、足さずに止まる
* 手順 2 で文書の記述と決定が矛盾し、どちらが正か判断が要るとき（同じ趣旨の記述があるだけなら止まらない）
* 差分が docs/notes・docs/decisions・docs/logs の外に出るとき（CLAUDE.md を変える必要が出たら、変えずに止まる）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが docs/notes/skills.md の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-PLG-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-PLG-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべて揃っている
- `git log --all --grep="CHAT-1103-PLG-03"` は0件、`docs/logs/` にも無し。CHAT-1103-PLG-01・02 と同じセッション
- 作業ブランチ: `work/1103-plg` はローカル・リモートとも `origin/cloudflare` の祖先（マージ済み）。`git merge --ff-only origin/cloudflare` は「Already up to date」（HEAD ad26c5345）
- 規則の表: この指示はガイド文に規則を足さない（指示文の止まる条件の1つ目）。PLG-02 で止まった docs/notes/design.md の1行は外されている
- 未マージの `work/` ブランチ: `origin/work/1029-lgn-page` が docs/decisions/operations.md の中ほどを変えている（今回の追記は末尾で別の箇所）。docs/notes/skills.md を変えているものは無し

### 1. plugin の一覧・参照箇所

- `claude plugin list` の「Synced from claude.ai:」: `claude-security@synced`・`security-guidance@synced` の2件（どちらも `Status: √ loaded`）。同期先の `manifest.json` も2件
  - 中身: claude-security は skill 1本（`claude-security`、`disable-model-invocation: true`）・agent 8本、security-guidance は hook だけ
- CLAUDE.md・docs/（docs/logs/ を除く）で外した5件を参照している箇所:
  - CLAUDE.md: 無し
  - docs/notes/skills.md 61行目「`mattpocock-skills` は25本、`code-review`・`triage` など組み込みや mj の運用と重なるもの」: mattpocock-skills の中の skill の名前で、plugin の code-review ではない。直さない
  - docs/notes/self-review.md「`/code-review` を使わない理由」: 「Claude Code の `/code-review` をクラウドセッションで試した（2026-10-27）」で、plugin に限らない記述。前提（要確認）のとおり、plugin を外しても変える必要は無い。直さない
  - docs/decisions/operations.md の 2026-10-20（PLG-01・07）・2026-10-28 ごろ（1182行目「`/code-review` は試し直さない」）の節: 決定の記録なので書き換えず、今回の節で置き換えを書いた
  - docs/notes/skills.md の「plugin の扱い」「コンテキストの負荷」: plugin の名前は無いが、件数・本数（7件）と採否の参照があり、今の状態に直す対象

### 2. 文書の直し

- docs/notes/skills.md「plugin の扱い」: 「アカウントに残している plugin と採否の経緯は docs/decisions/operations.md（2026-10-20・2026-11-03）」の1行を、
  残している2件（security-guidance・claude-security と各役割、回し方は docs/notes/security.md）と採否の経緯の参照、外した plugin のうち入れ直す場面（claude-md-management＝年数回の CLAUDE.md の監査、frontend-design＝方向性を決めるときの比較ページの案出しだけ）、入れ直す予定の無い3件、の3行に置き換えた
- docs/notes/skills.md「コンテキストの負荷」: 「2026-11-03 は plugin 7件で skill 4本・slash command 2本・agent 9本」を「plugin 2件で skill 1本〈`disable-model-invocation: true`〉・agent 8本、どちらも claude-security。security-guidance は hook だけ」にした
- 規則は足していない（入れ直す場面は今の状態の記述として書いた。「使い終えたら外す」は決定に無いので書かなかった）
- docs/decisions/operations.md の末尾に「2026-11-03（CHAT-1103-PLG-03）」の節を足した（2026-10-20 の「残す」8件を置き換える旨を含む）
- CHAT-1103-PLG-02 のログの `## 報告`（ファイルの最後の一致）の状態を「判断待ち / 続き: CHAT-1103-PLG-03」にした
- 差分は docs/notes/skills.md・docs/decisions/operations.md・docs/logs/ の範囲

### 3. 上限

- 変えた docs/notes/skills.md・docs/decisions/operations.md は、CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の上限の対象（CLAUDE.md・handover.md・chat-side-operations.md・instruction-template.md）ではない。測る対象は無し

## 報告

- 状態: 完了
- ブランチ: work/1103-plg
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1103-PLG-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-plg
- 確認用URL: なし
- マージ: 済（1eb05aaa0 を fast-forward で push。ドキュメントのみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 46a47f07）: https://github.com/retroeater/mj-logs/tree/main/guide/46a47f07

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad26c534.md
