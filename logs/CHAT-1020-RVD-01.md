# CHAT-1020-RVD-01

- 着手日時: 2026-10-20
- 対象issue: #492
- ブランチ: work/1020-rvd
- 着手時HEAD: 03eef856dbb76889f5f6af041329dcfc9a543f81

## 指示

【Claude作成】Claude Code 向け指示：ガイド4文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）をレビューして古い事実・重複・冗長を直し、サイズを減らして判断待ちで止まる
Chat-Ref: CHAT-1020-RVD-01
マージ: 判断待ちで止まる（整理後の全文をチャット側が読み比べてから、マージの指示を出す）
貼る時機: いつでも（新しいセッション推奨。並行する PLG-08 の続きとは別のブランチ）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-rvd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1020-rvd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1020-rvd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
容量制限のある4文書を見直し（古い事実・重複・冗長・食い違い）、規則を失わずにサイズを減らす（#492）。instruction-template.md は警告域（14KB = 14,336 バイト）の 11 バイト手前。
チャット側の実測（mj-logs の guide/e18c2138。2026-10-20 17時台の origin/cloudflare の HEAD e18c2138 と同じ中身）: CLAUDE.md 24,859・handover.md 22,937・chat-side-operations.md 21,976・instruction-template.md 14,325 バイト。
目安（届かなくても規則は消さない）: instruction-template.md 13,000 以下・CLAUDE.md 22,500 以下・handover.md 22,500 以下・chat-side-operations.md は 22,200 以下（純増は 200 バイトまで。冒頭の「純増したら同じ量を移す」の規則があるため）。

### 決定（平野さん）
- 「レビュー」は、容量制限のある文書を包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。文書の変更は判断待ちで止め、整理後の全文をログに貼らせて読み比べてからマージする（2026-09-29）
- CLAUDE.md の場面限定の手順（最終報告の形・Chat-Ref の受け取る側の細目）を docs/notes/ へ移すのは、容量の整理の指示で行う（2026-10-20、CHAT-1020-PLG-07 の決定。docs/decisions/operations.md「2026-10-20（CHAT-1020-PLG-07）」）

### 前提（チャット側。平野さんの決定ではない）
チャット側が mj-logs のガイド（guide/e18c2138）と REST（issue の状態、2026-10-20）で見つけた候補。実物（その時点の origin/cloudflare）で確かめ、違えば直さずに報告に書く。下の候補以外の重複・冗長も直してよい。
足す行は `docs/notes/handover-archive-2026.md` の「〜から外した記述」の節（2026-09-28〜2026-10-14 の5節）を先に確かめ、前に意図して外した行を足し戻さない。
Codespace に関する記述（CLAUDE.md「ブランチ運用」、handover.md 3章「役割分担」、instruction-template.md「作業ブランチ」の Codespace の文面）は、この指示では直さない（平野さんの判断を別に確かめる）。

