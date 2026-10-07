# CHAT-1007-LGR-18

- 着手日時: 2026-10-07
- 対象issue: #507（閉じたまま）
- ブランチ: work/1007-lgr-promo
- 着手時HEAD: dcdb6522

## 指示

【Claude作成】Claude Code 向け指示：「鳳凰戦 順位変動」の告知動画を初版のまま確定とし、制作のスクリプトと文書を cloudflare へマージして閉じる（動画そのものは入れない） Chat-Ref: CHAT-1007-LGR-18 マージ: 承認済み（チャットで、2026-10-07。チャット側が「このままでよければ、制作の道具と文書をマージして閉じる指示文にする」と伝え、平野さんが動画を見て「このまま」と答えた）。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1007-LGR-17 のセッションの続きに貼ってよい） 作業ブランチ: クラウドセッションで実行する。未マージの work/1007-lgr-promo を続けて使う（CHAT-1007-LGR-17 の制作のスクリプトと文書をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-lgr-promo への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1007-LGR-17 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1007-LGR-17 で作った告知動画の初版を、平野さんが見て「このまま」と決めた。決定を記録し、制作のスクリプトと文書を cloudflare へマージして、この流れを閉じる。
決定（2026-10-07、平野さん）

* 告知動画は初版（houou-race-promo-v1.mp4。24秒・1080×1920・音楽つき）のまま確定する
* 最終結果の画面に出る「もう一度見る」の矢印は、このまま（消さない）
* 早送りの速さは、このまま（3倍）
* 曲は、このまま（タイトル戦の告知と同じ曲）
* X への投稿は平野さんが行う。投稿の文は「鳳凰戦「順位変動」を公開しました。」で始めて、ページの URL を入れる予定

前提（チャット側。平野さんの決定ではない）

* CHAT-1007-LGR-17 のログ（mj-logs）で確かめたこと: 状態は判断待ち、作業ブランチ work/1007-lgr-promo に 726ef4a2（`scripts/promo_video/houou_race/` と docs/notes/houou-race.md「告知動画」）、`scripts/promo_video/title/` に差分は無い、動画・連番・フォント・gsap はコミットしていない。今の中身は実物で確かめる
* この指示は CHAT-1007-LGR-17 の続き。CHAT-1007-LGR-17 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1007-LGR-18` を足す（docs/notes/branch-operations.md「作業ログの寿命」）
* 動画そのものはリポジトリに入れない（タイトル戦の告知と同じ扱い。平野さんの端末に保存する）
* docs/notes/houou-race.md「告知動画」に、確定した版（初版のまま、曲、早送りの倍率、矢印を残したこと）と作り直しの手順が書いてあることを確かめ、足りなければ足す。決定は docs/decisions/houou.md に新しい節として足す
* 撮る側の時計の進め方を `scripts/promo_video/lib/` へまとめる案（CHAT-1007-LGR-17 の報告）は、今回は行わない。次に告知動画を作る時に考える（文書に1行残す）
* マージで動く自動処理の見込み: 変わるのは `scripts/promo_video/houou_race/` と docs/ だけで、配信するファイルは変わらない。docs/ の外を含む push なのでデプロイが1回走る（表示は変わらない）。ページの再生成（regenerate-page.yml）は、生成スクリプトと `scripts/lib/` を変えないので、この変更では走らない見込み（走って、ほかのページにシートの変化の差分が出ても、この指示とは関係が無い）
* 新しい issue は起票しない。#507（閉じたまま）に、確定とマージの SHA をコメントで残す
* 作業ブランチの片付けは、マージ済みの `work/*` を消す定時のワークフローに任せる（docs/notes/cloud-sessions.md「ブランチの削除」）
* 使う skill は無い

手順

1. 続きの印と記録。CHAT-1007-LGR-17 のログの状態に続きを足す。決定を docs/decisions/houou.md に書き、docs/notes/houou-race.md「告知動画」を確定の内容にそろえる（どちらも今の内容を読んでから）
2. マージの前の確かめ。origin/cloudflare を取り込み、cloudflare に入る差分が `scripts/promo_video/houou_race/` と docs/ だけであること、動画・画像の連番・フォントなどの素材が含まれていないこと、`scripts/promo_video/title/` に差分が無いことを確かめる。unittest と CLAUDE.md の検証を通す
3. マージして閉じる。止まる条件に当たらなければ cloudflare へマージし、check-run を確かめる（待つのは15分まで。超えたらその時点の状態を `## 経過` に書いて先へ進む）。#507 にコメントする

止まる条件

* CHAT-1007-LGR-17 の状態が判断待ちでない
* cloudflare に入る差分が、`scripts/promo_video/houou_race/` と docs/ のほかに及ぶ
* 動画・連番の画像・フォント・gsap などの素材がコミットに含まれている（どのファイルかを書いて止まる）
* `scripts/promo_video/title/` に差分がある
* unittest か CLAUDE.md の検証が通らない
* origin/cloudflare の取り込みで、自分で直せない衝突が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。ほかのセッションの push と重なって拒否されたときは、取り込み直して差分を確かめ、もう一度だけ行ってよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。残る論点が無ければ状態は「完了」（「判断が必要なこと」「未確認の項目」は「なし」）。マージの SHA、入った差分の範囲、作り直しの手順の場所を `## 経過` に書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-LGR-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-LGR-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1007-LGR-18"` は0件（同じチャットの LGR の続き）
- ブランチ: ローカルの work/1007-lgr-promo は origin/work/1007-lgr-promo と同じ（dcdb6522）。そのまま使う。origin/cloudflare は HEAD の祖先でない（取り込みはログの push の後）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致。CHAT-1007-LGR-17 の状態は「判断待ち」
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1007-lgr-promo
- ログ: https://github.com/retroeater/mj/blob/work/1007-lgr-promo/docs/logs/CHAT-1007-LGR-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-lgr-promo
- 確認用URL: なし（配信するファイルは変わらない）
- マージ: 未
- issue: #507（閉じたまま）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c201eba8）: https://github.com/retroeater/mj-logs/tree/main/guide/c201eba8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c201eba8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c201eba8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c201eba8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c201eba8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c201eba8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c201eba8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
