# CHAT-1103-TOP-05

- 着手日時: 2026-11-03
- 対象issue: #101
- ブランチ: work/1103-top
- 着手時HEAD: 96f9fa24d

## 指示

【Claude作成】Claude Code 向け指示：未公開の新トップ top/（TOP-02・TOP-03）を cloudflare へマージする（#101） Chat-Ref: CHAT-1103-TOP-05 マージ: 承認済み（チャットで。この指示文を貼ることが承認）。条件: (1) origin/cloudflare を取り込んだ後にテスト（`python3 -m unittest discover -s scripts/tests`）が通る (2) cloudflare との差分が TOP-02・TOP-03 の変更（top/・`scripts/generate_top_page.py`・`assets/top.js`〈あれば〉・`_redirects` の /top の行・`check-assets.yml`・`navbar.js` の1行・`regenerate.py`・`update-live-channel.yml`・テスト・docs）だけで、新しい変更を含まない (3) 取り込んだ後の `regenerate-page.yml` の作業ブランチでの手動実行（`target_page: top_page`）が成功し、top/ 以外の生成物に差分が出ない。1つでも満たさなければマージせずに止まる 貼る時機: いつでも（CHAT-1103-TOP-03 と同じセッションの続きで可） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1103-top を続けて使う（TOP-02・TOP-03 の成果をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行。並行する CHAT-1103-TOP-04 が `_redirects` に /title/hosho の行を足すので、その近くの衝突を含む）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成されたページの衝突は CLAUDE.md「ブランチ運用」のとおり取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1103-TOP-03 のログの `## 報告` の状態が「判断待ち」であることを確かめ、その状態の末尾に `/ 続き: CHAT-1103-TOP-05` を足す（TOP-02 のログはすでに `/ 続き: CHAT-1103-TOP-03`）。

目的
TOP-02・TOP-03 で作った未公開の新トップ `top/`（noindex・メニュー未掲載）を本番に入れ、毎朝の再生成で鮮度を保てることを本番で確かめられるようにする。今のサイトのトップ `index.html` は変えない。
決定（2026-11-03、平野さん。TOP 系列のチャットで）

* TOP-02・TOP-03 の `top/` を cloudflare へマージしてよい（プレビューを見たうえで。マージの可否はほかの問いと分けて聞いた）
* 新着の番組はこのまま（3番組の横断で新しい順に3件。今は3件とも帰り道になるが、それでよい）。TOP-03 の仮置き6項目（位置・行の形・行き先・各話のページの無い回は出さない・読めない番組は外す・導線に「番組」を置かない）は決定とする
* TOP-02 の仮置き（h1「ryoei.pro」・大会日程の段の判定と5件・title/ に無い大会は出さない・誕生日の前後3日・導線の文言・ナビの扱い・説明文）も、TOP-03 で直した点を除き決定とする

前提（チャット側。平野さんの決定ではない）

* TOP-02・TOP-03 のログ（`## 報告` は判断待ち）。マージで本番に入るもの: `top/index.html`（noindex）、`_redirects` の `/top`・`/top/` の行、`navbar.js` の白地の対象パスに `/top/`、`regenerate.py` の `top_page`、`update-live-channel.yml` の `target_page` に `top_page`。マージで自動の再生成（`regenerate-page.yml`、CLAUDE.md「構成」）が走ると、シート側のその日の変化による他ページの差分が混ざることがある（この指示の差分と分けて報告する）
* 並行する CHAT-1103-TOP-04（work/1103-top-hosho）が `_redirects` の title/ の行・`docs/decisions/title.md`・`docs/notes/title-pages.md` を変えてマージする見込み。どちらが先にマージしても、取り込みで両方を残す

手順

1. origin/cloudflare を取り込み（上の衝突の扱い）、マージの条件 (1)〜(3) を確かめる。`docs/decisions/top.md` に上の「決定」を足す（ログと同じコミットでよい）
2. 条件を満たせば `git push origin work/1103-top:cloudflare` でマージする（push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD`）
3. 本番で `https://ryoei.pro/top/?v=<未使用の値>` が 200・noindex・題「日本プロ麻雀連盟の選手と大会のデータ | ryoei.pro」で、`/top` が `/top/` へ 301 を返すこと、`https://ryoei.pro/` が今の `index.html` のままであることを確かめる（15分で反映されなければ「未確認の項目」に回して完了にする）。#101 に、マージしたことと、翌朝 04:00 JST の初回の毎朝の再生成で `top_page` が作り直されるかの確かめが残ることをコメントし、着手中を解除する

止まる条件

* TOP-03 が判断待ちでない、または work/1103-top がすでにマージ済み
* マージの条件のどれかを満たさない（満たさなかった条件と中身を書く）
* 上の扱いで解けない衝突が出た
* `https://ryoei.pro/` の内容が変わった（今のトップを変えないため。見つけたら直さずに報告する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 22d902d0）: https://github.com/retroeater/mj-logs/tree/main/guide/22d902d0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/22d902d0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
