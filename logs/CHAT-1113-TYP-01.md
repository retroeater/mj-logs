# CHAT-1113-TYP-01

- 着手日時: 2026-11-13
- 対象issue: なし（新規起票の予定）
- ブランチ: work/1113-typ
- 着手時HEAD: be8d366

## 指示

【Claude作成】Claude Code 向け指示：サイト全体の誤字・表記ゆれ・食い違いの点検を、Claude API の Batch API で1回だけ行い、結果を issue に一覧で出す（直しはしない） Chat-Ref: CHAT-1113-TYP-01 マージ: 承認済み（チャットで。一度きりのスクリプト・ワークフローの追加と、使い終えた後の削除） 貼る時機: いつでも（できるだけ早く。理由は「前提」の期限） セッション: 推奨（新しい系列 TYP。RSC のセッションとは別にする。ネットワークの変更は無い） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1113-typ を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-typ origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
失効の近い API のプロモーションクレジットで、ryoei.pro の公開ページの文字を一度だけ点検し、誤字・選手名などの表記ゆれ・ページどうしの数字の食い違いの一覧を出す。直すかどうか・どこで直すか（シート・テンプレート・文書）は一覧を見て平野さんが決める。
決定（2026-11-13、平野さん）

* 案3「サイト全体の誤字・表記ゆれの点検を、Batch API で1回だけ行う」に着手する（RSC の系列のチャットで出た案。系列は TYP）
* 組織の月間支出上限を $100 から $200 に上げた（2026-11-17 に $100 へ戻す。カレンダーに登録済み）
* この指示のマージ（一度きりのスクリプト・ワークフロー）と、使い終えた後の削除を承認する。ページ・シート・テンプレートは直さない

前提（チャット側。平野さんの決定ではない）

* クレジットの状況（2026-11-13 11:07 JST、平野さんの Console のスクショ）: プロモーションクレジット $14.78 が 2026-11-14 00:00 UTC、$109.24 が 2026-11-17 00:00 UTC に失効。購入クレジット $4.81・自動チャージはオフ。組織の今月の使用 $73.73・上限 $200。RSC-15 は API を使わない
* 費用の上限の案: この指示の合計 $100。Batch API の結果は最長 24時間かかりうるので、送るのは遅くとも 2026-11-15 中（11/17 00:00 UTC の失効の前に結果が返る余裕を見る）
* モデルの案: Opus 5.5 の Batch（入力 $2・出力 $10、100万トークンあたり。料金のページで確かめ、出典と日付をログに書く）。見込みが上限を超えるときは、Sonnet 5.5 の Batch か対象を絞る案を出して止まる
* 鍵は共有の `ANTHROPIC_API_KEY`（Default のワークスペース。research-candidates と同じ残高）。セッションには鍵が無いので、API はワークフローの手動実行（`workflow_dispatch`、既定は dry-run）で呼ぶ
* 一度きりのスクリプト・ワークフロー（例 `scripts/oneoff_text_audit.py`・`.github/workflows/oneoff-text-audit.yml`）は、結果を回収して issue に書いた後、同じ指示で外す（技術的負債を残さない。外すトリガ＝結果の回収）。docs/notes/github-actions.md の「ワークフローの一覧」の表と `scripts/tests/test_workflows_table.py` は、足すときと外すときの両方で合わせる。外したことと、もう一度やるときの手順（コミットの SHA から戻す）を docs/decisions に書く
* 対象の案: cloudflare の公開中のページ（noindex のページ・リダイレクト用のページ・生成物でないテスト用のページは外す）の、見えている文字（本文・見出し・title・meta description・alt）。テンプレートで全ページに同じに出る文字（ナビ・フッタ等）は1回だけにまとめる。ページ数・文字数・まとめた後のトークン数を dry-run で数える
* 点検の観点の案:
   1. 誤字・脱字・変換ミス（日本語として明らかなもの。文体の好みは指摘しない）
   2. 選手名・大会名・会場名の表記ゆれ（同じ人・大会の書き方の違い。旧字体・空白・中黒など）。ページをまたいで見るため、全ページから人名・大会名らしい文字列の一覧を作ってまとめて渡す形にする（1ページずつでは見つからない）
   3. ページどうしの数字・日付の食い違い（同じ期・同じ選手の成績・優勝者・日付が別のページで違う）。対になるページ（大会のトップと期のページ、選手の個別ページと大会のページなど）を同じ要求に入れる
