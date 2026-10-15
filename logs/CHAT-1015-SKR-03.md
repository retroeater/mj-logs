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

## 報告

- 状態: 作業中
- ブランチ: work/1015-skr-live
- ログ: https://github.com/retroeater/mj/blob/work/1015-skr-live/docs/logs/CHAT-1015-SKR-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skr-live
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e9d5efe8）: https://github.com/retroeater/mj-logs/tree/main/guide/e9d5efe8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
