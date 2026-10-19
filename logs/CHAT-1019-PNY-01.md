# CHAT-1019-PNY-01

- 着手日時: 2026-10-19
- 対象issue: なし
- ブランチ: work/1019-pny
- 着手時HEAD: d1ca57938

## 指示

【Claude作成】Claude Code 向け指示：CLAUDE.md「コード規約」に、既存の形の再利用・新規依存の禁止・簡略化の印・検査1本の4項目を足す
Chat-Ref: CHAT-1019-PNY-01
マージ: ドキュメントのみ（CLAUDE.md・docs/decisions・ログ）なので完了報告のうえ cloudflare へ入れてよい。足した後の「コード規約」節の全文をログの `## 経過` に貼る
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-pny の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-pny を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-pny origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Claude Code の自律開発で、不要な抽象化・新しい依存・雛形が増えないよう、受け手側の規則を CLAUDE.md「コード規約」に足す。外部のプラグイン（DietrichGebert/ponytail）の導入は見送り、その考え方のうち機械的に確かめられる4点だけをガイド文に置く（2026-10-19、平野さん）。

### 決定（2026-10-19、平野さん）
- ponytail（Claude Code プラグイン）は導入しない
- 代わりに、次の考え方を CLAUDE.md か docs/instruction-template.md に数行で明文化する: 既存の形があればそれを使う／新規依存を足さない／簡略化した箇所には印（上限と次の一手）を残す／分岐・パーサーには検査を1本残す

### 前提（チャット側。平野さんの決定ではない）
- 置き場所は CLAUDE.md「コード規約」（受け手側の規則のため）。docs/instruction-template.md には足さない（mj-logs の guide/7c82be63 の写しで 14,325 バイトと警告域 14KB を超えており、チャット側の雛形は受け手の規則の置き場所でもない）
- CLAUDE.md は同じ写しで 23,801 バイト（上限 28KB・警告域 26KB）。足す量は 500 バイト前後の見込み（要確認。作業する worktree の上で測る）
- 足す行の文面の案（既存の行と同じ趣旨なら置き換え・拡張してよい。「変更行数は最小にする」「外部ドメインへの依存を増やさない」「テストは `scripts/tests/`」が近い）:
  - 書く前に、既存の形（同じ型のページ・`scripts/` の共通関数・定数・既存の生成の仕組み）で足りるかを確かめ、あればそれを使う。実装が1つだけの抽象化・「後のため」の雛形・変わらない値の設定化は作らない（増えた形が次の作業の読む量になる）
  - 新しい依存（npm・pip のパッケージ、CDN、外部 API、新しいワークフロー）は足さない。要るときは足さずに止まって報告する（判断は平野さん）
  - 上限を分かって選んだ簡略化（素朴な走査・固定値・一部の場合だけの対応など）には、コードに `簡略化:` のコメントで上限と次の一手を1行で残す（例: `# 簡略化: 全件を線形に走査。選手が1万人を超えたら索引にする`）
  - 分岐・ループ・解析（文字列・日付・HTML の読み取り）・金銭・セキュリティに関わるロジックを足す・変えるときは、`scripts/tests/`（workers は `workers/<名前>/test/`）に検査を1本残す。自明な一行には要らない
- 削らない側（信頼境界の検証・データ損失を防ぐエラー処理・セキュリティ・アクセシビリティ）は、既存の docs/notes/security.md・a11y-manual-check.md があるので新しい行は足さない
- 決定の記録先は docs/decisions/operations.md の見込み（分野は docs/decisions/README.md の一覧で受け手が決めてよい）

### 知見 → 選んだ層 → 理由（CLAUDE.md「Chat-Ref」節の求める表）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 既存の形で足りるかを先に確かめ、頼まれていない抽象化・雛形を作らない | ガイド文（CLAUDE.md「コード規約」） | 「足りるか」「頼まれていないか」は書く途中の判断で、テスト・hook（計算的）でも着手前の確認手順（推論的）でも判定できない |
| 新しい依存を足さず、要るなら止まる | ガイド文 | 計算的センサー（新しい import・CDN URL・パッケージ定義の検知）は作れるが、対象が Python・JS・workers・ワークフローに散り、今の件数では費用に合わない。増え始めたら hook に落とす前提でガイド文に置く |
| 簡略化した箇所に上限と次の一手の印を残す | ガイド文 | 印の有無は grep で数えられるが、「簡略化したか」は機械的に決まらない。印を統一の語にしておけば後で `grep 簡略化:` で台帳が作れる |
| 分岐・解析・金銭・セキュリティのロジックには検査を1本残す | ガイド文 | 対象（分岐・解析など）の判定が機械的に決まらない。「テストの置き場所」は既にガイド文にあり、同じ節に並べる |

## 手順
1. 同じ論点（コード規約の追加、YAGNI・不要な抽象化・新規依存の禁止・簡略化の印・テストの規則、ponytail）の issue を、クローズ済みも含めて検索し（`gh issue list --state all --search "<語>"` を複数の語で）、あれば止まって報告する。あわせて `git branch -r --no-merged origin/cloudflare` の各ブランチが CLAUDE.md「コード規約」節の行を変えていないかを確かめる
2. CLAUDE.md「コード規約」節の今の内容を読み、上の「前提」の文面の案を元に足す（4項目で、規則と理由の一句だけ。既存の行と同じ趣旨の部分は置き換え・拡張してよく、その処理を `## 報告` に書く）。足す前後のサイズを作業する worktree の上で測ってログに書く。足した後の節の全文を `## 経過` に貼る
3. 記録: 上の「決定」を docs/decisions の該当の分野に足す（CLAUDE.md「作業ログ」節のとおり）。マージは冒頭の「マージ:」の行のとおり

