# CHAT-1104-SKN-10

- 着手日時: 2026-11-05
- 対象issue: #669（コメントのみ）
- ブランチ: work/1105-skn-gutter
- 着手時HEAD: 275d81111

## 指示

【Claude作成】Claude Code 向け指示：番組の一覧で PC 幅だけ説明文と全幅の並びが左へ 7.5px ずれるのを直し、条件を満たせば cloudflare へマージする Chat-Ref: CHAT-1104-SKN-10 マージ: 承認済み（チャットで。下の「止まる条件」に当たらないときだけ） 貼る時機: いつでも（新しいセッションでも、SKN-09 と同じセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-skn-gutter を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-skn-gutter origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#669 の残りの1つ。番組の一覧（wayhome/・exam/・mtsuku/・study/）で、PC 幅（縦のスクロールバーが幅を取る環境）だけ、説明文と全幅の並びが左へ 7.5px ずれる（CHAT-1104-SKN-08 の調べ: scrollbar-gutter と 100vw の組み合わせ。はみ出しではない。スマートフォンでは起きない）。これを直す。
決定（2026-11-05、平野さん）

* このずれを直す（番組のページは意図した例外の濃色の作りのままで、ずれだけを直す）
* 下の「止まる条件」に当たらなければ cloudflare にマージしてよい

前提（チャット側。平野さんの決定ではない）

* 直し方は実物に合わせて決めてよい（例: 全幅の並びの幅を `100vw` でなく、スクロールバーを除いた幅で取る）。番組のページの濃色の作り・幅（1100px や画面いっぱいの列）は変えない
* 同じ `100vw` の使い方がほかのページ（放送対局・最強戦の卓のページなど）にもあれば、同じずれが出ていないかを測り、出ていれば同じ直し方で直してよい（直したページと測った値をログに書く）
* 測り方: headless Chromium で、縦のスクロールバーが幅を取る設定（例: `--hide-scrollbars` を付けない、または `::-webkit-scrollbar` の幅を持たせる）の 1280px と、390px で、説明文と全幅の並びの左端を測る
* 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す
* その時点の mj-logs の actions/morning.md の「1. 失敗など」にある失敗のうち、work/1105-skn-gutter 以外のブランチのものは、この変更と無関係として扱う

手順

1. 同じ論点の open issue（#669 以外）・他セッションの着手中コメントが無いことを確かめる。上の測り方で直す前のずれ（4つの一覧と、同じ使い方のあるページ）を測ってログに書き、直す。
2. 直した後に同じ測り方で測り、1280px でずれが 0、390px で直す前と同じ位置であることを確かめる。テスト（scripts/tests の全体）と `python3 scripts/regenerate.py all --check` を通す。変わる生成物（CSS だけなら生成物の差分は無い見込み）を確かめる。
3. cloudflare にマージし、マージ後の check-run を確かめ、本番（https://ryoei.pro/wayhome/ ほか3つの一覧）を同じ測り方で測る。#669 に、直したことをコメントする（本文・状態・ラベルは変えない。title/ は TTL に依頼済みなので、#669 は閉じない）。

止まる条件

* 同じ論点の open issue・他セッションの着手中コメントがある
* 直した後も 1280px でずれが残る、または 390px の位置が直す前と変わる（測った値を全部書く）
* 番組のページの見た目に、ずれ以外の差（幅・地の色・題名など）が出た
* テストが落ちる、または `regenerate.py all --check` に止まる理由がある（この変更で落ちると分かっているテストは直してよい。直せなければ止まる）
* 生成物に、この変更とその日のシートの変化で説明できない差分が出た（出たファイルと行を全部書く）
* 未マージの work/ ブランチが同じ CSS の節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* マージ後の check-run の失敗のうち、この変更によるもの（無関係なら原因を報告に書いて残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1105-skn-gutter
- ログ: https://github.com/retroeater/mj/blob/work/1105-skn-gutter/docs/logs/CHAT-1104-SKN-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-skn-gutter
- 確認用URL: なし
- マージ: 未
- issue: #669
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d9c57896）: https://github.com/retroeater/mj-logs/tree/main/guide/d9c57896

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
