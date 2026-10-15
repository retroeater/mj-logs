# CHAT-1015-SKR-03

- 着手日時: 2026-10-15
- 対象issue: #549（新しい issue を起こす）
- ブランチ: work/1015-skr-live
- 着手時HEAD: e9d5efe88

## 指示

【Claude作成】Claude Code 向け指示：卓の「▶ 再生」にライブ枠を貼る決定の記録と起票、【3】に URL だけを入れたときの卓のページの出方の確かめ
Chat-Ref: CHAT-1015-SKR-03
マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1015-SKR-04 と並行してよい。別のセッションに貼る）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skr-live の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-skr-live を使う。リモートに無ければ origin/cloudflare から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
最強戦の卓のページの「▶ 再生」に、卓ごとの動画がまだ無い間は当日の ABEMA のライブ枠を貼る、という平野さんの決定を記録し、自動で切り替える仕組みを別の issue に起こす。あわせて、ABEMA ブックの【3】手動補正に URL だけを入れた卓（【1】【2】に回が無い）が、今の本番のコードと公開の作業ブランチ `work/1015-skr-pub`（CHAT-1015-SKR-02、未マージ）のコードで、それぞれどう出るかを確かめる。コード・ページ・ブックは変えない。

### 決定（2026-10-15、平野さん）
1. 未開始の対局と、終わった直後で卓ごとの動画がまだ無い対局は、卓ごとの「▶ 再生」に当日の ABEMA のライブ枠の URL を貼る（例: 2026 団体対抗スペシャル合戦 `https://abema.go.link/j9ySR`）。2026-10-14（CHAT-1014-SKD-06 ほか）の「ABEMA の放送枠の URL は控えない」を置き換える
2. 当面は、平野さんが ABEMA ブックの【3】手動補正に卓ごとの行で入れる（コードは変えない）。卓ごとの動画が出たら平野さんがその行を消す
3. 自動で切り替える仕組み（【3】に「放送枠URL」の列を足し、【2】に回があればそれ、無いときだけ放送枠を使う）は別の issue として起こす。公開（#549）の後に作る
4. ABEMA の回が無い卓のヒーローに、出典「画像: ABEMA」を出す（CHAT-1015-SKR-02 の判断のまま）
5. CHAT-1015-SKR-02 のプレビューは平野さんが iPhone で確かめ、この形で公開してよい（マージは告知動画の確定後、別の指示）

### 前提（チャット側。平野さんの決定ではない）
- 【3】の列と上書きの規則は docs/notes/abema-book.md「タブ」「生成の読み方」（`abema_book.video_urls()` が【2】の URL を【3】の空でない URL で上書きする）。サムネイル・題名は【1】【2】の回から来るので、URL だけの卓でヒーローの画像と「▶ 再生」・出典がどう出るかは確かめていない（要確認）
- 平野さんが入れる予定の行（2026-10-18 団体対抗スペシャル合戦 ベスト8 A卓・B卓。cloudflare の `saikyo/2026/20261018-s1-a.html`・`-s1-b.html` のパンくずの名前）: 年度 2026・対局日 2026-10-18・対局「団体対抗スペシャル合戦」・ステージ「ベスト8」・卓「A卓」／「B卓」・URL `https://abema.go.link/j9ySR`（要確認: 鍵の5列の正しい値をシートと【2】から確かめる）

