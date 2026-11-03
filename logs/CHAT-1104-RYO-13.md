# CHAT-1104-RYO-13

- 着手日時: 2026-11-04
- 対象issue: #657（コメントのみ）
- ブランチ: work/1104-ryo13
- 着手時HEAD: 5c74b8644

## 指示

【Claude作成】Claude Code 向け指示：r/world-data.json に出ている路線の名前「家族線」を書き出さないようにし、cloudflare へマージする（未公開のまま）
Chat-Ref: CHAT-1104-RYO-13
マージ: 承認済み（チャットで。2026-11-04 に平野さんが、変わる範囲〈r/world-data.json の L05 の名前が消えることだけで、r/ の見た目は変わらない〉を示した問いに単独で「マージしてよい」と回答。下の止まる条件で機械的に確かめる）
貼る時機: いつでも（CHAT-1104-RYO-12 は完了・マージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-ryo13 を origin/cloudflare から作る（リモートにすでにあれば止まる。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
RYO-12 のマージ後、チャット側が本番の `https://ryoei.pro/r/world-data.json` を開いたところ、路線 L05 の名前「家族線」が文字で出ていた（灯りの駅 S20・S28 の名前は出ていない）。家族は言葉で出さない決定に合わせ、灯りの駅を含む路線の名前を書き出さないようにする。

### 決定（2026-11-04、平野さん）
- Q6: 灯り（家族）の駅を含む路線は、名前を公開されるファイルに書き出さない（色と駅の並びは残す）
- Q7: 確かめがすべて通れば、判断待ちにせずそのまま cloudflare へマージしてよい
- 「基本」の「数字: 子ども」は、平野さんがシートで「4人」に直した（年齢を出さない）。本番に「4人／子ども」と出ていることをチャット側が見た

### 前提（チャット側。平野さんの決定ではない）
- 書き出さない形: 路線の JSON の `name` を空にするか、キーごと除く。どちらにするかは、RYO-08 で駅（灯りの駅は `name` を除き `id` と `look` だけ）にした形にそろえる。シートの「路線」は変えない
- 検査: RYO-08 で足した「家族の語が `r/` の生成物に出ない」検査に、灯りの駅を含む路線の名前が書き出されないことを足す。検査の語の一覧に「家族」が無ければ足す。CLAUDE.md「判断・作業の原則」のとおり、直す前のコードでこの検査が落ちることを先に確かめる
- ほかに灯りの行・駅に結びつく文字（出来事の題・説明、素材の題など）が公開されるファイルに出ていないかも、同じ検査で確かめる。見つかったら同じ形で書き出さないようにし、ログに書く（決定 Q6 と同じ趣旨の範囲。それ以外の見た目の変更はしない）
- 再生成は `python3 scripts/regenerate.py ryoei_pages` か `regenerate-page.yml` の手動実行（鍵は Actions にしか無いので、手元で R_良栄 に届かなければ手動実行で作り直す）。`scripts/lib` を変えた場合は、CLAUDE.md の共有の関数の決まりのとおり全ページの再生成の差分を確かめる
- 本番の確かめ（UA に `mj-internal`、`?v=<未使用の値>` 付き）: `https://ryoei.pro/r/world-data.json` が 200 で、「家族」の語と L05 の名前が無く、L05 の色と駅の並びは残っていること。`/r/` が 200 で「数字で見る」に「4人」と「子ども」があり、「9〜18」が無いこと。15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする
- #657 にマージした旨をコメントする（閉じない）

## 手順
1. #657 に着手中コメントを書く。直す前のコードで足す検査が落ちることを確かめてから直し、`r/` を作り直す
2. `python3 -m unittest discover -s scripts/tests`・`scripts/check_asset_limits.py` を流し、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする
3. check-run と本番（上の「本番の確かめ」）を確かめ、#657 にコメントする

## 止まる条件
- テスト・`check_asset_limits.py`・手動実行が通らない、R_良栄 の検査に当たる行がある
- 直した後も、公開されるファイル（`r/index.html`・`r/world-data.json`）に灯りの行・駅に結びつく文字が残る
- `r/index.html` に、灯りの文字を消したこと以外の差分が出る（シートの変更で説明できるものは除き、ログに書く）
- マージ後の check-run の失敗が今回の変更によるもの（無関係な失敗は原因を報告に書いたうえで残りの手順を進めてよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RYO-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-RYO-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 同じ Chat-Ref のコミットなし。`RYO` は既存の識別子。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はそろっている。「マージ: 承認済み（チャットで）」がある
- 0: 「指示」欄の末尾はこの指示文の最後の行と一致
- `work/1104-ryo13` はローカル・リモートとも無かったので `git checkout -b work/1104-ryo13 origin/cloudflare`（5c74b8644）

