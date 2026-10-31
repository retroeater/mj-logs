# CHAT-1031-PRO-03

- 着手日時: 2026-10-31
- 対象issue: #219・#641・#296
- ブランチ: work/1030-pro
- 着手時HEAD: a742021

## 指示

【Claude作成】Claude Code 向け指示：pro/ の仮置き K1〜K17 の確定を入れ、選手別 OGP を Actions で描く形にする（未公開・プレビューで止める） Chat-Ref: CHAT-1031-PRO-03 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1031-PRO-02 のセッションの続きでも、新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1030-pro への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1030-pro を続けて使う（PRO-02 で全員分を作った続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-02 のログ（作業ブランチの `docs/logs/CHAT-1031-PRO-02.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、その状態の行の末尾に `/ 続き: CHAT-1031-PRO-03` を足す

目的
PRO-02 の仮置き K1〜K17 と、チャット側がスクショで見つけた3点（A〜C）への平野さんの回答を入れる。選手別の OGP 画像は Actions の生成で描く形（K16 の案②）にし、期が変わっても手作業なしで正しい画像になるようにする。公開の段で行うもの（K8・K13・K14）は #641 に書き足す。この指示では公開しない。
決定（2026-10-31、平野さん）

* A: 「成績」の面の行の書き方を大会どうしでそろえる。形は「最高 ◯ · 出場 N期」（期のある大会）／「最高 ◯ · 出場 N回」（最強戦など回で数える大会）。WRC の「最高到達」の語、最強戦の「3回出場」の語順をこの形に直す。通過回数など大会に固有の補足は括弧で後ろに付けてよい
* B: 「いま」の面の直近の節は、終わった期の結果だと分かる書き方にする（例「43期前期 20位（最終節 ▲45.7）」）。進行中の期の節の速報は #223 の後
* C: トップの表の「支部·期」を1行（「北陸·42期」）に収める
* K1: 上の B のとおり直す。ほかは今の形
* K2: 今のまま（WRC-R も入れ、タイトル戦は小見出しで分ける）
* K3: 放送対局の「すべて（N件）」の件数は、押した先（live/ の検索 `?q=`）と同じ部分一致で数える
* K4・K5・K6・K7・K9・K10: 今のまま（支部は名簿の値「東京」のまま）
* K8: 今のまま。答えに出た選手のカードの行き先は、公開の段で pro/ の個別ページにする
* K11: 支部・入会期ごとの静的なページは作らない（#285 で改めて判断）
* K12: 選手別 OGP の文の段は、鳳凰戦の値が無い選手は女流桜花の値（「女流桜花 C1 · 最高 A」の形）を出す
* K13: canonical は公開の段で付ける（個別ページは URL の変種を持たないので、wayhome/ の各話と同じ例外とする）
* K14: トップの OGP は公開の段で `img/ogp/pros/` を `img/ogp/pro/` へ移す（写して二重にはしない。`jpml_pros.html` の参照も移した先にする）
* K15: #219 を #296（新サイト）の sub-issue から外す（現行サイトで作ったため。#296 の本文「新サイト送りにするとき・やめるとき」の手順で、コメントと sub-issue の両方を外す）
* K16: 選手別 OGP 画像は Actions の生成で描く（案②）。そのために、生成のワークフローに Pillow と字体（Noto Sans JP Bold）の取得を足してよい（新しい依存を足すことの承認）。どちらも版を固定する（Pillow はバージョン、字体は取得元のコミットの SHA）
* K17: PRO-02 で外から読んでいる関数・定数（`lib.birthdays._parse_month_day()`・`generate_jpml_pros.QUERY_ESCAPES`）を `lib/` の公開の関数・定数に移し、jpml_pros 側もそれを使う形にする
* iPhone の Safari でのプレビューの見え方は、平野さんが確かめて問題なし（PRO-02 の版、2026-10-31）

前提（チャット側。平野さんの決定ではない）

* `regenerate-page.yml` を呼ぶ側（`git grep` で洗った。使う直前に確かめ直す）: push（paths）、`workflow_dispatch`、`fetch-youtube.yml`（週次の `all`）、`regenerate-saikyo.yml`（毎朝の `saikyo_pages` だけ）、`run-daily.yml`（2か所）。呼ばれ方ごとの振る舞いの案: 選手別 OGP を描く（Pillow と字体を取る）のは、生成する対象に `pro_pages` が入るとき（`target_page` が `pro_pages` か `all`、または push で pro の生成が起きるとき）だけにする。`saikyo_pages` だけの毎朝の呼び出しでは取らない（毎朝の時間と外の取得を増やさない）。違う形の方がよければ理由をログに書いて選んでよい
* 描き直しの範囲の案: 文の一覧（PRO-02 の「OGP の文の一覧」）のハッシュが変わった選手の画像だけを描き直し、変わっていない画像は書き換えない（PNG のバイトが環境で揺れても差分を出さないため）。字体の取得が失敗したときは生成を止めず、画像を描かずに前の画像を残し、警告を出す（ページの生成は止めない）。PRO-02 で作った `build_pro_ogp.py`（コンテナで回す手作業）は、Actions の描き方に置き換えて消す（手作業の手順を残さない）
* 字体の取得元は docs/notes/title-pages.md の手順の取得元（`raw.githubusercontent.com/notofonts/noto-cjk`）と同じにし、URL にコミットの SHA を入れて固定する。取得は Actions だけ（ページから外部を読むわけではないので CSP は変わらない）（要確認）
* ワークフローを変えるので、着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読み、作業ブランチで `regenerate-page.yml` を手動実行して確かめる（`target_page` `pro_pages`、check 真・偽の両方。偽の実行は作業ブランチにコミットする）。外のサイトに触れるので、一時的な接続の失敗は失敗したジョブだけを1回再実行して success なら止まらない
* 期が変わったときの確かめ方の案: 手動実行の前に、選手を1人選んで文の一覧の値を変えたときにその1枚だけが描き直されることをテストで確かめる（`scripts/tests/`）
* K12 の文の段の端のデータ: 鳳凰戦・女流桜花の両方がある選手、鳳凰戦だけ・女流桜花だけ・どちらも無い選手（どちらも無い選手は名前・支部·期だけの段）の4種で画像を撮って `docs/logs/CHAT-1031-PRO-03/` に置く
* A〜C の画面の確かめは、PRO-02 と同じ端のデータ（両方・鳳凰戦だけ・桜花だけ・どちらも無い・名前が最も長い・放送対局の多い・SNS が無い・進行中の期だけ）とトップの 360px・390px の明暗。WRC・WRC-R・最強戦の行のある選手を1人以上含める
* 公開の段で行うもの（K8・K13・K14）は、#641 に「公開の指示で行う」としてコメントで書き足す（PRO-02 のコメントへの追記として、この指示の Chat-Ref を末尾に）
* 決定は完了時に docs/decisions/pros.md に足す（CLAUDE.md「作業ログ」節）

手順

1. 0章の確かめの後、A〜C・K1〜K17 のうちこの指示で直すもの（A・B・C・K3・K12・K17）を入れ、テストを足す。K15 の #296 の sub-issue からの取り外しと、#641 へのコメント（K8・K13・K14）を行う
2. K16: 選手別 OGP を Actions で描く形に変え（ワークフロー・生成スクリプト・テスト）、`build_pro_ogp.py` を消す。作業ブランチで `regenerate-page.yml` を手動実行し（check 真・偽）、描いた枚数・描き直さなかった枚数・時間・取得した字体と Pillow の版を報告に書く。`saikyo_pages` だけの実行で字体を取らないことも1回確かめる
3. 検査（`python3 -m unittest discover -s scripts/tests`・`--check`・`check_asset_limits.py`）と画面の確かめを流し、新しい文脈のサブエージェントに差分をレビューさせ（CLAUDE.md「判断・作業の原則」）、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* 0章の確かめが通らない（PRO-02 のログの状態が「判断待ち」でない）
* 前提が実物と食い違い、決定（上と PRO-01・PRO-02 の「決定」の欄）のどれかが守れない（どれが・なぜを書いて止まる）
* テスト・`--check`・`check_asset_limits.py`・手動実行が通らない（一時的な接続の失敗の再実行は上の前提のとおり。原因を書いて止まる）
* 手動実行で、文の一覧が変わっていない選手の画像が書き換わる（PNG のバイトの揺れ。何枚かを書いて止まる）
* 未マージの work/ ブランチが `.github/workflows/regenerate-page.yml`・`scripts/lib/page.py`・`scripts/lib/birthdays.py`・`scripts/generate_jpml_pros.py`・`scripts/regenerate.py`・`style.css` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 他セッションの着手中コメントが #219・#641 にある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-31）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。PRO-02 のログの状態は「判断待ち」→ 末尾に「 / 続き: CHAT-1031-PRO-03」を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 1行目にまとまって貼られているが、4つとも有る
- 同じ Chat-Ref のコミット: 0件。CLAUDE.md は origin/cloudflare から変わっていない
- 作業ブランチ: ローカルの work/1030-pro は origin/work/1030-pro（a742021）と一致。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽 → 取り込む
- 取り込み: `git merge origin/cloudflare`（docs/logs/CHAT-1031-PGV-01.md の1件。衝突なし、ea3f6b4）
- #219・#641: 他セッションの着手中コメントは無し → #219 に着手中（続き）のコメント（issuecomment-6536522345）
- 未マージの work/ ブランチの重なり（止まる条件の対象のファイル）: work/1017-lvp（style.css）・work/1028-lgn・work/1029-lgn-page（style.css・regenerate.py）は PRO-02 と同じ箇所のまま。`.github/workflows/regenerate-page.yml`・`scripts/lib/page.py`・`scripts/lib/birthdays.py`・`scripts/generate_jpml_pros.py` を変えている未マージのブランチは無し。この指示の style.css の追記は pro/ の節の中、regenerate.py は `pro_pages` の行だけ
- `regenerate-page.yml` を呼ぶ側（使う直前に `git grep` で確かめ直した）: 前提の一覧と違い、`run-daily.yml` は直接は呼ばない（`update-live-channel.yml`〈target `live_pages title_pages`〉・`update-sns-book.yml`〈`all`〉を通して呼ぶ）。ほかは `fetch-youtube.yml`（`all`）・`regenerate-saikyo.yml`（`saikyo_pages`）・push・`workflow_dispatch`。前提の振る舞いの案（`pro_pages` が対象に入るときだけ字体を取る）はそのまま当てはまるので、案のとおりにした（`live_pages title_pages` の毎日の呼び出しでも取らない）

### 手順1: A〜C・K3・K12・K17、K15、#641

- A: 成績の面の要約を「最高 ◯ · 出場 N期」にそろえた（WRC の「最高到達」をやめた。`contest_summary()`）。最強戦は名前の面の1行を読み直して「最高 ファイナル · 出場 6回（ファイナル 2回）」「最高 最強位（2022・2021） · 出場 16回」「出場 3回」（`saikyo_summary()`）
- B: 「いま」の2行目を「43期前期 20位（最終節 ▲45.7）」の形に（節の成績の無い期は「（合計 +50.0）」、鳳凰位の期は「42期 鳳凰位」。`last_round()`）
- C: トップの表の支部·期を1行「北陸·42期」に（`white-space: nowrap`）
- K3: 放送対局の件数と3件を、live/ の検索（`assets/live.js` の `normalize()`: NFKC・小文字・空白を1つ・全部の語を含む）と同じ部分一致にした（`live_key()`・`lives_of()`）
- K12: OGP の3段目は鳳凰戦の値、無ければ女流桜花の値。大会名は必ず付ける（現在のリーグが無い選手は「女流桜花 最高 C1」。PRO-02 では大会名の無い「最高 C1」が133名に出ていたのを見つけて直した、a2d0432）
- K17: `lib.birthdays.parse_month_day()` を公開の名前にし、`QUERY_ESCAPES` を `lib/url.py` へ移した（`generate_jpml_pros.py` も `lib/url.py` から読む）。`regenerate.py` の `pro_pages` の依存から `jpml_pros` を外した
- テスト（`scripts/tests/test_pro_pages.py`、22件→27件）: `saikyo_summary`・新しい `last_round`・WRC の要約・live/ と同じ部分一致・OGP の段の女流桜花への切り替えと大会名・描き直しの範囲（1人の文を変えるとその1枚だけを描き、変わっていない画像の更新時刻が変わらず、古い画像が消える）・描けないとき前の画像を残す
- K15: #219 を #296 の sub-issue から外した（`sub_issue_write` remove。#296 の子は 37→36）。#219 に取り消しのコメント（issuecomment-6536524859）。#296 の本文の表の #219 の行を「親の対象外」へ移した（#378 の前例の形。書き換え前の `updated_at` 2026-10-29T12:00:54Z を確かめてから REST の PATCH）
- #641 に K8・K13・K14 を「公開の指示で行う」として追記（issuecomment-6536528759）

### 手順2: K16（選手別 OGP を Actions で描く）

- `generate_pro_pages.py`: 文の一覧を書いた後、名前（文のハッシュ）の画像が無いものだけを描き（`draw_ogp()`）、一覧に無い画像を消す。描く関数は `ogp_renderer()`（Pillow が無い・字体〈`OGP_FONT`〉が無いなら警告して描かない＝前の画像を残す）。描いた後にページを作るので、描けた選手はその画像を指す。`scripts/build_pro_ogp.py` は消した
- `regenerate-page.yml`: 手順「選手別のOGP画像の字体とPillowを取る」を足した。`if: inputs.check != true && (push・target が空・all・pro_pages を含む)`、`continue-on-error: true`。`pip install Pillow==12.3.0`、字体は `https://raw.githubusercontent.com/notofonts/noto-cjk/f8d157532fbfaeda587e826d4cd5b21a49186f7c/Sans/SubsetOTF/JP/NotoSansJP-Bold.otf`（`git ls-remote` で取った main のコミット）を `curl` で取り、sha256 `1b0edfb5…2c5f` を `sha256sum -c` で確かめる（PRO-02 でコンテナが使った字体と同じバイト）。取れたときだけ出力 `font` を `OGP_FONT` で再生成の手順に渡す。入力はすべて env で受け、`run:` に展開していない
- 手動実行（作業ブランチ）:

