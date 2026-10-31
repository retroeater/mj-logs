# CHAT-1031-PRO-09

- 着手日時: 2026-10-31
- 対象issue: #219・#641
- ブランチ: work/1031-pro
- 着手時HEAD: 91f5b7b

## 指示

【Claude作成】Claude Code 向け指示：PRO-05 の仮置き G1〜G9 を確定し、work/1031-pro（pro/ の作り直し、未公開）を cloudflare へマージする
Chat-Ref: CHAT-1031-PRO-09
マージ: 承認済み（チャットで。2026-10-31 に平野さんが、work/1031-pro を未公開のまま cloudflare へマージしてよいと単独の問いで答えた。下の「止まる条件」に1つでも当たればマージしない）
貼る時機: いつでも（CHAT-1031-PRO-05 のセッションの続きでも、新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-pro への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1031-pro を続けて使う（PRO-05 のマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突（4ffd8a3 の `all` の再生成で入った pro/ 以外のページを含む）は取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-05 のログ（作業ブランチの `docs/logs/CHAT-1031-PRO-05.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、その状態の行の末尾に ` / 続き: CHAT-1031-PRO-09` を足す。マージの前に CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」「ブランチを削除するとき」を読む

## 目的
PRO-05 の「判断が必要なこと」への回答を記録し、pro/ の作り直し（トップの列と絞り込みのシート、個別ページの簡素化、獲得タイトル・放送対局の2面、選手別 OGP の撤去）を未公開のまま cloudflare へマージする。公開（#641）と「今後の予定」の面（CHAT-1031-PRO-08 の調べの後）はこの指示では行わない。

### 決定（2026-10-31、平野さん）
- 仮置き G1〜G9 は、すべて PRO-05 の今の形で確定する（G1 同じ年の獲得タイトルは title/ の大会の並び〈決勝の日付順は対局日の調べ #408 の後に見直す〉／G2 優勝0回で決勝に出た選手は「獲得タイトル」の下に「すべての決勝進出」の1行だけ／G3 正式名称は title/ の期のページの名前「第42期鳳凰戦」の形／G4 長い名前（360px で2名）は2行に折り返す／G5 放送の題は1行で入らない分は「…」／G6 SNS のアイコンは字の色のインライン SVG／G7 「すべて（N件）」は役を合わせた数で1つ／G8 誕生日・女流桜花の絞り込みは1行と「解除」だけ〈シートには出さない〉／G9 名前の面のチップは押せる大きさ 44px）
- 放送対局の役の振り分け（/live の「対局者」「実況」「解説」の列をそのまま使う）は合っている（蒼井ゆりかの「第16期麻雀グランプリMAX ベスト16 A卓」「第33期麻雀マスターズ」が実況・解説に入るのは正しい、と平野さんが確かめた）
- work/1031-pro を cloudflare へマージしてよい（未公開のまま。選手別 OGP の画像 1,098枚とその生成の手順は本番から消える）

### 前提（チャット側。平野さんの決定ではない）
- マージで本番に入るもの（見込み。差分の種類ごとに確かめる）: `pro/`（1,099ファイル前後）・`img/ogp/pro/` の削除（1,098枚）・`scripts/`（生成・lib・テスト。`regenerate.py` の `SHARED_GENERATORS` の live_pages）・`.github/workflows/regenerate-page.yml`（pro の字体と Pillow の手順の削除。帰り道の手順は変えない）・`style.css` の pro の節・`assets/pro.js`・`docs/`（決定・notes〈ogp.md の「選手」の規則の削除・new-page-checklist.md の `birthday`〉・ログ）
- 取り込みの後、取り込んだ後のスクリプトで全ページを生成し直して差分を確かめる（CLAUDE.md「判断・作業の原則」の共有の定数・関数の行）。pro/ と sitemap の lastmod 以外のページの差分は、取り込んだ cloudflare の変更とシートの変化で説明できるものだけ（4ffd8a3 の古いデータは、生成し直しで今のデータに置き換わる見込み）
- 未公開の確かめ（マージの前に作業ブランチの上で）: `pro/` の全ページに `noindex`、`sitemap.xml`・`llms.txt`・navbar・ほかのページから `/pro/` へのリンクが無い（`git grep -n '/pro/'` で、pro/ の中・docs・scripts・テスト以外の出現を一覧にする）。`img/ogp/pro/` を参照するページが残っていない
- CHAT-1031-PRO-06（404 のページ、work/1031-pro-404）が並行している。`style.css` を両方が変えていれば、後からマージする側で衝突しうる。この指示の側は自分の変更だけを入れ、PRO-06 のブランチには push しない。マージの後に `git merge-tree` で PRO-06 のブランチと cloudflare の衝突の見込みを確かめて報告に書く
- マージ後の本番の確かめ: `https://ryoei.pro/pro/?v=<マージのSHA>` と `https://ryoei.pro/pro/sho.shiratori.23/?v=<マージのSHA>` が 200 で `noindex`、トップの表の上に「五十音: あ」の1行がある。`https://ryoei.pro/img/ogp/pro/` の画像が無くなっている（1枚を名指しして 404）。本番への取得は数件に限る（Cloudflare のレート制限、docs/notes/cloudflare.md）。15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする（チャット側が後で確かめる）
- マージの後、cloudflare に入った push で動く `regenerate-page.yml`・`regenerate-sitemap.yml` の結果を確かめる（失敗したら、今回の変更による失敗か無関係な失敗かを分けて報告に書く。無関係なら原因を書いて残りの手順を進めてよい。自分の変更で落ちるテストは直してよい）
- PRO-05 で #219 の着手中コメントを書き忘れた件は、この指示の着手時に #219 へ着手中コメントを書き、完了時に結果をコメントする（PRO-05 の分はログに書いてあるとおりで、追って直すことは無い）。#641 に「cloudflare へマージした（未公開。選手別 OGP を外し、K8 はトップの質問欄だけに読み替え）」と、この指示の Chat-Ref でコメントする
- 作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」のとおり（関係のない検証の成否に条件づけない）
- 決定は完了時に docs/decisions/pros.md に足す（CLAUDE.md「作業ログ」節）