* 名前は「別名」の辞書で旧名を現在名に直して出している。旧名が残っていても、辞書の意図どおりなら指摘しない（辞書の一覧をプロンプトに入れるか、点検の後にコードで外す）
* 出力の案: 指摘ごとに（観点・ページの URL・該当の文字・直す案・確度〈高・中・低〉・直す場所の見立て〈シートのブック・タブ／テンプレートのファイル／docs〉）。直す場所はコードで分かる範囲で付ける（生成元の対応が分からなければ空でよい）。結果は issue（新規起票。題の案「ryoei.pro の文字の点検（誤字・表記ゆれ・食い違い）の結果」、ラベルは実在のものから選ぶ）に、確度の高い順・観点ごとの表で書く。多いときは issue には上位と件数を書き、全件はログ（または添付の csv）に置く
* 人の名前の扱い: 選手名は公開ページにすでに出ているものだけを扱う。ログ・issue に、公開ページに無い情報（シートだけにある列など）を書かない

手順

1. 同じ論点（文字の点検・誤字・表記ゆれ）の open issue を検索し（クローズ済みも含めて確かめる）、あれば止まって報告する。未マージの work/ ブランチが docs/notes/github-actions.md の「ワークフローの一覧」の表の同じ行を変えていないか確かめる
2. 一度きりのスクリプト・ワークフローを作り（dry-run を既定に）、試験を足し、自己レビュー（docs/notes/self-review.md）の後にマージする。cloudflare から dry-run を手動実行し、対象のページ数・まとめた後のトークン数・要求の数・モデルごとの見込みの費用をログに書く（ページの種類ごとの表で全件）
3. 見込みが $100 以内なら、Batch を送り、結果を回収する（ジョブの時間の内で待ち、終わらなければ回収だけを後で手動実行する形にする）。結果を確度・観点ごとに数え、issue を起票して一覧を書く。実額（Batch の usage から）をログに書く。回収できた後、一度きりのスクリプト・ワークフローを外してマージし、外したことを docs/decisions に記録する
   * 指摘のうち 5件を無作為に選び、実際のページを開いて正しい指摘かを確かめ、結果をログに書く（誤った指摘の割合の目安にする）
   * 「判断が必要なこと」に、指摘を直す進め方の案（直す場所ごとの件数、平野さんの手作業〈シート〉と Claude Code の作業〈テンプレート・docs〉の分け方）をメリット・デメリット・推奨つきで書く

止まる条件

* 同じ論点の issue がある
* dry-run の見込みが $100 を超える（対象の絞り方・モデルの変え方の案と、それぞれの見込みを書いて止まる）
* 2026-11-15 23:59 JST までに Batch を送れない見込み（理由を書いて止まる）
* API の残高不足・認証・支出上限のエラーが出た（調べ直さずに止まり、エラーをそのまま書く）
* 差分が次の範囲を超える: 一度きりのスクリプト・ワークフロー・試験、docs/notes/github-actions.md の表と `scripts/tests/test_workflows_table.py`、docs（decisions・ログ）。ページ・生成物・テンプレート・ほかのワークフローが変わったら止まる（マージしない）
* 結果を回収できない（Batch が失敗・期限切れ）。このときは一度きりのスクリプト・ワークフローを外さずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-TYP-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-TYP-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-11-13）

