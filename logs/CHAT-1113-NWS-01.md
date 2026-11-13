# CHAT-1113-NWS-01

- 着手日時: 2026-11-13
- 対象issue: #408
- ブランチ: work/1113-nws
- 着手時HEAD: b3a20d7

## 指示

【Claude作成】Claude Code 向け指示：過去の「月刊プロ連ニュース」（2001〜2006年、スキャンの PDF 56本・574ページ）を Claude API（Opus 5.5 の Batch、画像）で読み、タイトル戦の日付・順位などを「タイトル」シートと照らして候補タブに書く Chat-Ref: CHAT-1113-NWS-01 マージ: 承認済み（チャットで。一度きりのスクリプト・ワークフローの追加と、使い終えた後の削除。試しの1号で読み取りの正しさを確かめた後に限り全号を送る） 貼る時機: いつでも（できるだけ早く。理由は「前提」の期限） セッション: 推奨（新しい系列 NWS。RSC・TYP のセッションとは別にする。ネットワークの変更は無い見込み〈Drive API・Claude API は Actions から呼ぶ〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1113-nws を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-nws origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが渡した過去の「月刊プロ連ニュース」から、タイトル戦の期ごとの対局日・順位（出場者）などを取り出し、「タイトル」シートの補完（日付が年だけの期など）と食い違いの確かめに使う候補を、出典（号・ページ）付きで候補タブに書く。
決定（2026-11-13、平野さん）

* 過去の「連盟ニュース」で、過去のタイトル戦の日付・順位等の補完・チェックを行う
* PDF の置き場所はドライブのフォルダ「【Claude受渡用】」（平野さんが置いた。2026-11-13 15:32 JST）
* 失効の近いプロモーションクレジット（$109.24、2026-11-17 00:00 UTC 失効）を使う。組織の月間支出上限は 2026-11-17 まで $200
* この指示のマージ（一度きりのスクリプト・ワークフロー）と、試しの1号で正しさを確かめた後の全号の実行、使い終えた後の削除を承認する。シートの「タイトル」は変えない（本体に入れるのは平野さん。候補タブ R_候補 に書く）

前提（チャット側。平野さんの決定ではない）

* 資料（チャット側で中身を確かめた）: 56本の PDF。名前は `YYYYMM_NNN.pdf`（2001年1月号 001 〜 2006年9月号 057、047 は無い）。全部で 574ページ。1ページ 1591×2142px 前後の JPEG 1枚（文字のデータは無い。`pdftotext` は空）。紙面は縦書きの新聞の形で、タイトル戦の結果の記事（例: 200104_003 の1面「第17期プロリーグ鳳凰戦レポート」に「古川孝次 鳳凰位連覇」「2位・椎名大介 3位・荒正義 4位・森谷健」）、リーグ戦の全成績表、「平成13年度行事予定表」のような年間の予定（対局日）が載る
* 置き場所での形（zip のままか、展開した PDF か）は要確認。読み方は `scripts/lib/ryoei_book.py` の「【Claude受渡用】」の探し方（sheets-reader で名前から探す。ID はコード・ログに置かない）に合わせる。ほかのファイル（特別授業のスライドの PDF）は読まない
* 読み取りの形の案: ページを 200dpi 相当で縦横に分けた画像（例: 1ページを 4〜6枚。見出しの大きい字だけでなく本文・表の小さい字が読める大きさ。チャット側で 200dpi の切り出しが読めることを確かめた）を Opus 5.5 に渡し、決まった形の JSON（大会名・期・対局日〈日付・節〉・順位・名前・点数〈あれば〉・号・ページ・切り出しの位置・確度）で返させる。行事予定表は「予定」として別に扱う（実際の対局日と区別する）。ページごとに「タイトル戦の結果・予定が無い」なら空で返させる（全ページに大きな出力をさせない）
* 費用の見込み: 画像 1枚あたり 約1,600トークンとして、574ページ×5枚で入力 約460万トークン。Opus 5.5 の Batch（入力 $2・出力 $10、100万トークンあたり。TYP-01 で確かめた値）で入力 約$9＋出力。上限はこの指示の合計 $60（今月の使用と残りのクレジットは Console で平野さんが見る。実額は Batch の usage から）
* 期限: Batch の結果は最長 24時間かかりうるので、全号の Batch を送るのは遅くとも 2026-11-15 中
* 照らし方の案: 名前は「別名」で現在名に直してから照らす（RSC の B と同じ部品）。大会名は「タイトル」シートの大会名の書き方に寄せる（対応の表を作り、ログに書く）。照らした結果は「照合済み（シートと同じ）」「食い違い（シートと違う。どちらの値も書く）」「補完（シートが空・年だけ）」「シートに無い（期・人が無い）」に分ける
* 候補タブへの書き方の案: 日付は種別 C（#408、対局日）と同じ列の形で、モデルの列に「連盟ニュース」のように出典の種類が分かる値、出典は「YYYYMM_NNN.pdf p.N」の形（URL は置かない）。順位・出場者は新しい種別の名前を決めて書く（docs/notes/research-candidates.md の種別の表に足す）。どちらも状態は空（採用・却下は平野さん）。一度きりの書き込みでも、今の候補タブの列・飛ばし方（同じ対象・モデルの行があれば飛ばす）に合わせる
* 権利・公開: 連盟ニュースは連盟の刊行物。PDF・画像・切り出しはリポジトリにコミットしない（Actions の作業領域だけで扱う）。ログ（公開の mj-logs に写る）・issue には、日付・順位・名前などの事実と号・ページだけを書き、記事の文章は写さない
* 一度きりのスクリプト・ワークフローは、結果を回収して候補タブに書いた後、同じ指示で外す（TYP-01・02 と同じ扱い。docs/notes/github-actions.md の表と `scripts/tests/test_workflows_table.py` を足すときと外すときの両方で合わせ、外したことと戻し方〈コミットの SHA〉を docs/decisions に書く）

