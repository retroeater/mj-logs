# CHAT-1104-SKN-04

- 着手日時: 2026-11-05
- 対象issue: #641（重なりが増えたときのコメントのみ）
- ブランチ: work/1104-skn
- 着手時HEAD: 89ef8d9c2

## 指示

【Claude作成】Claude Code 向け指示：SKN-03 の続き（残りの5点を推奨どおりに入れ、条件を満たせば cloudflare へマージ） Chat-Ref: CHAT-1104-SKN-04 マージ: 承認済み（チャットで。下の「止まる条件」に当たらないときだけ） 貼る時機: いつでも（SKN-03 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-skn を続けて使う（SKN-01〜03 の実装の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-SKN-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-SKN-04` を足して push する。

目的
CHAT-1104-SKN-03 の「判断が必要なこと」5点への回答を入れ、SKN-01〜04 の変更を cloudflare にマージする。
決定（2026-11-05、平野さん。5点とも「推奨どおり」）

1. 「対戦成績」タブの URL は `?view=vs`、並びに使う数は同卓の回数（対局済みの卓）のまま（仮置き1を確定）
2. 名前の面のアイコンは pro/ と同じ形・22px・押せる大きさ 44px で、名前と「N回出場」の行の下のまま（仮置き3を確定）
3. 卓のページの出場者のカードの写真の読み上げを「<名前>の個人成績」にそろえる（年度・対局のページと同じ。見た目は変えない）
4. 写真のリンクに、押した瞬間に写真が少し薄くなる反応を付ける（年度・対局・卓のページと、対戦成績タブの写真で同じ形。フォーカスの枠は今のまま）
5. `generate_ryoei_pages.py` も `lib.sns_icons` を直接読むようにする（pro/ を経由しない。pro/ 側に残した `SNS_ICONS` の名前は、ほかに読む所が無ければ消す）
6. マージ: 下の「止まる条件」に当たらなければ cloudflare にマージしてよい

前提（チャット側。平野さんの決定ではない）

* 新旧の生成物の比べ（regenerate-page.yml の compare_ref）は、その時点の `git rev-parse origin/cloudflare` の SHA を渡す
* 2026-11-05 07:54 の mj-logs の actions/morning.md の「1. 失敗など」にある失敗（regenerate-page.yml の work/1104-lgn-release・work/1104-lgn-gnotice・work/1104-skn〈00:13、compare_ref にブランチ名を渡した失敗〉、check-assets.yml の work/1104-lgn-policy、convert-x-posts.yml の cloudflare）は、この変更と無関係として扱う。同じジョブが同じ理由だけで落ちても止まらない
* pro/ の公開ブランチ work/1101-prp-pub との重なりは、pro/ の公開のマージの指示で解く（SKN-02 の決定）。決定5で増えた重なりがあれば #641 に追記する

手順

1. 決定3〜5を入れ、テスト（`python3 -m unittest discover -s scripts/tests` ほか scripts/tests の全体）を通す。docs/notes/saikyo-page-design.md と docs/decisions/saikyo.md（決定1〜6）を直す。
2. 確かめ: `python3 scripts/regenerate.py all --check` で止まる理由が 0 件。pro/・r/ の生成物の差分が 0。saikyo/ の差分が SKN-01〜04 の変更とその日のシートの変化で説明できるものだけ（ページの種類ごとに変わった数をログに書く）。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）。プレビューで決定3・4（読み上げの文字と押した反応）を確かめる。
3. cloudflare にマージし、マージ後の regenerate-page.yml と Workers Builds の check-run の結果を確かめ、本番（https://ryoei.pro/saikyo/players/?name=魚谷侑未&view=vs と 2025年の対局のページ 1つ）で、写真から個人成績へ飛べることと対戦成績タブを確かめる。

止まる条件

* テストが落ちる（この変更で落ちると分かっているテストは直してよい。直せなければ止まる）
* `regenerate.py all --check` に止まる理由がある
* pro/・r/ の生成物に差分が出た、または saikyo/ の差分にこの変更とシートの変化で説明できないものがある（出たファイルと行を全部書く）
* 「対戦成績」タブの勝敗の数が、対戦成績のページ（`?name=A,B`）の数と1組でも合わない
* 差分レビューで bug の指摘がある（直せる範囲は直して見直し、直せなければ止まる）
* 未マージの work/ ブランチ（work/1101-prp-pub を除く）が同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* マージ後の check-run・regenerate-page.yml の失敗のうち、この変更によるもの（上の「前提」の無関係な失敗は除く。無関係なら原因を報告に書いて残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-SKN-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-SKN-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1104-skn
- ログ: https://github.com/retroeater/mj/blob/work/1104-skn/docs/logs/CHAT-1104-SKN-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-skn
- 確認用URL: なし
- マージ: 未
- issue: #641
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c5721c60）: https://github.com/retroeater/mj-logs/tree/main/guide/c5721c60

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5721c60/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/04d7a070.md