- 0章: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット・TYP の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-TYP-'` と `docs/logs/CHAT-*-TYP-*.md` の追加履歴とも 0件（`git fetch --unshallow` の後）
- 作業ブランチ: `work/1113-typ` はローカル・リモートとも無し → `git checkout -b work/1113-typ origin/cloudflare`（be8d366）
- 同じ論点の issue（手順1）: `search/issues` で「誤字」「表記ゆれ」「表記揺れ」「校正」「typo」「点検」「脱字」「食い違い」を open・closed とも検索。サイト全体の文字の点検を主題にする issue は無し
  （近いもの: #415 選手名の絞り込みの正規化〈検索欄の話〉、#244 大会名マスタの一本化〈データ構造の話〉。いずれも別の主題）
- 未マージの work/ ブランチと「ワークフローの一覧」の表: `origin/work/1101-prp-pub`（`check-image-links.yml` の行）・`origin/work/1103-ryo5`（`fetch-x-posts.yml` の行）が表を変えているが、どちらも別の行。今回は `research-candidates.yml` の行の後に1行足すだけで重ならない
- 料金（出典: https://platform.claude.com/docs/en/about-claude/pricing.md 、2026-11-13 に取得）: Batch は Opus 5.5 入力 $2・出力 $10、Sonnet 5.5 入力 $1・出力 $5（100万トークンあたり）。キャッシュの読み取りは入力の 0.05 倍、5分のキャッシュ書き込みは 1.25 倍

### 作ったもの

- `scripts/oneoff_text_audit.py`（一度きり）: mode plan（既定。count_tokens だけ）/ submit / collect
  - 対象: 公開しないディレクトリ（.assetsignore と同じ）・noindex・meta refresh のページを除く HTML。手元の cloudflare で 2,399ページ（pro/ 1,099・books/ 191・study/ 41 などは noindex で外れる）
  - 取り出す文字: 本文・見出し・title・meta description・img の alt。script・style・aria-hidden の中（頭文字のアバター「畑」など）は外す（外さないと「畑畑正憲」となり誤字の誤検知になることを試作で確かめた）
  - 組: 対になるページを同じ要求に入れる（title/<大会>、live/<大会>/<期>、saikyo/<年度>、houou/ など）。1要求 6万文字を超える組はページの境で分け、組のトップを各要求に入れる
  - テンプレートの文字: 節のページの半分以上かつ20ページ以上に出る行は、ページから外して1つの要求にまとめる
  - 表記ゆれ: 全ページの文字のノードから2〜16文字の文字列を集め、「空白・中黒・長音・NFKC を揃えると同じ」「1文字違い（数字・卓や回を表す文字の違いは除く）」の組を作って1要求で渡す。「別名」で同じ現在名になる組は外す（シートが読めるとき）
  - 出力は strict のツール `report_findings`（tool_choice auto。Opus 5.5 は any/tool が 400 のため）。effort medium、max_tokens 24,000
- `.github/workflows/oneoff-text-audit.yml`（手動だけ、既定 plan）、`scripts/tests/test_oneoff_text_audit.py`（12件）、`docs/notes/github-actions.md` の一覧の表に1行
- 名前の解決は `houou_race.current_name` を使う（`.resolve()` を直接呼ぶと `test_name_resolvers` の表に足す必要が出て、範囲外の `check_unused_names.py` を変えることになるため）

### 手元の見積もり（鍵なし、1文字＝1トークンで見積もり）

`python3 scripts/oneoff_text_audit.py`: 2,399ページ・取り出した文字 2,409,606・要求 245・入力 約297万トークン。Opus 5.5 の Batch で見込み $15.74（出力 4,000/要求）・最大 $45.14（出力 16,000/要求のとき。24,000 に上げた後の最大は約 $65）。正確な値は cloudflare の plan の実行で取る

### 検証

- `python3 -m unittest discover -s scripts/tests`: 2149件 OK（test_oneoff_text_audit 12件を含む）

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a5fcf1af）: https://github.com/retroeater/mj-logs/tree/main/guide/a5fcf1af

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5fcf1af/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5fcf1af/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5fcf1af/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5fcf1af/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5fcf1af/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a5fcf1af/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