| run | 入力 | 字体と Pillow の手順 | 結果 |
|---|---|---|---|
| 41150945841 | pro_pages・check 真 | skipped | 「pro_pages: 通る(--check、警告 1件)」。警告は K12 で文が変わった女流桜花だけの24名の画像が無いこと（想定どおり） |
| 41151021073 | pro_pages | success（2秒） | 再生成 9秒。c742255: 画像は追加24枚・削除24枚（描き直さなかった 1,074枚）・中身の書き換え0枚 |
| 41151198966 | pro_pages（a2d0432 の大会名の直しの後） | success（2秒） | 再生成 13秒。1c4bec3: 追加134枚・削除134枚（描き直さなかった 964枚）・中身の書き換え0枚 |
| 41151310640 | saikyo_pages・dry_run 真 | skipped | 字体と Pillow を取らないことを確かめた |

- 文の一覧が変わっていない選手の画像は書き換わらなかった（どちらの実行もコミットの画像の変更は追加と削除だけで、変更〈M〉は0件。止まる条件に当たらない）
- 版: Pillow 12.3.0（`pip install Pillow==12.3.0` が通ったので、その版）、字体は上のコミットの SHA と sha256 で固定。手順の出力の Pillow の版の行（`python3 -c "import PIL; print('Pillow', PIL.__version__)"`）はジョブのログの途中にあり、ログの末尾だけを読める MCP では引けていない
- OGP の4種（両方・鳳凰戦だけ・女流桜花だけ・どちらも無い）を `docs/logs/CHAT-1031-PRO-03/ogp-*.png` に置いた: 蒼井ゆりか「鳳凰戦 D1 · 最高 C2」・會田亮介「鳳凰戦 E3 · 最高 E3」・安部久美子「女流桜花 最高 C1」・藍ありさ（名前と「北陸・42期」だけ）
- 合計: `img/ogp/pro/` 1,098枚・39,213,157 バイト、`pro/` 20,267,091 バイト