- (a) 古い事実（要確認）:
  - a1. CLAUDE.md「データの流れ」の「**型Cの選手選択リストに退会済みの選手が出ないのは正しい挙動**（同「ページ側のJS」、#168）。Google Charts依存の6ページは旧方式（#7）」: 型C の2ページと Google Charts 依存の6ページは #582 で消し（docs/notes/static-generation.md の 2026-10-19 の注記）、#7 は closed（REST）。行を外す。#168 の規則が今のページ（houou/ の検索など）にも当てはまるなら、その形に書き換える（当てはまらなければ archive へ）
  - a2. CLAUDE.md「判断・作業の原則」の「外部ドメインへの依存を増やさない（CSP導入を予定しているため）」: CSP は 2026-10-19 に本番へ入り（`_headers`）、#9 は closed。理由を「CSP の許可（`_headers`）を足す必要が出るため」の趣旨に直す。handover.md 4章「外部ドメインへの依存を増やさない」の「CSP〈#9〉のため」「CSPでは `script-src` にのみ必要」「#9 でCSPの`img-src`を書くときの指針は #9 のコメント」も、導入済みの現状（許可の正は `_headers`。img-src の値は実物で確かめる）に合わせて短くする
  - a3. handover.md 5章の表の #9 の行: closed のため消す
  - a4. handover.md 5章「次の会話の順番（2026-10-09）」の #523: closed（REST）。外す。#388・#389 は open・状況: 待ち のまま
  - a5. handover.md 5章の表の最後の行「#425 も現行サイトで作る（#501・#502）。#366 は載せ方が未決」: #425・#501・#502・#366 はすべて closed（#366 は wrcr/ として公開、#611）。済んだものを外し、未決の「#365・#367 のデータを誰がいつ入力するか」と「#378 は新サイト送り」だけ残す
  - a6. handover.md 3章「新サイト全体の親 issue は #296（sub-issue 21件）」: REST の sub_issues は 30 件。件数は消し「sub-issue の一覧は #296」とする（件数は変わるため）
  - a7. handover.md「最終更新」: この整理の日付にし、3行をこの整理の変更に置き換える（CLAUDE.md「更新ルール」の書き方）
  - a8. handover.md 6章の表の `docs/notes/security.md` の行の「〈2026-10-20 に閉じた〉」: 日付は要らない（「結果は #598〈closed〉、残りは #619」など）
  - a9. CLAUDE.md「構成」の「Bootstrap 5.3.8」: `assets/vendor` の実物の版と合っているか確かめる（違えば直す）
  - a10. CLAUDE.md「作業ログ」の「規則の削除は #298 の実装4の後半」: #298 は open で予定どおりのため、そのまま
- (b) 重複（片方を正にし、もう片方は削るか参照1つにする。PLG-02 の追加候補3）:
  - b1. CLAUDE.md「作業ログ」の「**ターミナルへ返す最終報告は、状態・ログのURL・…の行だけにする**…」の項と「例外として、次の2つは…」の項: 形は `docs/logs/_template.md`「ターミナルへ返す最終報告」が正。CLAUDE.md は「最終報告は `_template.md`「ターミナルへ返す最終報告」の形。最後の行は Chat-Ref、直前は「ログ（公開）」の行（mj-logs で今回の版が返ることを確かめてから）。質問で止まるとき・push に失敗したときだけ内容をターミナルに書く」程度の要点と参照に縮める。`_template.md` に無い規則（「ログ（公開）」の確かめ、例外2つ）があれば `_template.md` へ移してから縮める
  - b2. CLAUDE.md「Chat-Ref」の「着手前の確認」の3つの小項目: docs/notes/branch-operations.md「Chat-Ref の着手前の確認」が正。CLAUDE.md は「同じ Chat-Ref のコミット／`XXX` の重複／0章ゲート（#265・#336）の3点。当たれば着手せず止まる」の1行と参照にし、細目（括弧内の理由・コマンド）は branch-operations.md に無ければ移す
  - b3. CLAUDE.md「Chat-Ref」冒頭の識別子の規則（3文字・2文字は重ねない、#474）と docs/notes/chat-side-operations.md「セッション識別子の確認」の1項目め: chat-side は CLAUDE.md「Chat-Ref」への参照1行に
  - b4. CLAUDE.md「issueの着手ルール」の最後の項（Issues で管理・ボードは使わない・期日はカレンダー）と handover.md 3章「タスク管理」: handover が正。CLAUDE.md は「期日はカレンダー「ryoei.pro」（handover.md「タスク管理」）」の1行に
  - b5. CLAUDE.md「コミットのルール」の「文章をシェル経由で書かない」の括弧内の具体（ヒアドキュメントの引用符、`--body-file`、`git commit -F -`）: 規則の1行と参照を残し、具体は docs/notes/cloud-sessions.md か branch-operations.md の該当の節へ（無ければ節を作る）
  - b6. CLAUDE.md「コード規約」の「簡略化:」の例（`# 簡略化: 全件を線形に走査…`）と「`grep 簡略化:` で台帳にするため語を変えない」: 語を変えない規則は残し、例は外す