## 手順
1. 確かめる: 同じ主題の issue（「放送枠」「ライブ枠」「slots」「手動補正」）をクローズ済みも含めて検索する。docs/decisions/saikyo.md の今の内容と、置き換わる「放送枠の URL は控えない」の行を読む。【2】【3】を生成と同じ経路で読み、2026-10-18 の2卓の鍵の5列の値と、【3】の今の行数をログの経過に書く
2. 出方を確かめる（ブックには書かない）: 【3】に上の2行があるとみなした入力で、`origin/cloudflare` と `origin/work/1015-skr-pub` のそれぞれの生成スクリプトで 2026-10-18 の卓のページを手元で作り、ヒーローの画像・「▶ 再生」の有無と行き先・出典・司会実況解説・対局のページの卓の見出しの ABEMA のリンクを、表にしてログの経過に書く（ブックの読み込みを差し替えるか、テストの形で行う。作ったページはコミットしない）。どちらかで「▶ 再生」が出ない・行き先が違うなど、決定 1 のとおりにならなければ、その理由（どの関数のどの条件か）を書く
3. 起票と記録:
   - 新しい issue「最強戦の卓の『▶ 再生』を、卓ごとの動画が無い間は ABEMA のライブ枠に自動で切り替える」: 決定 1〜3、手順2 の結果、関係（#549・#573・docs/notes/abema-book.md）。ラベルは既存の付け方に合わせる（`対象: saikyo` など）
   - #549 にコメント: 決定 4・5 と、手順2 の結果のうち `work/1015-skr-pub` の分（直しが要るなら、それを公開のマージの指示に入れるか別にするかはチャット側が決める、と書く）
   - docs/decisions/saikyo.md の末尾に `## 2026-10-15（CHAT-1015-SKR-03）` で決定 1〜5 を書き、置き換わる行の行末に「→ 置き換え: 2026-10-15（CHAT-1015-SKR-03）」を付ける。docs/notes/abema-book.md の【3】の説明に「ライブ枠の URL を卓ごとに入れてよい（卓ごとの動画が出たら消す）」を1行足す

## 止まる条件
- 同じ目的の open issue がある（起票せずにその issue へコメントして進める）
- docs/decisions/saikyo.md の今の内容が決定と矛盾し、どちらが正か判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい）
- 未マージの work/ ブランチが docs/decisions/saikyo.md・docs/notes/abema-book.md の同じ節を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く。両立する衝突〈追記どうし〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。新しい issue の番号を「未確認の項目」に書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKR-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKR-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- `git log --all --grep=CHAT-1015-SKR-03` は0件。`origin/work/1015-skr-live`・ローカルとも無いため `git checkout -b work/1015-skr-live origin/cloudflare`（e9d5efe88）
- 同じ主題の issue（MCP の検索「ABEMA 放送枠 ライブ枠 slots 手動補正 最強戦 再生」「放送枠」）: #573・#572・#425・#501（closed）と放送対局カレンダーの issue が当たった。卓の「▶ 再生」をライブ枠にする目的の issue は無い（#572 は新しい回が出たかの検知で別の目的。関係として書いた）
- docs/decisions/saikyo.md の置き換わる行は 2026-10-14（CHAT-1014-SKD-06）の「ABEMA の放送枠（`abema.tv/channels/mahjong/slots/…`）の URL は控えない」。決定1と矛盾するが、決定1が明示して置き換えるもの
- 未マージの work/ ブランチで docs/decisions/saikyo.md・docs/notes/abema-book.md を変えるのは `origin/work/1015-skr-pub`（saikyo.md の末尾に `## 2026-10-15（CHAT-1015-SKR-02）` の節を足すだけ）。今回も末尾への追記なので、後からマージする側で追記どうしの衝突になる（両方を残して解ける）。abema-book.md を変えるブランチは無い

### 【2】【3】（生成と同じ `fetch_table()` で読んだ、2026-10-15）

- 【3】手動補正: 見出し `年度・対局日・対局・ステージ・卓・URL・司会・実況・解説・備考`、行は 0
- 【2】の 2026-10-18 の行（鍵の5列）: `2026`・`2026-10-18`・`団体対抗スペシャル合戦`・`ベスト8`・`A卓`／`B卓`（URL・割り当ては空）。指示の前提の値と一致

### 【3】に URL だけの2行があるとみなした出方

