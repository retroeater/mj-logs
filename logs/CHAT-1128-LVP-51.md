# CHAT-1128-LVP-51

- 着手日時: 2026-11-28
- 対象issue: なし
- ブランチ: work/1128-lvp
- 着手時HEAD: 2e8b2be91

## 指示

【Claude作成】Claude Code 向け指示：CLAUDE.md「判断・作業の原則」のテストの行を実態に合わせ、docs/notes/ だけの push で check-assets が起動するかを実測する
Chat-Ref: CHAT-1128-LVP-51
マージ: 承認済み（チャットで）。ただし「止まる条件」に当たれば止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は状態と公開ログの URL の2行）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1128-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1128-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1127-LVP-50 の申送りで残った2つを片付ける。(1) CLAUDE.md「判断・作業の原則」のテストの行が実態と合っていないのを直す。(2) 8d408dc2e（check-assets.yml の paths に docs/notes/ を戻した）の後、docs/notes/ だけを変えた push はまだ一度も無く、その push で check-assets が起動するかは実測されていない。この指示の docs/notes/ の変更を単独で push して確かめる。

### 決定（2026-11-28、平野さん）
- CLAUDE.md のテストの行を実態に合わせて直す
- この指示はマージしてよい（止まる条件に当たらなければ）

### 前提（チャット側。平野さんの決定ではない）
- 今の CLAUDE.md「判断・作業の原則」の行（チャット側が mj-logs の guide/241f98c7 で読んだ）: 「テストは`scripts/tests/`（unittest、標準ライブラリのみ）。実行は`python3 -m unittest discover -s scripts/tests`。毎朝の run-daily の`check-meibo.yml`も実行する。」
- 実態（チャット側が REST で cloudflare の `.github/workflows/` を読んだ）: `python3 -m unittest discover -s scripts/tests` を回すのは check-assets.yml・fetch-gsc.yml・sync-dojo-calendar.yml・update-live-channel.yml で、いずれも前に `pip install ... google-auth requests` がある。regenerate-page.yml は `test_title_redirects.py` だけを回す。check-meibo.yml には `unittest` の文字が無かった（呼び出し先の共通部品 `setup-python` の `test: 'true'` 経由かは見ていない。要確認）
- テストが google-auth・requests を使う（import する）ことは、LVP-50 の申送りの記述でチャット側は import を見ていない（要確認）
- 直し方の案（実物に合わせて変えてよい）: CLAUDE.md の行は「標準ライブラリのみ」をやめて依存（google-auth・requests）を書き、CI で回るワークフローは列挙せず docs/notes/github-actions.md への参照にする（CLAUDE.md「テスト・hook・ワークフローで確かめるようにした規則は…参照だけ残す」、#492）。CI でどこが回すかの一覧は docs/notes/github-actions.md の適した節に1〜2行で置く。CLAUDE.md の上限（check-assets.yml）を超えない、純増させない
- check-assets.yml の push の paths（8d408dc2e）は `'**'`・`'!docs/**'` の後に docs/handover.md 等と docs/notes/ を戻している、と docs/notes/github-actions.md の表にある。docs/notes/ の戻しの行を実物で確かめていない（要確認）

## 手順
1. 上の「前提」の実態（各ワークフローの行、check-meibo.yml が unittest を回すか、テストが import するもの、check-assets.yml の paths）を実物で確かめ、表にしてログの `## 経過` に書く。CLAUDE.md・docs/notes/github-actions.md の該当の節の今の内容を読む。前提と食い違えば、食い違いが直し方の案を変えるときだけ止まる（変えないなら実物に合わせて進め、ログに書く）
2. docs/notes/github-actions.md の変更（と作業ログ）だけを1つのコミットにし、**ほかの変更を含めずに単独で push する**（作業ログの先行 push とも分ける。この push の before..after で変わるファイルが docs/notes/ と docs/logs/ だけであることを確かめる）。その SHA で check-assets.yml の run が push で起動したか（起動したなら run ID と結論、起動しなければ 15 分待って無いこと）をログの `## 経過` に書く
3. CLAUDE.md のテストの行を直して push し、check-assets.yml の run の成功を確かめる。直した前後の行をログに引用する。cloudflare へマージする

## 止まる条件
- 同じ論点（CLAUDE.md のテストの行・check-assets の paths）の open issue か、他セッションの着手中コメントがある
- 手順2で check-assets が起動しなかった（設計では起動する。起動しないなら paths の書き方の問題なので、直さずに止まって報告する。マージしない）
- check-assets.yml の run が失敗した、または CLAUDE.md・docs/notes/github-actions.md が上限・警告域に入った
- 変更が CLAUDE.md のテストの行の付近・docs/notes/github-actions.md の1〜2行・作業ログ以外に及ぶ
- 未マージの work/ ブランチが CLAUDE.md のテストの行か docs/notes/github-actions.md の同じ節を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1128-LVP-51.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット無し・0章（指示欄の末尾が指示文の最後の行と一致）。雛形の行の欠け無し。同じ論点（CLAUDE.md のテストの行・check-assets の paths）の open issue は無い（「check-assets」「標準ライブラリのみ」などで検索。当たったのは #292・#565・#492 などの別の主題）。対象の issue は無いので着手中のコメントは書いていない

