# CHAT-1009-STL-01

- 着手日時: 2026-10-09
- 対象issue: #475
- ブランチ: work/1009-stl
- 着手時HEAD: 4e7c1a8d

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ以外」「別名」の使われていない登録を #475 に並べて出すための調査（利用先の洗い出し・概要欄の修正が届くか・今の件数の試算。コード・シート・issue は変えない） Chat-Ref: CHAT-1009-STL-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1009-stl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-stl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-stl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
YouTube の概要欄が直ったときなどに、「連盟プロ以外」「別名」に残った、もうどこでも使われていない登録を片付けたい。実装の指示を書く前に、「使われている」を判定するのに見るべき利用先、概要欄の修正が取り込みに届くかどうか、今のデータでの件数を確かめる。
決定（2026-10-09、平野さん）

* 「連盟プロ以外」「別名」の登録のうち、もうどこでも使われていないものを、/live の未登録の名前のチェック（#475）とあわせて調べるようにする。目的は、YouTube の概要欄が直ったときなどに登録を片付けること
* 「別名」は区分が `訂正` の行だけを対象にする（`登録名変更` の行は対象外）
* 結果は #475 の毎日のコメントに並べて書く。手動実行でも出せるようにする

前提（チャット側。平野さんの決定ではない）

* 仕組みは一覧を出すだけで、行を消すのは平野さんの手作業（シートを自動で消さない）。「連盟プロ以外」の行には X ID・X画像URL・所属を手で入れているため
* 「使われている」の判定は /live の概要欄だけでなく、これらのタブを読むすべての利用先を横断する。ガイド文書から読める利用先は次のとおり（すべて要確認。コードで全部を洗い出す）: /live（`scripts/lib/live.py`・`names.py` の `NameBook`、層2 `lib/live_extract.py`）、title/（`generate_title_pages.py`）、saikyo/（`generate_saikyo_pages.load_rows()`、`check-saikyo-unregistered.yml`、`collect_saikyo_images.py`）、鳳凰戦 順位変動（`generate_houou_race.py`）、放送対局の予定表・カレンダー（`sync_live_calendar.load_name_fixer()`）、books/（凍結中）
* 「別名」の `訂正` の行は、その変換前の名前がどこかのデータに現れるときだけ「使われている」。「連盟プロ以外」の行は、その名前がどこかのデータに直接現れるか、使われている「別名」の変換後になっているときに「使われている」と読んでいる（`登録名変更` の行の変換後になっているだけの場合の扱いは、手順3で案を出す）
* 概要欄を直しても、層1が取り込み済みの動画の概要欄を読み直さなければ、古い読み違いが【1】【2】に残り、未使用として出てこない（要確認。手順2）
* 毎日の取り込みが他のページのシートまで読むようになると、取り込みが失敗する要因が増える。未使用の検査が失敗しても、取り込み・【2】【3】の書き込み・未登録の名前の知らせは止めない形がよいと考えている（実装の指示で決める）
* 名前の検査は docs/notes/live-page-design.md「1-5」、層2と #475 の知らせは docs/notes/live-channel-write.md、隠れた行の検査は docs/notes/static-generation.md（`check_not_filtered()`）

手順

1. 洗い出し（書かない）: 同じ論点の issue（「別名」「連盟プロ以外」「未使用」「使われていない」「#475」などで。クローズ済みとコメントを含む）と、未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）で層2・`names.py`・#475 の知らせに触れているものを調べる。次に、コード全体（`scripts/`・`.github/workflows/`）で「連盟プロ以外」「別名」のタブ・`NameBook`・`load_name_fixer` などを読む・import する所を全部挙げ、利用先ごとに「照合する名前の出どころ（スプレッドシート・タブ・列、表示 N や候補でない行も含めて読むか）」を表にする。#475 の本文と直近のコメントの形も読む
2. 概要欄の修正が届くか（書かない）: 層1（`update-live-channel.yml`）が、取り込み済みの動画の概要欄を読み直すかをコードと直近の実行のログで確かめる。読み直さないなら、平野さんが概要欄を直したあとに【1】【2】へ届かせる方法（手動実行の入力など）があるかを書く。【3】に手で入れた名前が「使われている」の判定に入るかも書く
3. 今の件数の試算（シートに書かない）: 手順1の利用先をすべて読み、今のデータで使われていない「連盟プロ以外」の行と「別名」の `訂正` の行を一覧にする（行番号・名前〈別名は変換前・変換後〉・所属団体と所属補足、または区分と備考）。読むタブはそれぞれ2回読み、行数が同じことを確かめる。参考として、使われていない `登録名変更` の行の件数も書く（一覧は要らない）。そのうえで、実装の案を報告の「判断が必要なこと」に書く: #475 のコメントへの載せ方（未使用の一覧が変わった日だけ書くか等）、手動実行の入力、検査が失敗したときの扱い、`登録名変更` の行の変換後にだけなっている「連盟プロ以外」の行の扱い、直す文書（docs/notes/live-channel-write.md・live-page-design.md「1-5」・#475 の本文など）