手順

1. 同じ論点（連盟ニュース・過去のタイトル戦の対局日・順位）の open issue を確かめる（#408 は対局日の親として使う。新しい issue が要れば起票する）。#408 に着手中のコメントを書く（本文・状態・ラベルは変えない）。未マージの work/ ブランチが `scripts/research_candidates.py`・`scripts/lib/ryoei_book.py`・docs/notes/github-actions.md の表の同じ行を変えていないか確かめる
2. 一度きりのスクリプト・ワークフロー（既定は dry-run）を作り、試験を足し、自己レビュー（docs/notes/self-review.md）の後にマージする。dry-run で「【Claude受渡用】」の中のファイルの一覧（名前・ページ数）と、切り出しの枚数・count_tokens による入力トークン数・見込みの費用を書く
3. 試しの1号: 200104_003.pdf（8ページ）だけを Batch ではなく通常の API（または小さな Batch）で読み、取り出した全件の表と、「タイトル」シートとの照らし合わせの表をログに書く。少なくとも1面の「第17期鳳凰戦 1位 古川孝次・2位 椎名大介・3位 荒正義・4位 森谷健」と、7面の「平成13年度行事予定表」の各行が正しく取れていることを確かめる。取れていなければ切り出し・プロンプトを直して試し直す（2回まで）
4. 全号の Batch を送り、回収し、照らし合わせて候補タブに書く。書いた行の数を分け方（照合済み・食い違い・補完・シートに無い）×大会ごとの表で全件書き、「食い違い」と「補完」は全件の表（大会・期・項目・シートの値・ニュースの値・号・ページ・確度）をログに書く。実額を書く。#408 に要約をコメントする（ログのどの表から引用するかを書く）。回収の後に一度きりのスクリプト・ワークフローを外してマージし、docs/decisions に記録する
5. 「判断が必要なこと」に、(1) 候補の採用の進め方（補完・食い違いの件数と、平野さんの確かめの手間の見込み） (2) 鳳凰戦などリーグ戦の全成績表（今回は取り出さない）を同じ方法で取り出すか、をメリット・デメリット・推奨つきで書く

止まる条件