### 手順3: 検査と画面

- `python3 -m unittest discover -s scripts/tests`: OK。`check_asset_limits.py`: 配信ファイル数 6,405 / 100,000 ほか全部 OK
- 画面: PRO-02 と同じ11画面（トップ3つ・個別ページ8つ。蒼井ゆりかは WRC・WRC-R・最強戦の行がある）を 360・390px × 明暗で44枚（`docs/logs/CHAT-1031-PRO-03/`）。はみ出し0件。重なりの検出32件は PRO-02 と同じ閉じた `<details>` の誤検出で、見える重なりは0件。A・B・C が出ていることを目で確かめた（例: 蒼井ゆりか「43期前期 20位（最終節 ▲45.7）」「最高 予選通過（16期） · 出場 2期（通過1回）」、トップ「東京·24期」が1行）
- 確認用の印（プレビュー）: 個別ページの「いま」の2行目が「43期前期 20位（最終節 ▲45.7）」の形（蒼井ゆりか）

### 差分のレビュー（新しい文脈のサブエージェント、1回）

a742021..116ebe8（生成物・ログ・サイトマップを除く）。テスト 1663件 OK、字体の sha256 を実際に取って照合、Pillow 12.3.0 が PyPI にあることを確かめたうえで、重大な指摘なし。A・B・C・K3・K12・K16・K17 は決定どおり、`if:` の式（push・workflow_call で `inputs.check` が null）・呼ぶ側ごとの振る舞い・字体の取得の守り（SHA と sha256、env 経由）は問題なしとのこと。