### 直す前の確かめと直し（手順1）

- 本番と同じ `r/world-data.json`（origin/cloudflare）: L05 の名前「家族線」が出ている（駅の並び S18・S20・S28・S29、S20・S28 が灯りの駅）。`r/index.html` に「家族」は無い。灯りの駅に置かれた出来事で表示＝Y のものは無い
- 検査を先に足し、直す前のコードで落ちることを確かめた: テストのシートの写しに灯りの駅を含む路線「テスト用の家族線」（L05、S36・S20）を足し、
  路線の書き出しで L05 に `name` が無いこと・公開のファイルに「家族」の語と灯りに結びつく文字が無いことを確かめるテストにした → 直す前のコードで `test_next_scene_data_is_written`・`test_light_texts_are_not_written` の2件が FAIL
- 直し（e4f010ea0）:
  - 灯りの駅を含む路線は `name` のキーを除き、`id`・`color`・`stations` だけを書く（灯りの駅を `name` を除いて書いた RYO-08 の形にそろえた。シートの「路線」は変えない）
  - 同じ趣旨で、灯りの駅に置かれた出来事は、見せ方が文字でも ID・年・見せ方だけを書く（今のシートには該当の行が無いので出力は変わらない。テストで確かめた）
  - 生成の後の照合（`check_hidden`）の対象に、灯りの駅を含む路線の名前と、灯りの駅に置かれた出来事の題・説明を足した（出ていたら止める）
  - テストの家族の語の一覧に「家族」を足した。docs/notes/ryoei-world.md を直した
- `python3 -m unittest discover -s scripts/tests` OK、`python3 scripts/check_asset_limits.py` OK
- r/ の作り直し（run 657、e4f010ea0、`target_page: ryoei_pages`）: success、ワークフローが be7866cea をコミット。`r/index.html` は変わらない。`r/world-data.json` の差は L05 の `name` が消えたことだけ
- 作り直した後の `r/index.html`・`r/world-data.json`: 「家族」0件、家族の語（結婚・長男・長女・次男・次女・三男・離婚・妻・息子・娘）0件
- `scripts/lib/ryoei_book.py` を変えたので、全ページの新旧の比べ（`dry_run`・`all`・`compare_ref: origin/cloudflare`）を流す。`ryoei_book` を読むのは `generate_ryoei_pages.py` だけ

### レビュー（サブエージェント、1回。対象は e4f010ea0 と r/ の差）

- 確かめたこと（レビューの側）: テスト全件 OK。直す前のコード（0d1ae1b3a の scripts/）に新しいテストを当てると、直しに関係する4件が落ちる。生成物の差は L05 の `name` が消えただけで、色と駅の並びは残る。駅の並びの書式のゆれ（空白・全角空白は strip で正しく判定、空の要素・読点・小文字は参照切れの検査で止まる）。JSON-LD は基本と SNS だけで経路なし

| 指摘 | 扱い |
|---|---|
| 灯りの駅に置かれた出来事（見せ方＝文字）を指すスクリーン・看板の素材が表示＝Y だと、題が出演（HTML・JSON）に出て、照合でも止まらない（要修正） | 直した（9bc2a924c。灯り扱いの出来事〈見せ方＝灯り、または灯りの駅に置かれた出来事〉の集合を parse で1回作り、素材の止め・出来事の書き出し・照合で使う。照合にその素材の題〈写真パネル・表示＝N を含む〉を足した。止まることのテスト） |
| 灯り扱いの判定と駅の並びの分割が複数か所にある | 直した（`light_events` と `line_stations()` を共用） |
| docstring・エラー文が古い | 直した（モジュールの docstring・parse の返り値・check_hidden の docstring とエラー文「灯りに結びつく文字が公開のファイルに出ています」） |
| 路線の名前が漏れたら止まることのテストが無い | 直した（灯りでない駅の名前に「テスト用の家族線」を入れると L05 で止まるテスト） |
| check_hidden の照合が JSON-LD の逃がしの形を含まない | 見送り（JSON-LD は基本と SNS だけで、今は経路が無い。JSON-LD に出来事を載せるときに足す） |

- 直した後: `python3 -m unittest discover -s scripts/tests` OK、`check_asset_limits.py` OK。実データで r/ を作り直して、検査で止まらず出力が変わらないことを確かめる

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a7323386）: https://github.com/retroeater/mj-logs/tree/main/guide/a7323386

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dfabc859.md
