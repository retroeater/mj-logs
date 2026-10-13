# CHAT-1013-SNM-18

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-snm-hide
- 着手時HEAD: 532cb3259

## 指示

【Claude作成】Claude Code 向け指示：「表示」= N の扱いをそろえる直し（work/1013-snm-hide）を、平野さんの判断を記録して cloudflare へマージする。正本の「リーグ」タブを消した後の最初の再生成を確かめる（CHAT-1013-SNM-17 の続き） Chat-Ref: CHAT-1013-SNM-18 マージ: 承認済み（チャットで。2026-10-13 に平野さんが、CHAT-1013-SNM-17 の直しをマージしてよいと承認。下の「止まる条件」に当たれば止まる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-snm-hide の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜17 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1013-snm-hide を続けて使う（CHAT-1013-SNM-17 のログと直しがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-SNM-17 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1013-SNM-18` を足す。

目的
CHAT-1013-SNM-17 で、「プロ」の「表示」が N の人を、本人のページには出さず、ほかの人のページでは名前だけ（写真・X なし）を出す扱いにそろえた（work/1013-snm-hide）。平野さんが判断待ちの点に答え、マージを承認したので、判断を記録してマージする。あわせて、平野さんが正本の「リーグ」タブを消した後の最初の再生成が通ることを確かめる。
決定（2026-10-13、平野さん）

* CHAT-1013-SNM-17 の直しを cloudflare へマージする
* データ・コンシェルジュ（workers/ask/data.json）では、N の人は在籍者とランキングから外し、決勝の順位と /live の行には名前を残す（SNM-17 の直しのまま。houou/ と /live と同じ扱い）
* 旧方式のページ（houou_results・houou_ranking など、「プロ」を読まないページ）は今のままにする（N の人も名前が出る。houou/ のランキングとの食い違いは受け入れる）
* 正本の「リーグ」タブは消した（2026-10-13、平野さん）

前提（チャット側。平野さんの決定ではない）

* CHAT-1013-SNM-17 のログ: テスト 995件 OK。(a) 今のシート（全員 Y・正本の「リーグ」なし）で直す前と後の生成物の差 0（title_pages の `guard()` の引数は cc18b675 で直した）。(b) 佐々木寿人を N にした模擬で、/live は 2,918本のまま、本人のページには出ず、検査は知らせず、道場部ゲストの照合は止まらない
* SNM-17 の時点で、正本の「リーグ」を消した後の regenerate-page.yml の run はまだ無かった（最後は 38348958077〈acb5aa04〉）
* books/ だけは、books_pages が「書籍」95行で前から止まるため、N の人の写真・X が外れていない（作り直せば外れる。#97・#429 の開発凍結のため、この指示では扱わない）
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直したファイルの同じ行・同じ関数を変えていないか確かめ直す。origin/cloudflare を取り込み（必要なときだけ）、テストを通す
2. 取り込んだ後のコードで、今のシートから全ページを1ページずつ生成し、その時点の origin/cloudflare のコードで生成したものとの差を比べる（SNM-17 の (a) と同じ方法）
3. 止まる条件に当たらなければ、docs/decisions/pros.md に上の「決定」を足し、cloudflare へマージする。マージ後の自動再生成（regenerate-page.yml、push）の run と結論を書く。あわせて、正本の「リーグ」を消した後の最初の regenerate-page.yml の run（このマージより前に毎日の実行などがあればそれも）と結論を書く

止まる条件

* 手順2で生成物に差がある（シートの変化で説明できる差は、直す前と後を続けて生成して出ないことを確かめる。説明できない差が1件でもあれば止まる）、または直す前は通ったページが直した後に止まる（books_pages が「書籍」の行数で止まるのは、これまでと同じなら許す）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、直したファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 0章の状態の書き足し、手順1の確かめとテストの結果、手順2の結果、足した決定の箇所がログにある
* マージ後の自動再生成の run と結論、正本の「リーグ」を消した後の最初の再生成の run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章

- 指示欄の末尾は指示文の最後の行と一致している。Chat-Ref の重複なし
- work/1013-snm-hide はローカルとリモートが同じ（532cb325）。それを続けて使った
- CHAT-1013-SNM-17 の `## 報告` の状態は「判断待ち」。末尾に ` / 続き: CHAT-1013-SNM-18` を足した

### 手順1 確かめ

- origin/cloudflare（5cfee13c。work/1012-dsn〈DSN-08〉がマージ済み）を merge で取り込んだ。衝突なし（`scripts/generate_houou_pages.py` は自動で合わさり、`load_profiles()` の在籍の判定〈`active=current in pro_names`〉はそのまま）
- 取り込んだ後、`git branch -r --no-merged origin/cloudflare` のうち、直したファイル（scripts/ の18ファイル）を変えているブランチは無い（dsn はマージ済みで外れた）
- `python3 -m unittest discover -s scripts/tests`: 995件 OK

### 手順2 全ページの生成（SNM-17 の (a) と同じ方法）

- 今のシート（「プロ」全員 Y・正本の「リーグ」なし）で、origin/cloudflare の scripts/ と取り込んだ後の scripts/ を続けて、全ページを1ページずつ生成した
- 終了コードは同じ（22ページ rc=0、books_pages は前からの「書籍」95行で rc=3）。生成物の木（scripts/ を除く）は同じ（dbdf3a7b）で、HEAD とも差 0

### 手順3 決定・マージ・再生成

- 決定: `docs/decisions/pros.md` に「2026-10-13（CHAT-1013-SNM-18）」の節を足した
- 正本の「リーグ」を消した後の最初の regenerate-page.yml: run 38350905330（push、2026-10-13 05:20 UTC、86979e3d〈dsn のマージ〉）。SNM-17 の着手時（この run より前）に「リーグ」が消えていたのを確かめているので、これが消した後の最初の run。対象は houou/（正本の「プロ」「鳳凰」を読む）で、ジョブは success、「対象ページを再生成」success、コミットは「変更なし」。毎日の予約実行は、消した後まだ無い

## 報告

- 状態: 対応中
- ブランチ: work/1013-snm-hide
- ログ: https://github.com/retroeater/mj/blob/work/1013-snm-hide/docs/logs/CHAT-1013-SNM-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-hide
- 確認用URL:
- マージ: 未
- issue: なし
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj feff70b6）: https://github.com/retroeater/mj-logs/tree/main/guide/feff70b6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/feff70b6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/feff70b6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/feff70b6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/feff70b6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/feff70b6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/feff70b6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
