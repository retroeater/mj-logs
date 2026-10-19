# CHAT-1019-ENG-03

- 着手日時: 2026-10-19
- 対象issue: なし（手順1で検索）
- ブランチ: work/1019-eng
- 着手時HEAD: 9903e2209

## 指示

【Claude作成】Claude Code 向け指示：ENG の振り返りの申送り（シートを移した・消したと言われたときの確かめを、既存の手順に足す）
Chat-Ref: CHAT-1019-ENG-03
マージ: ドキュメントのみ（docs/notes・docs/decisions・ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-eng の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-eng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-eng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
ENG のチャットの振り返り（2026-10-19）の知見のうち、手順に落とせる1件を残す。平野さんが「シートを別のスプレッドシートへ移した」「シート・スプレッドシートを消す」と言ったとき、チャット側がその場で確かめず、伝言に「見立て（確かめていない）」と書いて終わった（旧「ログ」シートは結果として残っていた）。

### 決定（2026-10-19、平野さん）
- 振り返りの知見を申送りする（「申送り」の指示）

### 前提（チャット側。平野さんの決定ではない）
- 知見: 平野さんがシート・スプレッドシートを「移した」「消した」「廃止する」と言ったら、チャット側は答える前に (a) まだそれを読むコードがあるか（`git grep` で、スプレッドシートの定数名・シート名）と (b) 移し方がコピーか移動か（旧い側にまだ残っているか）を確かめさせる。読むコードが残っているうちは、消すのを待ってもらう
- 置き場所の候補（要確認）: `docs/notes/chat-side-operations.md` の「場面ごとに読む文書」の表の行「…シートを用意したと言われた」と、`docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の対応する項、`docs/notes/static-generation.md`「平野さんがシートを用意した・直したと言ったときのチャット側の指示」。**新しい行・節は立てず、既存の「用意した・直した」を「用意した・直した・移した・消した」に広げる形**を第一案にする（chat-side-operations.md は純増させない）
- 計算的センサーの候補: スプレッドシート（定数名）とシートごとに、読むスクリプトを一覧で出す検査（#557 の「読み手の一覧（17冊）」と同じ中身）。既にそれに近いもの（スクリプト・テスト・文書の表）があるかを先に探す。無ければ作らず、起票するかどうかを報告に書く（この指示では作らない）

## 手順
1. 同じ論点の open issue（シートの移動・削除のときの確かめ、シートの読み手の一覧）を検索し（クローズ済みも含めて確認）、あれば止まって報告する。上の「置き場所の候補」の3か所の今の内容と、計算的センサーの候補に近いものの有無を読み、ログに引用する
2. 知見を、計算的センサー → 推論的センサー → ガイド文 の順に検討し（docs/notes/chat-routines.md「申送り」）、選んだ層で書く。ガイド文を選んだときは「知見 → 選んだ層 → 理由」の表をログに書く。書く前に追記先の残り容量（上限・警告域との差）を測ってログに書く。規則と理由の一句だけを書き、事例（ENG の旧「ログ」シートの件）は `docs/notes/handover-archive-2026.md` へ置くか書かない。追記先に同じ趣旨の記述があれば置き換え・拡張してよい
3. `docs/decisions/operations.md` に、この申送りを書いたことを1行で足す（既存の申送りの行の書き方に合わせる）

## 止まる条件
- 同じ論点の open issue がある、他セッションの着手中コメントがある
- 追記先の記述と矛盾していて、どちらが正か判断が要る（同じ趣旨なら置き換えて進める）
- `chat-side-operations.md` が純増する形でしか書けない（純増の量と、移す候補を書いて止まる）
- 未マージの work/ ブランチが追記先の同じ行・同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-ENG-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-ENG-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `CHAT-1019-ENG-03` のコミット無し（このセッションの3つ目の指示）
- 作業ブランチ: ローカルの work/1019-eng は origin/cloudflare の祖先（ENG-02 でマージ済み）。`git merge --ff-only origin/cloudflare`（9903e2209）
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 止めた理由（着手前の確認）

- CLAUDE.md「Chat-Ref」節: 「ガイド文（CLAUDE.md・handover.md・docs/notes/ のチャット側の文書）に規則を足す指示に『知見 → 選んだ層 → 理由』の表が無ければ、着手せず報告して止まる」。`docs/notes/chat-routines.md`「申送り」の観点のチェックリストも「ガイド文を選んだときは、指示文に表を入れる（無いと Code は止まる、#492）」
- この指示は置き場所の候補3か所がどれもガイド文（`chat-side-operations.md`・`chat-routines.md`・`static-generation.md` のチャット側の節）で、計算的センサーは「この指示では作らない」。結果はガイド文になるが、表は指示文に無く、受け手が作る形になっている。このため手順2・3（文書の編集と operations.md の申送りの行）は行っていない
- 判断に要るので読んだもの（編集はしていない）:
  - `chat-side-operations.md`「場面ごとに読む文書」の行: 「最初の指示・マージ・クローズ・…・一覧の行の形を決める・シートを用意したと言われた | `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」」
  - `chat-routines.md`「指示の種類ごとの確かめ」: 「平野さんがシート（タブ）を用意した・直したと言ったとき: `docs/notes/static-generation.md`「生成を止める条件の設計」の末尾の手順に従う（生成と同じ経路で読ませてから指示を書く）」
  - `static-generation.md`「平野さんがシートを用意した・直したと言ったときのチャット側の指示」: 1項目め「…消す・直すを頼むときは済んだ後の状態（ブックごとのタブの数など）を書き、平野さんに数を返してもらってから次の指示を作る」、2項目め「タブ・列を変えたと言われたら、そのタブを読む処理（凍結中のページ・検査を含む）を `git grep` で洗わせる手順を次の指示に入れる」。**知見の (a)（読むコードが残っているか）は2項目めとほぼ同じ趣旨で既にある**。足りないのは (b)（コピーか移動か、旧い側に残っているか）と「読むコードが残っているうちは消すのを待ってもらう」と、見出し・表の語（「用意した・直した」）に「移した・消した」が無いこと
  - 計算的センサーに近いもの: スクリプト・テストは無い。文書では `docs/notes/sheets-reader.md` が「読み手の一覧（17冊）」を #557（open、ブックの公開範囲の整理）のコメントに置いている（一覧はリポジトリの検査ではない）
  - 容量: `chat-side-operations.md` 22,155 バイト（上限 28,672・警告域 26,624 との差 4,469）。`chat-routines.md` 16,761・`static-generation.md` 76,441（どちらも上限なし）
