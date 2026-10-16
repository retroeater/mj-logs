# CHAT-1016-ACF-25

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-acf-notes
- 着手時HEAD: a9421dee940d4b54444253268403bda9422841c1

## 指示

【Claude作成】Claude Code 向け指示：ACF の振り返り（2026-10-16）の申送り — 朝の確かめの範囲・調べの前の mj-logs の検索・伝言の渡し方を文書に書く（docs のみ）
Chat-Ref: CHAT-1016-ACF-25
マージ: ドキュメントのみ（docs/notes・docs/decisions・ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-acf-notes の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜15・CHAT-1014-ACF-16〜21・CHAT-1015-ACF-22〜23・CHAT-1016-ACF-24 で使ったもの。
作業ブランチ: クラウドセッションで実行する。work/1016-acf-notes を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-acf-notes origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

ACF のチャットの振り返り（2026-10-16、ACF-15〜24 の範囲）で出た知見のうち、手順に落とせる3つを文書に書く。事例は書かず、規則と理由の一句だけを書く。

### 決定（2026-10-16、平野さん。層の選び方は Fable 5.1 のレビューのおすすめ）

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| (1) 朝の確かめで mj-logs の Actions の失敗（「mj の作業ログを写す」#2417 の 502）を見落とした。Worker の朝の確かめ（#506）は mj の行しか見ないので、mj-logs は #506 に出ない | ガイド文（`docs/notes/github-actions.md`「チャット側」） | 計算的センサー（Worker に mj-logs の行を足す）は採らない: mj-logs の失敗は GitHub のメールで平野さんに届いており、二重の通知になる。推論的センサー（Code の手順）は当たらない: 朝の確かめはチャット側が行う。見る範囲を列挙するガイド文で足りる |
| (2) 調べの指示（ACF-22）を、別のチャット（JPL-07）が同じ run を調べ済みと知らずに出した。Code の手順1（mj-logs を run 番号で検索）で止まったので1往復で済んだが、チャット側で先に grep すれば往復が消える | ガイド文（`docs/notes/chat-routines.md`「指示の種類ごとの確かめ」） | 推論的センサーは既にある（指示の手順1。これは保つ）。足すのはチャット側の、書く前の確かめなのでガイド文 |
| (3) 他のチャットへの伝言（返信を含む）は、チャット本文に書かず .md の添付で渡す（画面が何十行も流れないため。2026-10-14、チャット全体の規則）。今はプロジェクトのメモリーにしか無く、Code 側・ほかの会話から見えない | ガイド文（`docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」） | 毎回のやり取りに効く規則なので、上限ありの日常の規則の文書に置く。センサーには落とせない（チャットの出力の形） |

### 前提（チャット側。平野さんの決定ではない。2026-10-16 10:15 JST に mj-logs の guide/4fdda151 で測った）
- `docs/notes/chat-side-operations.md`: 22,203 B（上限 28,672 B、警告域 26,624 B。`assets-check.yml` が測り、**純増も検査する**）。足した分と同じ量を同じ文書から `docs/notes/handover-archive-2026.md`「docs/notes/chat-side-operations.md から」へ移す
- `docs/notes/github-actions.md`: 34,026 B、`docs/notes/chat-routines.md`: 11,646 B。どちらも上限なし
- `github-actions.md`「チャット側」の節（124行付近）には「失敗通知メールは1件ずつ扱う。本番稼働済みの Actions のエラーは ACF のチャットで解決する」の項がある。(1) はこの項に足す
- `chat-routines.md`「指示の種類ごとの確かめ」には「最初の指示: 関係する issue の本文・コメント、未マージの work/ ブランチ…」の項がある。(2) はこの項に足す
- `chat-side-operations.md`「指示文の書き方・渡し方」（115行付近）の項の並びに (3) を足す
- CLAUDE.md・handover.md・chat-side-operations.md には出典としての Chat-Ref を書かない（issue 番号は書いてよい）
- 使う skill は無い

## 手順
1. 3文書の今のサイズを `wc -c` で測ってログに書く（作業ブランチの上で）。既に同じ趣旨の行が無いか、各文書を grep で確かめる（「mj-logs」「添付」「run 番号」）。あれば足さずに、その行を直す
2. 次の3行を足す（文面はこの趣旨で。長さは各1〜2行）。
   - (1) `github-actions.md`「チャット側」の「失敗通知メールは1件ずつ扱う」の項に: 「朝の確かめで見るのは mj の Actions・**mj-logs の Actions**（「mj の作業ログを写す」。Worker の朝の確かめは mj の行しか見ないので #506 に出ない）・Worker の起動（毎朝の運行が1回だけか）・#506・届いた失敗のメール。work/ ブランチの失敗はその系列のチャットへ伝言、cloudflare の失敗が ACF」
   - (2) `chat-routines.md`「指示の種類ごとの確かめ」の「最初の指示」の項に: 「調べの指示（失敗の原因・run 番号が分かっているもの）は、書く前にチャット側で mj-logs の `logs/` を run 番号と issue 番号で grep する（別のチャットが調べ済みなら指示を出さず、そのログを使う）」
   - (3) `chat-side-operations.md`「指示文の書き方・渡し方」に: 「**他のチャットへの伝言（返信を含む）は .md の添付で渡す。** チャット本文は要点2〜3行と平野さんに頼む判断・手作業だけ（本文に書くと画面が流れる）」。足した分と同じ量を、同じ文書の括弧内の事例・経緯から選んで `handover-archive-2026.md`「docs/notes/chat-side-operations.md から」へ移す（規則そのもの・issue 番号・止まる条件は移さない。移した行と移し先をログに書く）
3. 3文書のサイズを測り直し、chat-side-operations.md が純増していないことを確かめる。決定（上の表）を `docs/decisions/automation.md` に足す。cloudflare へマージする

## 止まる条件
- 差分が docs/notes/github-actions.md・docs/notes/chat-routines.md・docs/notes/chat-side-operations.md・docs/notes/handover-archive-2026.md・docs/decisions/automation.md・ログ以外に出た
- chat-side-operations.md の移す候補が見つからず、純増になる（候補を報告して止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（3文書の前後のサイズ、足した行と移した行）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-ACF-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-ACF-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。決定に「知見 → 選んだ層 → 理由」の表がある
- Chat-Ref の重複: `git log --all --grep="CHAT-1016-ACF-25"` は 0 件。`work/1016-acf-notes` はローカル・リモートとも無く、`git checkout -b work/1016-acf-notes origin/cloudflare`（a9421dee9）
- 手順1（作業ブランチの上で `wc -c`）: github-actions.md 34,026 B・chat-routines.md 11,646 B・chat-side-operations.md 22,203 B（前提の値と一致）。handover-archive-2026.md 100,083 B
- 同じ趣旨の行の確かめ（「mj-logs」「添付」「run 番号」で grep）: 3文書とも無い。chat-side-operations.md の「添付」の行（「『添付した』と書く前に、ファイルを提示し終えたことを確かめる」「チャットの添付ファイル（CSV 等）は Code から読めないことがある」）は別の趣旨。github-actions.md「チャット側」に朝の確かめの範囲の列挙は無い
- 手順2で足した行:
  - (1) github-actions.md「チャット側」の「失敗通知メールは1件ずつ扱う。」の項の3行目: 「朝の確かめで見るのは mj の Actions・**mj-logs の Actions**（「mj の作業ログを写す」。Worker の朝の確かめは mj の行しか見ないので #506 に出ない）・Worker の起動（毎朝の運行が1回だけか）・#506・届いた失敗のメール。work/ ブランチの失敗はその系列のチャットへ伝言し、cloudflare の失敗は ACF のチャットが扱う」
  - (2) chat-routines.md「指示の種類ごとの確かめ」の「最初の指示」の項の2行目: 「調べの指示（失敗の原因・run 番号が分かっているもの）は、書く前にチャット側で mj-logs の `logs/` を run 番号と issue 番号で grep する（別のチャットが調べ済みなら指示を出さず、そのログを使う）」
  - (3) chat-side-operations.md「指示文の書き方・渡し方」の「チャットの添付ファイル（CSV 等）…」の項の次: 「- **他のチャットへの伝言（返信を含む）は .md の添付で渡す。** チャット本文は要点2〜3行と平野さんに頼む判断・手作業だけ（本文に書くと画面が流れる）」（213 B）
- (3) の分として chat-side-operations.md から移した括弧内の事例・経緯（計 224 B）。移し先は handover-archive-2026.md の新しい小見出し「### 2026-10-16 に chat-side-operations.md から外した括弧内の事例・経緯（#492）」（「## docs/notes/chat-side-operations.md から」の節の最後、「2026-10-14 の作り直しで…」の次）:
  - 「返答の最後に、『未完了の指示』…」の項の末尾「（2026-10-12・10-14 の決定）」
  - 「『マージしてよい』にスクリーンショットが添付されているとき」の項の末尾「（2026-10-12 の決定）」
  - 「コミットSHAを固定して書かない」の項の例「（`git show <sha>:<path>` など）」
  - 「止まる条件と検証の指定」の「承認済み」の条件の項の例「〈入口のカードの順・`search.json` の順など〉」
  - 同節の許すずれの項の例「例: 直すは ±3件まで、作る・消すは見込みと同じ。」（理由の「無いと進むかを受け手が決める」は残した）
  - 規則そのもの・issue 番号・止まる条件は移していない
- 手順3（測り直し）: github-actions.md 34,439 B（+413）・chat-routines.md 11,914 B（+268）・chat-side-operations.md 22,192 B（−11、純増なし。警告域 26,624 B の下）・handover-archive-2026.md 101,062 B（+979）
- 決定（表）を docs/decisions/automation.md に `## 2026-10-16（CHAT-1016-ACF-25）` として足した
- 差分は docs/notes の4文書・docs/decisions/automation.md・このログだけ（成果物は be69231e5）
- マージ: push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真であることを確かめて `git push origin work/1016-acf-notes:cloudflare`（a9421dee9..8529f02f1）

## 報告

- 状態: 完了
- ブランチ: work/1016-acf-notes
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-ACF-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-acf-notes
- 確認用URL: なし
- マージ: 済（8529f02f1）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8529f02f）: https://github.com/retroeater/mj-logs/tree/main/guide/8529f02f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8529f02f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8529f02f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8529f02f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8529f02f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8529f02f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8529f02f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