| 指摘 | 扱い |
|---|---|
| 低: `pip install Pillow==12.3.0` にハッシュが無い | 見送り（判断が必要なこと。既存の帰り道の手順は版の固定も無い） |
| 低: `all` のとき帰り道の手順が最新の Pillow を入れ、この手順が 12.3.0 に下げる | 意図どおり（害は無い。判断が必要なことに併記） |
| 低: `ogp_renderer` の2つの分岐と、順位の無い行の `last_round` に検査が無い | 直した（2691055） |
| 低: push では pro_pages が対象でなくても字体を取る | 直した（2691055。`簡略化:` の印） |
| 軽微: generate_jpml_pros.py の空行4行・テストの空行3行・ogp.md の条件に「空」が抜け | 直した（2691055） |

2691055 はコメント・テスト・文書だけの変更なので、ワークフローの手動実行はしていない（手順の中身は 5c81b04 からの実行で確かめた）。`python3 -m unittest discover -s scripts/tests`: OK

## 報告

- 状態: 判断待ち（K1〜K17・A〜C の決定を入れ、選手別 OGP を Actions で描く形にして、未公開のままプレビューで止めた） / 続き: CHAT-1031-PRO-04
- ブランチ: work/1030-pro
- ログ: https://github.com/retroeater/mj/blob/work/1030-pro/docs/logs/CHAT-1031-PRO-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1030-pro
- 確認用URL: プレビューあり（URL は最終報告）。確かめた画面は `/pro/`・`/pro/yurika.aoi.24/` ほか11画面。この版の印: 「いま」の2行目が「43期前期 20位（最終節 ▲45.7）」の形
- マージ: 未（平野さんの判断待ち）
- issue: #219（着手中・新サイト送りの取り消し）・#296（sub-issue から外し、本文の表を移した）・#641（K8・K13・K14 を追記）
- 判断が必要なこと:
  - K16 の計測: 描いた枚数 24枚（K12）→ 134枚（大会名の直し）、描き直さなかった枚数 1,074枚 → 964枚、中身の書き換え 0枚。字体と Pillow の手順 2秒・再生成 9〜13秒。Pillow 12.3.0・字体 noto-cjk f8d1575（sha256 1b0edfb5…）。`saikyo_pages` だけの実行では取らないことを確かめた
  - Pillow の取得にハッシュの照合（`--require-hashes`）を足すか。また `all` のとき帰り道の手順が版の固定なしの Pillow を入れる（この手順が 12.3.0 に戻す）のをそろえるか
  - 前提の「`run-daily.yml`（2か所）が `regenerate-page.yml` を呼ぶ」は、実物では `update-live-channel.yml`・`update-sns-book.yml` を通した呼び出し（振る舞いの案はそのまま当てはまったので止まらなかった）
  - 公開の判断（#641。K8・K13・K14 は公開の指示で行う）
- 未確認の項目:
  - X・LINE での選手別 OGP 画像のカード表示（公開前なので確かめていない）
  - 質問欄に実際に問いを送った答え（Worker を呼ぶため送っていない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a512c7d9）: https://github.com/retroeater/mj-logs/tree/main/guide/a512c7d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a512c7d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a512c7d9.md