* 「【Claude受渡用】」が見つからない、同じ名前が2つ以上ある、または中に `YYYYMM_NNN.pdf` の PDF（または zip）が見つからない
* dry-run の見込みが $60 を超える（切り出しの減らし方・対象ページの絞り方〈例: 見出しだけ先に読み、タイトル戦の記事のあるページだけ細かく読む〉の案と見込みを書いて止まる）
* 試しの1号で、2回直しても鳳凰戦の4人の順位か行事予定表が正しく取れない（取れた表と取れなかった箇所を書いて止まる。全号は送らない）
* 2026-11-15 23:59 JST までに全号の Batch を送れない見込み
* API の残高不足・認証・支出上限のエラー（そのまま書いて止まる）
* Batch が失敗・期限切れで回収できない（一度きりのスクリプト・ワークフローは外さずに止まる）
* 差分が次の範囲を超える: 一度きりのスクリプト・ワークフロー・試験、docs/notes/github-actions.md の表と `scripts/tests/test_workflows_table.py`、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフローが変わったら止まる（マージしない）
* PDF・画像・記事の文章をコミット・ログ・issue に入れる必要が出た（入れずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-NWS-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-NWS-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0.: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- Chat-Ref の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-NWS-'`・`docs/logs/CHAT-*-NWS-*.md` の履歴とも 0件
- issue: #408 は open。着手中のコメントを書いた（https://github.com/retroeater/mj/issues/408#issuecomment-6846161609 ）。同じ論点の open issue はほかに無い（#408 のコメントの流れで、RSC-14〜17 の Web の調べものが区切られている）
- 0章ゲート: 未マージの work/ 8本のうち、触るファイル（`scripts/research_candidates.py`・`scripts/lib/ryoei_book.py`・`docs/notes/github-actions.md`・`docs/notes/research-candidates.md`・`docs/decisions/`）を変えるのは `work/1101-prp-pub`・`work/1103-ryo5`（github-actions.md の別の行と decisions）・`work/1113-skp-amateur`（decisions/saikyo.md）。同じ行は無い。CLAUDE.md 23,972B・handover.md 24,511B（警告域 26KB の下）
- ブランチ: リモートに `work/1113-nws` が無いので `origin/cloudflare`（b3a20d7）から作った

### 2. 一度きりのスクリプト・ワークフロー（39ba6e6）

- `scripts/oneoff_news_read.py`・`.github/workflows/oneoff-news-read.yml`・`scripts/tests/test_oneoff_news_read.py`
- PDF: 1ページ1枚の JPEG を PDF から取り出す（Pillow は PDF を開けないので、ページの木〈/Root → /Pages → /Kids、圧縮されたオブジェクトのストリームも読む〉を辿って画像の JPEG をそのまま取る。新しい依存は足さない）
- 切り出し: 1ページを 2列×2行・12%重ね、1.6倍に拡大（Opus 5.5 の画像の上限 長辺 2576px・3.75MP に収める）、灰色の JPEG。1ページを1要求で送る（4枚に位置のラベル「右上・左上・右下・左下」、右の列が先）
- 答え: クライアント側のツール `report_page`（strict）。records は 種類（結果・対局日・予定）・大会（「タイトル」の表示する大会の名前か「その他」から選ばせる）・紙面の大会名・期・段階・日付・紙面の日付・決勝の最終日か・順位・名前・点数・位置・確度・注記。記事の文章は出させない
- 照らし合わせ: C（決勝の最終日）は「対局日」を「予定」より先に使う。E（順位）は名前を「別名」で直し、NFKC・空白を除いて照らす。分け方は 照合済み・食い違い・補完・シートに無い
- 候補タブ: 種別 C・E（E はこの指示で足した）、モデル「連盟ニュース」、出典の列は `YYYYMM_NNN.pdf p.N`。同じ（種別, 対象キー, モデル）の行があれば飛ばす。docs/notes/research-candidates.md に E の行と「連盟ニュースの行」の節を足した
- 範囲の外の1行: `scripts/check_unused_names.py` の `NAME_RESOLVERS` に `oneoff_news_read.load_sheet` を足した。`generate_title_pages.build_aliases` を呼ぶ関数を足すと `scripts/tests/test_name_resolvers.py` が表への登録を求めるため（足さないと全体の試験が2件落ちる）。外すときに一緒に外す
- 試験: `python3 -m unittest discover -s scripts/tests` → 2159件 OK（新しい試験 14件: Pillow で作った PDF・圧縮されたオブジェクトのストリームの PDF からの取り出し、回転、切り出しの重なりと上限、照らし合わせの分け方、候補タブの行の形）
- 新しいワークフローは既定ブランチに無いので作業ブランチでは起動できない（docs/notes/github-actions.md「ワークフローを変更したとき」）。既定の mode plan は書き込みも Batch の送信もしないので、自己レビューの後にマージして cloudflare で plan を実行する

## 報告

- 状態: 対応中
- ブランチ: work/1113-nws
- ログ: https://github.com/retroeater/mj/blob/work/1113-nws/docs/logs/CHAT-1113-NWS-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-nws
- 確認用URL: なし
- マージ: 未
- issue: #408
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b3a20d7c）: https://github.com/retroeater/mj-logs/tree/main/guide/b3a20d7c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3a20d7c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