- (c) instruction-template.md から場面限定の規則を移す（上限なしの docs/notes/chat-routines.md「指示の種類ごとの確かめ」へ。chat-side-operations.md には足さない〈純増の規則〉。template には「計測・試運転・本番へ書き込むワークフローの試し・データ更新後の生成を含む指示の書き方は chat-routines.md「指示の種類ごとの確かめ」」の参照1行だけ残す）:
  - c1. 「**計測を伴う指示は、環境・回数・判定を最初の指示で決めて書く**」とその4つの小項目
  - c2. 「**試運転（dry-run の手動実行など）が失敗で止まる見込みのある指示は…**」
  - c3. 「**本番に書き込むワークフローの変更をマージ前に試すときは…**」
  - c4. 「**シート・SNS ブックなどデータが変わった後にページを生成する指示では…**」
  - c5. 移す規則が chat-routines.md に見つかる場面になるよう、chat-side-operations.md「場面ごとに読む文書」の表に1行足す（「計測・試運転・本番へ書き込むワークフローの試し・データ更新後の生成を含む指示」→ `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」）。これは規則の追加ではなく参照の行だが、CLAUDE.md「Chat-Ref」節の規則に従い表を付ける:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 計測・試運転・書き込みワークフローの試し・データ更新後の生成の書き方は、該当する指示を書くときだけ要る | docs/notes/chat-routines.md（上限なし）＋ chat-side-operations.md の表の参照1行 | 雛形は毎回読むので上限が厳しい。場面限定の手順は表から引く既存の形に合わせる |

  - c6. template のほかの項の言い換え・理由の重複も削る。「作業ブランチ」の行の文面3つは変えない（写す文面のため）
- (d) handover.md 5章の表の備考の圧縮（経緯を外し、今の状態と参照だけに）: #486 の行の「旧ランキング3ページは houou/・ouka/・jwrc/ へ移って消えた（#582）」、#490 の行の「（#437・#477 から集約。…）」、#475 の行の運用の説明（docs/notes/live-channel-write.md にあれば参照に）。外した経緯は archive へ
- (e) 圧縮で消してはならないもの: 規則そのもの、issue 番号、止まる条件、決定の内容。迷ったら残して報告に書く。外した括弧内の事例・経緯は `docs/notes/handover-archive-2026.md` に「2026-10-20 の整理で4文書から外した記述（#492）」の節を作って移す
- 取り込みの衝突: 別のセッション（PLG-08 の続きなど）が CLAUDE.md・docs/notes/skills.md を変えていそうなので、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492 など文書の上限・整理の issue）、この指示と食い違う進行中の作業（着手中のコメントがある等）があれば止まって報告する。`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、4文書と移す先（chat-routines.md・branch-operations.md・cloud-sessions.md・`docs/logs/_template.md`・archive）を変えているものを一覧にしてログに書く（重なっても止まらない。同じ行・同じ節を変えているものは報告に書く）。#492 に着手中のコメントを残す（本文・状態・ラベルは変えない）
2. 前提 (a)〜(d) を実物で確かめて直し、ほかに見つけた重複・冗長・食い違いも直す。対応表（候補 → 確かめた事実 → 処理〈削除／統合先／参照化／移した先／残した理由〉）を経過に書く。参照先の見出しが実在することを確かめる
3. 4文書の前後のサイズ（バイト、作業ブランチの上で `wc -c`）を報告に書き、整理後の4文書の全文と、移した先（notes・`_template.md`・archive）の差分を経過に貼る。`python3 scripts/check_asset_limits.py` と `python3 -m unittest discover -s scripts/tests`（文書参照の検査を含む）を通す

## 止まる条件
- 手順1で同じ論点の進行中の作業が見つかった
- 規則を消さないと減らせない箇所がある（消さずに報告に書く。止まるのはその箇所だけで、ほかは進める）
- chat-side-operations.md の純増が 200 バイトを超える（超えるなら足す行を縮めるか、同じ文書の重複〈b3〉で相殺する。相殺できなければ止まる）
- 取り込みで、両立しない衝突が出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしないので通常は起きない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の「判断が必要なこと」に、マージの可否と a1・a2・a9 の結果を書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-RVD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-RVD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 03eef856）: https://github.com/retroeater/mj-logs/tree/main/guide/03eef856

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/03eef856.md