- 参考（実物の確かめ）: 旧ブック（ENG-02 で「対局」「ログ」があったもの）のシートは今「ログ」だけ。`generate_resource_logs.py` は 3層のスプレッドシートを読むように替わっていて（LOG-01）、旧ブックを読むコードは `scripts/` に無い

### 表の案（チャット側が指示文に入れる場合の材料。決めるのはチャット側・平野さん）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| シート・スプレッドシートを「移した・消した・廃止する」と言われたら、答える前に (a) 読むコードが残っているか（`git grep` でスプレッドシートの定数名・シート名）と (b) コピーか移動か（旧い側に残っているか）を確かめさせ、読むコードが残っているうちは消すのを待ってもらう | ガイド文（推論的センサーとしてのチャット側の手順）。`static-generation.md` の既存の節の見出しと2項目めを「用意した・直した・移した・消した」に広げ、(b) と「待ってもらう」を1文で足す。`chat-routines.md` の項と `chat-side-operations.md` の表の語も同じ語に置き換える（`chat-side-operations.md` は語の置き換えだけで、増えるのは十数バイト） | 計算的センサー（定数名・シート名ごとの読み手の一覧を出す検査）は、シートがリポジトリの外にあり「消した」の申告をコードの側で受け取れないため、移す・消すの場面では効かない。読み手の一覧は #557 の範囲で、作るかは起票の判断 |

- 注: 案のとおりでも `chat-side-operations.md` は語の置き換えで十数バイト増える（「純増させない」の条件に当たるかはチャット側の判断。増やさないなら表の語は変えず、`chat-routines.md` 側だけ広げる）

## 報告

- 状態: 判断待ち / 続き: CHAT-1019-ENG-04
- ブランチ: work/1019-eng
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-ENG-03.md
- 比較URL: https://github.com/retroeater/mj/compare/9903e2209...work/1019-eng
- 確認用URL: なし
- マージ: 済（ログと decisions のみ。ガイド文は変えていない）
- issue: なし（同じ論点の issue の検索は着手前に止めたので未実施。#557 は読み手の一覧の置き場所として読んだだけ）
- 判断が必要なこと:
  - 指示文に「知見 → 選んだ層 → 理由」の表が無いため、CLAUDE.md「Chat-Ref」節のとおり着手せず止めた。表を入れた指示（`## 経過` の「表の案」を材料にできる）を新しい Chat-Ref で貼ってほしい
  - `chat-side-operations.md` の表の語を「用意した・直した・移した・消した」に広げると十数バイト増える。許すか、`chat-routines.md` 側だけ広げるか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d0930563）: https://github.com/retroeater/mj-logs/tree/main/guide/d0930563

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0930563/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0930563/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0930563/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0930563/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0930563/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0930563/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19fa8d37.md