`scripts/` を `origin/cloudflare`（e9d5efe88）と `origin/work/1015-skr-pub`（8ab674ba5）からそれぞれ作業ディレクトリの外へ取り出し、【2】【3】を読んだ値に上の2行（鍵の5列＋URL `https://abema.go.link/j9ySR`）を足して `abema_book.table_cells()` に渡し、生成と同じ `load_rows()`・`build_matches()`・`attach_videos()` で 2026-10-18 の対局・卓のページの本文を組み立てた（ファイルは書かない。スクリプトは scratchpad の `live_check.py`）。

| | cloudflare（本番のコード） | work/1015-skr-pub |
|---|---|---|
| ヒーローの画像 | 年度の OGP 画像（`img/ogp/saikyo/2026-black.png`。取り出した `scripts/` だけでは画像が無く `img/ogp.png` になったが、リポジトリには 2026 の画像がある） | ABEMA の作品の画像（`series/68-38/thumb.png?…&width=1280`） |
| 「▶ 再生」 | 出ない。代わりに「ABEMA で見る」のボタン | 同じ |
| ボタンの行き先 | `https://abema.go.link/j9ySR`（新しいタブ） | 同じ |
| 画像を押して ABEMA へ（`mj-saikyo-hero-link`） | なし | なし |
| 出典「画像: ABEMA」 | なし | あり |
| 司会・実況・解説 | なし（【3】に入れていない） | なし |
| 対局のページの卓の見出し | `20261018-s1-a.html`・`-s1-b.html`（卓のページへ）。対局のページに ABEMA へのリンクは無い（SKD-05） | 同じ |
| 生成のログ | 卓ごとに「警告: 動画URLが ABEMA の回の URL ではありません(リンクだけ出し、サムネイルは出しません)」 | 同じ |

- 決定1のとおりにならない点: 行き先はライブ枠の URL で合っているが、ボタンが「▶ 再生」ではなく「ABEMA で見る」になり、画像のリンクも付かない
- 理由: `generate_saikyo_pages.py` の `abema_episode_id()` が `ABEMA_EPISODE_PREFIX`（`https://abema.tv/video/episode/`）で始まる URL だけを回とみなす。`build_table_hero()` は回のとき（`if episode:`）だけ「▶ 再生」と `mj-saikyo-hero-link` を出し、それ以外の URL は `elif table.video:` の枝で `VIDEO_LABEL`（「ABEMA で見る」）のリンクだけを出す（#502 の扱い）。警告は `attach_videos()` の `if table.video and not abema_episode_id(table.video):`

### 起票・コメント・文書

- 起票: #577「最強戦の卓の『▶ 再生』を、卓ごとの動画が無い間は ABEMA のライブ枠に自動で切り替える」（分野: 自動化・分野: UI/UX・対象: saikyo）。決定1〜3、上の表と理由、作るときに決めること、関係（#549・#573・#572・#502・abema-book.md）
- #549 にコメント: 決定4・5と、work/1015-skr-pub の出方（「▶ 再生」にならない・理由・直すなら公開のマージの指示か #577 かはチャット側が決める）
- docs/decisions/saikyo.md: 末尾に `## 2026-10-15（CHAT-1015-SKR-03）` で決定1〜5。SKD-06 の「放送枠の URL は控えない」の行末に「→ 置き換え: 2026-10-15（CHAT-1015-SKR-03）」
- docs/notes/abema-book.md: 「タブ」の表の後に、【3】にライブ枠の URL を入れてよい（動画が出たら消す）と、今のコードでの出方の1段落

## 報告

- 状態: 完了
- ブランチ: work/1015-skr-live
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1015-SKR-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skr-live
- 確認用URL: なし
- マージ: 済（work/1015-skr-live を cloudflare へ fast-forward）
- issue: 起票 #577、コメント #549
- 判断が必要なこと: なし
- 未確認の項目:
  - 新しい issue は #577（期日なし。公開〈#549〉の後に作る）
  - 【3】に URL だけを入れた卓は「▶ 再生」ではなく「ABEMA で見る」になる（#549・#577 に書いた。直す時機はチャット側が決める）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a94b0a64）: https://github.com/retroeater/mj-logs/tree/main/guide/a94b0a64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
