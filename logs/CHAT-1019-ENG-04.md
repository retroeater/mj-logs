# CHAT-1019-ENG-04

- 着手日時: 2026-10-19
- 対象issue: なし（手順2で検索）
- ブランチ: work/1019-eng
- 着手時HEAD: d09305637

## 指示

【Claude作成】Claude Code 向け指示：（ENG-03 の続き）表を入れて申送りを書く。あわせて旧スプレッドシートの読み手について ENG-02 と ENG-03 で記述が食い違う点を確かめる
Chat-Ref: CHAT-1019-ENG-04
マージ: ドキュメントのみ（docs/notes・docs/decisions・ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1019-ENG-03 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-eng の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-eng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-eng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1019-ENG-03 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1019-ENG-04` を足す。

## 目的
ENG-03 は、指示文に「知見 → 選んだ層 → 理由」の表が無かったため止まった（チャット側の書き漏れ）。表を入れて申送りを書く。
あわせて、旧スプレッドシートを読むコードについて、ENG-02 のログ（「`generate_resource_logs.py` が『ログ』シートを読む、2,706行」）と ENG-03 のログ（「`generate_resource_logs.py` は3層のスプレッドシートを読み、旧スプレッドシートを読むコードは `scripts/` に無い」）が食い違うので、どちらが正しいかを確かめる。

### 決定（2026-10-19、平野さん）
- 振り返りの知見を申送りする（「申送り」の指示）
- 旧「ログ」シートは、2026-10-19 22:26 の時点で旧スプレッドシートにまだ残っている（平野さんの申告）

### 前提（チャット側。平野さんの決定ではない）
- `chat-side-operations.md` の「場面ごとに読む文書」の表の語を広げてよい（チャット側の判断）。増えた分は、同じ文書の「純増したら、足した分と同じ量を docs/notes/ の各文書へ移す」に従って移す。移すものが見つからなければ、表の語は変えず `chat-routines.md` 側だけ広げる
- 表（ENG-03 の「表の案」をそのまま使う）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| シート・スプレッドシートを「移した・消した・廃止する」と言われたら、答える前に (a) 読むコードが残っているか（`git grep` でスプレッドシートの定数名・シート名）と (b) コピーか移動か（旧い側に残っているか）を確かめさせ、読むコードが残っているうちは消すのを待ってもらう | ガイド文（推論的センサーとしてのチャット側の手順）。`static-generation.md`「平野さんがシートを用意した・直したと言ったときのチャット側の指示」の見出しと2項目めを「用意した・直した・移した・消した」に広げ、(b) と「待ってもらう」を1文で足す。`chat-routines.md`「指示の種類ごとの確かめ」の項と `chat-side-operations.md` の表の語も同じ語に置き換える | 計算的センサー（定数名・シート名ごとの読み手の一覧を出す検査）は、シートがリポジトリの外にあり「消した」の申告をコードの側で受け取れないため、移す・消すの場面では効かない。読み手の一覧は #557 の範囲で、作るかは起票の判断 |

## 手順
1. 食い違いを確かめる: origin/cloudflare で、旧スプレッドシートの定数（ENG-02 で `generate_resource_logs.py` が読んだとしたもの）を `git grep` し、どのスクリプト・ワークフローが参照しているかを表でログに書く（ID は書かず定数名で）。あわせて ENG-02 のときの `generate_resource_logs.py`（ENG-02 のコミット時点）と今のものの読み先を比べ、ENG-02 の記述が誤りだったのか、間でコードが変わったのかを書く。旧スプレッドシートを今も読むコードがあれば、ここで止まって報告する
2. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。無ければ上の表のとおり書く（追記先の今の内容を読んでから。容量を測ってログに書く。規則と理由の一句だけ。事例は書かない）
3. `docs/decisions/operations.md` に、この申送りを書いたことを1行で足す（ENG-03 で足した行があればそれを直す）

## 止まる条件
- 手順1で、旧スプレッドシートを今も読むコードがある
- 同じ論点の open issue がある、他セッションの着手中コメントがある
- 追記先の記述と矛盾していて、どちらが正か判断が要る（同じ趣旨なら置き換えて進める）
- 未マージの work/ ブランチが追記先の同じ行・同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-ENG-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-ENG-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `CHAT-1019-ENG-04` のコミット無し
- 作業ブランチ: ローカルの work/1019-eng は origin/cloudflare の祖先。`git merge --ff-only origin/cloudflare`（d09305637）
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。ENG-03 のログの状態に ` / 続き: CHAT-1019-ENG-04` を足した

## 報告

- 状態: 作業中
- ブランチ: work/1019-eng
- ログ: https://github.com/retroeater/mj/blob/work/1019-eng/docs/logs/CHAT-1019-ENG-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-eng
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
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