止まる条件

* 同じ論点の open issue か、層2・`names.py`・#475 の知らせに触れる未マージのブランチがある（着手せずに報告する）
* 読むタブを2回読んで行数が違う（件数を書いて止まる）
* 手順1で、名前の出どころが実行時まで決まらない利用先（外部のページから名前を受け取る等）があっても止まらない。その利用先を表に書き、手順3の試算では「使われている」とみなさなかった場合と、みなした場合の差を書く
* コード・シート・issue を変えたくなっても変えない（この指示は調査だけ）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-STL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-STL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複なし（`git log --all --grep`）。識別子 `STL` は全ブランチのコミット・`docs/logs/` の履歴に無い。クローンは浅くない
- `work/1009-stl` はローカル・リモートとも無し → `git checkout -b work/1009-stl origin/cloudflare`。1回目は auto モードの分類器に `[Modify Shared Resources]` で拒否され、ターミナルで報告して止まった。平野さんが同じコマンドを許可する返答を貼り、1回だけ実行し直して成功した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 手順0: このログの「指示」欄の末尾は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- issue の着手中コメントは書かない（指示が「issue は変えない」調査のみで、コードも触らないため。CLAUDE.md「issueの着手ルール」は「コードを触る前に」）

### 手順1 前半: 重なりの確認（止まる条件に当たらない）

- issue: 「別名」「連盟プロ以外」「未使用」「使われていない」「訂正 登録名変更 削除」などで検索し、open の全 issue の題（更新順の先頭100件）も見た。
  同じ論点（使われていない登録の洗い出し・片付け）の issue は無い。近いもの: #475（この論点の行き先、常設）、#396・#397（「連盟プロ以外」のかな・所属・X を埋める。open だが別論点）、#431・#500（closed）
- 未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）: work/1008-dic（docs/logs のみ）、work/1008-hou（houou/ の生成。`scripts/generate_houou_race.py` を変えるが、名前の辞書を読む部分の差分に「別名」「連盟プロ以外」「NameBook」は無い）、work/1009-wkr-13（docs/logs のみ）、work/1009-stl（このログ）。
  層2・`names.py`・#475 の知らせに触れるものは無い
- #475: 本文は bot（github-actions）が作った最初の一覧（173名）。コメントは bot の差分の知らせ（「未登録の名前（読み違いを含む）: N名 → M名」＋「新しく出た名前」「消えた名前」＋案内の文＋実行ログのリンク）と、人（Claude Code）の調査のコメント2件。直近は 10/8 19:03 UTC の「1名 → 0名」（根越英人が消えた）

### 手順2: 概要欄の修正が届くか

- 層1（`scripts/fetch_live_channel_raw.py`）: 毎日の `--mode new` は新着と配信予定・配信中だった動画だけを取り直す。
  **週1回（水曜 JST、`VERIFY_WEEKDAY: '3'`）の `--mode verify` が既知の全動画を取り直し、取得日時以外の項目（タイトル・概要欄など）が最新の行と違えば取り直した行を追記する**（`plan_verify()`・`changed_keys()`。上限 `CHANGED_LIMIT` 200本、超えたら追記せず止まる）
- 実測: 10/7（水）の run 37536913863（schedule）で verify が動き、「既知の動画: 14126本、videos.listで取れた 14126本」「値が変わった: 2本 項目別 {'長さ': 2}」で2行を追記した
- 層2（`write_live_channel_candidate.py`）は毎日、層1の各動画の最新の行（`load_latest_videos`）から【2】を全件作り直す。したがって **概要欄の直しは、次の水曜の実行で層1に入り、その日の層2で【2】に届く**（最長で約1週間遅れる）。【1】も全件を書き写すので同じ日に届く
- すぐ届かせる方法: 手動実行（`update-live-channel.yml`、入力 `apply` と `verify` を真）。verify は手動の入力 `verify` で曜日に関係なく動く（ワークフローの「実行するかどうかを決める」で `verify="${{ inputs.verify }}"`）。値が変わった動画が200本を超えるときは `allow_many_changes`
- 【3】: 追記は補正の列を空欄で足すだけで（`append_live_layer3_candidates.py`）、生成は `live_layer3.merge_records()` で列ごとに「【3】が空欄なら【2】、`EXPLICIT_BLANK` なら空、それ以外は【3】」を使う。平野さんが【3】の対局者・実況・解説に手で入れた名前は /live・title/ の生成に入る。
  **#475 の未登録の検査（層2）は【2】だけを見ており【3】を見ない**。「使われている」の判定には【3】の補正の列（と、それが空欄の行の【2】）を入れる必要がある（手順1の表で扱う）

## 報告

- 状態: 作業中
- ブランチ: work/1009-stl
- ログ: https://github.com/retroeater/mj/blob/work/1009-stl/docs/logs/CHAT-1009-STL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-stl
- 確認用URL: なし
- マージ: 未
- issue: #475
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b577508d）: https://github.com/retroeater/mj-logs/tree/main/guide/b577508d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b577508d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eabe7134.md