## 止まる条件
- 手順1で同じ論点の issue（クローズ済みのコメントの決定を含む）が見つかった
- 追記先の既存の行と矛盾し、どちらが正か平野さんの判断が要る（同じ趣旨があるだけなら止めず、置き換え・拡張してよい）
- 足した後の CLAUDE.md が警告域 26KB（26,624 バイト）を超える
- 変更が CLAUDE.md・docs/decisions・ログのほかに出る
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが CLAUDE.md「コード規約」節の同じ行を変えている、または取り込みで衝突する（同じファイルの別の節の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-PNY-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-PNY-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0章: ログの「指示」欄の末尾が指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: すべてある
- `git fetch --unshallow origin` の後、`git log --all --grep=PNY` と `docs/logs/` の履歴に PNY の使用なし
- `work/1019-pny` はローカル・リモートとも無し → `git checkout -b work/1019-pny origin/cloudflare`（起点 d1ca57938）

### 手順1: 同じ論点の issue・未マージブランチ

- GitHub MCP `search_issues`（意味検索。状態を問わず）: 「ponytail plugin」「コード規約 不要な抽象化 YAGNI 雛形」「新規依存 依存を足さない パッケージ 追加禁止」「簡略化 コメント 印 上限 次の一手」「テストの規則 検査を残す 分岐 パーサー unittest」「コード規約 ルール追加」「外部依存 増やさない」は0件。
  「Claude Code プラグイン 導入」は #311（応答言語の固定）、「テストを書く規則」は #565（文書参照の検査テスト）の各1件で、どちらも別の論点。
  検索自体が動くことは「作業ログ Chat-Ref」で41件返ることで確かめた。REST の `/search/issues` はセッションの制限で使えない（repo スコープ外の API）
- `git branch -r --no-merged origin/cloudflare` の各ブランチ（work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-lvp・1018-log-367-migrate・1019-ouk19）は、いずれも CLAUDE.md を変えていない（`git diff --stat origin/cloudflare...<b> -- CLAUDE.md` が空）

### 手順2: CLAUDE.md「コード規約」

- サイズ: 前 23,801 バイト → 後 24,972 バイト（+1,171。見込みの500バイトより多いが、警告域 26,624 バイト未満）
- 既存の行との関係:
  - 「変更行数は最小にする」（同じ節の先頭行）: 変えずに残し、すぐ後に「既存の形を使う」を置いた（趣旨は近いが、変更の量と形の増やし方で別の規則）
  - 「判断・作業の原則」の「外部ドメインへの依存を増やさない（CSP導入を予定しているため）」: 変えずに残した。handover.md・new-page-checklist.md などが同じ見出しで参照しており、動かすと参照が切れるため。新しい行はパッケージ・CDN・外部API・ワークフローへ範囲を広げ、「止まって報告」を足した
  - 「判断・作業の原則」の「テストは`scripts/tests/`」: 置き場所と実行の規則はそちらに残し、新しい行からは参照だけにした（同じ内容を2箇所に書かない）
- `python3 -m unittest discover -s scripts/tests`: 1430件 OK

足した後の「コード規約」節の全文:

```
## コード規約
- 着手中のissueと無関係なコードは触らない。自分が書いた・変更した箇所以外にコメントを足さない。変更行数は最小にする
- 書く前に、既存の形（同じ型のページ・`scripts/`の共通関数・定数・生成の仕組み）で足りるか確かめ、あれば使う。
  実装が1つだけの抽象化・「後のため」の雛形・変わらない値の設定化は作らない（増えた形が次の作業の読む量になる）
- 新しい依存（npm・pipのパッケージ、CDN、外部API、新しいワークフロー）は足さない。要るときは足さずに止まって報告する（判断は平野さん）
- 上限を分かって選んだ簡略化（素朴な走査・固定値・一部の場合だけの対応など）には、`簡略化:`のコメントで上限と次の一手を1行残す
  （例: `# 簡略化: 全件を線形に走査。選手が1万人を超えたら索引にする`。`grep 簡略化:`で台帳にするため語を変えない）
- 分岐・ループ・解析（文字列・日付・HTMLの読み取り）・金銭・セキュリティに関わるロジックを足す・変えるときは、検査を1本残す
  （置き場所と実行は「判断・作業の原則」のテストの行）。自明な一行には要らない
- 繰り返し使う値・意味のある値（列番号、画像サイズ、URL、閾値）は定数にする。一度きりで自明な値はインラインでよい
- ネストを深くしない。早期return / continueを使う
- JSのif文は1行でも必ず`{}`を付ける
- コメントは「何を・なぜ」を短く。「どうやって」はコードに語らせる
- 人が読む文章（コメント、コミットメッセージ、応答）は最小限の語数で。賞賛・相槌は不要
- スプレッドシートのファイルは「スプレッドシート」、タブは「シート」と書く。ほかの語（ワークフロー・定期実行・本実行など）も docs/notes/glossary.md に従う（新しく書く文から）
```

### 手順3: 記録

- 「決定」を docs/decisions/operations.md に `## 2026-10-19（CHAT-1019-PNY-01）` として足した

## 報告

- 状態: 完了
- ブランチ: work/1019-pny
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-PNY-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-pny
- 確認用URL: なし
- マージ: 済（cloudflare へ fast-forward。SHA は作業ブランチの先頭と同じ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 52eae621）: https://github.com/retroeater/mj-logs/tree/main/guide/52eae621

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/52eae621/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/52eae621/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/52eae621/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/52eae621/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/52eae621/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/52eae621/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d1ca5793.md