### 1. 実態（cloudflare の実物、2026-11-28）
| 項目 | 実物 |
|---|---|
| `scripts/tests` の全件を直接回すワークフロー | check-assets.yml・update-live-channel.yml・fetch-gsc.yml・sync-dojo-calendar.yml（どれも前に `pip install ... google-auth requests`。sync-dojo-calendar は anthropic も） |
| 共通部品 `setup-python` の `test: 'true'` 経由で回すワークフロー | check-meibo.yml（packages: google-auth requests）・fetch-redirect-counts.yml・sync-birthday-calendar.yml。check-meibo.yml に `unittest` の文字は無いが、`.github/actions/setup-python/action.yml` の `test` が `python3 -m unittest discover -s scripts/tests` を回す（前提の「要確認」の答え） |
| regenerate-page.yml | `test_title_redirects.py` だけ（全件ではない） |
| テストが import するもの | テストのファイルで `requests` を import するのは test_inspect_gsc_urls.py（無ければその検査を飛ばす）と test_net_retry.py の2つ。`google` を import するテストは無い。scripts の側は google・requests を関数の中で import する（一番上での import は無い） |
| 依存の実測 | 標準ライブラリだけの venv: 2,327件中 エラー4件（test_net_retry の GoogleApiTest）・skip 8件。requests だけを入れた venv: 2,327件 OK（skip なし）。google-auth は要らない |
| check-assets.yml の push の paths | `'**'`・`'!docs/**'`・`docs/handover.md`・`docs/notes/chat-side-operations.md`・`docs/instruction-template.md`・`docs/notes/**`（前提のとおり。`docs/notes/**` は 8d408dc2e で足した） |

- 前提との食い違い: 「テストが google-auth・requests を使う」は、実際は requests だけ。直し方の案は変えない（CLAUDE.md は依存の requests を書き、CI の一覧は docs/notes/github-actions.md への参照にする）
- 未マージの work/ ブランチで CLAUDE.md・docs/notes/github-actions.md を変えるもの: work/1101-prp-pub（表の check-image-links.yml の行）・work/1103-ryo5（表の fetch-x-posts.yml の行）が docs/notes/github-actions.md の「ワークフローの一覧」の表を変えている。今回の「共通の部品」の節とは別の箇所。CLAUDE.md を変えるブランチは無い

### 2. docs/notes/ だけの push で check-assets が起動するか
- docs/notes/github-actions.md「共通の部品（`.github/actions/`）」の `setup-python` の行の後に1行（全件のテストを回すワークフローと依存）を足し、作業ログと一緒の1コミット de491b5df にして単独で push した。push の before..after（583034d40..de491b5df）で変わったファイルは `docs/logs/CHAT-1128-LVP-51.md` と `docs/notes/github-actions.md` の2つだけ
- **check-assets は push で起動した**: run 46093466952（event push、head de491b5df）、success。8d408dc2e で足した `docs/notes/**` が効いている

### 3. CLAUDE.md のテストの行（「判断・作業の原則」）
- 前: 「テストは`scripts/tests/`（unittest、標準ライブラリのみ）。実行は`python3 -m unittest discover -s scripts/tests`。毎朝の run-daily の`check-meibo.yml`も実行する。」
- 後: 「テストは`scripts/tests/`（標準ライブラリと requests）を`python3 -m unittest discover -s scripts/tests`で回す（CI は docs/notes/github-actions.md「共通の部品」）。」
- CLAUDE.md は 23,299 → 23,298 バイト（純増なし。check-assets の警告域 25,600 に当たらない）。CI で回すワークフローの一覧は docs/notes/github-actions.md に置き、CLAUDE.md は参照だけにした（#492）
- push（CLAUDE.md を直した 214542717）の check-assets: run 46093647242（push）success。テスト 2,327件 OK
- 変更したのは CLAUDE.md のテストの行・docs/notes/github-actions.md の1行・作業ログだけ
- セルフレビューのサブエージェントは使っていない（docs だけの変更で、実態は表のとおり実物で確かめたため）

## 報告

- 状態: 完了
- ブランチ: work/1128-lvp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1128-LVP-51.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1128-lvp
- 確認用URL: なし（docs のみ）
- マージ: 済（このログのコミットを含む先頭を cloudflare へ push）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d9066093）: https://github.com/retroeater/mj-logs/tree/main/guide/d9066093

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9066093/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9f9a15fb.md