## 手順
1. 0章の確かめの後、origin/cloudflare を取り込み、全ページを生成し直して差分を種類ごとに確かめる。`python3 -m unittest discover -s scripts/tests`・`--check`（pro_pages）・`check_asset_limits.py`・未公開の確かめを流す
2. 新しい文脈のサブエージェントに、取り込みで変わった分（PRO-05 の最後の 91f5b7b から先。生成物を除く）をレビューさせる（変わった分が生成物とログだけならレビューは要らない。その旨を書く）
3. 止まる条件に当たらなければ cloudflare へマージし（`git push origin work/1031-pro:cloudflare`、直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）、本番の確かめ・push で動くワークフローの結果・PRO-06 のブランチとの衝突の見込み・#219 と #641 へのコメント・作業ブランチの片付けを行う

## 止まる条件（1つでも当たればマージしない）
- 0章の確かめが通らない（PRO-05 のログの状態が「判断待ち」でない）
- 前提が実物と食い違い、決定（上と PRO-01〜05 の「決定」の欄のうち置き換えていないもの）のどれかが守れない（どれが・なぜを書いて止まる）
- 1 の全ページの生成で、pro/・img/ogp/pro/ の削除・sitemap の lastmod 以外のページに、取り込んだ cloudflare の変更とシートの変化で説明できない差分がある（中身を書いて止まる）
- 未公開の確かめで、`pro/` に noindex の無いページがある、sitemap・`llms.txt`・navbar・ほかのページから `/pro/` へのリンクがある、または `img/ogp/pro/` を参照するページが残っている
- テスト・`--check`・`check_asset_limits.py` が通らない、またはレビューで「重大」の指摘が残る（直して取り込み直しても、往復は CLAUDE.md の2回まで）
- 取り込みで、上の作業ブランチの行が解いてよいとしたもの以外の衝突が起きる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- 他セッションの着手中コメントが #219・#641 にある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージせず報告する）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-31）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。PRO-05 のログの状態は「判断待ち」→ 末尾に「 / 続き: CHAT-1031-PRO-09」を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: 0件。CLAUDE.md は origin/cloudflare から変わっていない
- 作業ブランチ: ローカルの work/1031-pro は origin/work/1031-pro（91f5b7b）と一致 → `git checkout work/1031-pro`

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 057e86f6）: https://github.com/retroeater/mj-logs/tree/main/guide/057e86f6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/057e86f6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/057e86f6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/057e86f6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/057e86f6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/057e86f6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/057e86f6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
