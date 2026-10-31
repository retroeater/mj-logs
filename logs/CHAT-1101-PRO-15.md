# CHAT-1101-PRO-15

- 着手日時: 2026-11-01
- 対象issue: #219・#641・#107
- ブランチ: work/1031-pro-sched
- 着手時HEAD: 17cf429

## 指示

【Claude作成】Claude Code 向け指示：「今後の予定」（work/1031-pro-sched、未公開）を cloudflare へマージし、#107 に論点を1つ足す
Chat-Ref: CHAT-1101-PRO-15
マージ: 承認済み（チャットで。2026-11-01 に平野さんが、work/1031-pro-sched を未公開のまま cloudflare へマージしてよいと単独の問いで答えた。下の「止まる条件」に1つでも当たればマージしない）
貼る時機: いつでも（CHAT-1031-PRO-12 のセッションの続きでも、新しいセッションでもよい）。CHAT-1031-PRO-13・PRO-14 と前後してよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-pro-sched への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1031-pro-sched を続けて使う（PRO-12 のマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-12 のログ（作業ブランチの `docs/logs/CHAT-1031-PRO-12.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、その状態の行の末尾に ` / 続き: CHAT-1101-PRO-15` を足す。マージの前に CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」「ブランチを削除するとき」を読む

## 目的
PRO-12 の「判断が必要なこと」への回答を記録し、個別ページの「今後の予定」と毎朝の生成への `pro_pages` の追加を、未公開のまま cloudflare へマージする。あわせて、濃色固定のページの明暗の対応を新サイトの UI 方針の論点として #107 に残す。

### 決定（2026-11-01、平野さん）
- 最強戦の行は、今は時刻なしでよい（時刻を出すなら ABEMA ブックなどから足す別の作業）
- 放送対局・最強戦のデータが読めない日は、pro/ の生成を止める（PRO-12 の今の作り。前の日のページのまま、失敗のメールが届く）
- work/1031-pro-sched を cloudflare へマージしてよい（pro/ は未公開のまま。マージの後は毎朝の `update-live-channel.yml` の再生成で pro/ も作り直してコミットする）
- 濃色固定のページ（live/・wayhome/ など）も明暗の両方に対応するかは、新サイトの UI 方針（#107 のダークモード）で検討する（今は濃色固定のまま。2026-10-31 の CHAT-1031-PRO-14 の決定のとおり、ドロップダウンも常に暗い）

### 前提（チャット側。平野さんの決定ではない。要確認）
- 生成を止める扱いの条件: pro/ の生成が止まっても、同じ毎朝の再生成の `live_pages`・`title_pages` は止まらずに生成・コミットされること。`regenerate.py` が複数のページ名を受けたとき1つの失敗で残りを止めるかを実物で確かめ、止めるなら pro_pages の失敗が他を止めない形にする（ジョブを失敗にして知らせるのは保つ）。確かめはテストか作業ブランチの手動実行（pro_pages だけが失敗する状態を作れる方法で）で行う
- PRO-12 の最終報告の後に origin/cloudflare に入った変更（PRO-13・PRO-14 など）を取り込み、全ページを生成し直して差分を確かめる。pro/ と sitemap の lastmod 以外のページの差分は、取り込んだ変更とシートの変化で説明できるものだけ
- 未公開の確かめ: `pro/` の全ページに `noindex`、sitemap・`llms.txt`・navbar・ほかのページから `/pro/` へのリンクが無い（PRO-09 と同じ方法）
- マージ後の本番の確かめ: `https://ryoei.pro/pro/shota.akutsu.34/?v=<マージのSHA>` が 200・noindex で「今後の予定」の見出しがある（本番への取得は数件に限る。Cloudflare のレート制限、docs/notes/cloudflare.md）。15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする（チャット側が後で確かめる）
- マージの後、cloudflare に入った push で動くワークフローの結果を確かめる（失敗したら、今回の変更による失敗か無関係な失敗かを分けて報告に書く。無関係なら原因を書いて残りの手順を進めてよい）。翌朝の `update-live-channel.yml` の初回の実行（pro/ を含む再生成）の日時を、報告の予約実行の初回の欄に書く（チャット側が確かめる）
- #107 へのコメント: 上の決定の4つ目を「新サイトの UI 方針の論点」として、経緯（2026-09-12 の濃色固定の決定と理由、PRO-11・PRO-14 でドロップダウンを常に暗くしたこと、両方に対応するときのメリット・デメリット）を3〜5行で書く。#107 に他セッションの着手中コメントがあれば書かずに報告する
- 作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」のとおり
- 決定は完了時に docs/decisions/pros.md に足す（4つ目は docs/decisions/design-renewal.md）

## 手順
1. 0章の確かめの後、生成を止める扱いの条件（pro_pages の失敗が live_pages・title_pages を止めない）を確かめ、要れば直してテストを足す
2. origin/cloudflare を取り込み、全ページの生成と差分・テスト・`--check`・`check_asset_limits.py`・未公開の確かめを行う。1 でコードを変えたときは、新しい文脈のサブエージェントにその差分をレビューさせる
3. 止まる条件に当たらなければ cloudflare へマージし（`git push origin work/1031-pro-sched:cloudflare`、直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）、本番の確かめ・push で動くワークフローの結果・#219 と #107 へのコメント・作業ブランチの片付けを行う

## 止まる条件（1つでも当たればマージしない）
- 0章の確かめが通らない（PRO-12 のログの状態が「判断待ち」でない）
- 前提が実物と食い違い、決定のどれかが守れない（どれが・なぜを書いて止まる）
- pro_pages の失敗で live_pages・title_pages の毎朝の生成が止まる形が残る（直せなければ止まる）
- 全ページの生成で、pro/ と sitemap の lastmod 以外のページに、取り込んだ変更とシートの変化で説明できない差分がある
- 未公開の確かめで、noindex の無いページ、または sitemap・`llms.txt`・navbar・ほかのページから `/pro/` へのリンクがある
- テスト・`--check`・`check_asset_limits.py` が通らない、またはレビューで「重大」の指摘が残る（往復は CLAUDE.md の2回まで）
- 取り込みで、上の作業ブランチの行が解いてよいとしたもの以外の衝突が起きる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- 他セッションの着手中コメントが #219 にある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージせず報告する）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRO-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRO-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d750c1c8）: https://github.com/retroeater/mj-logs/tree/main/guide/d750c1c8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d750c1c8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
