# CHAT-1104-PIL-01

- 着手日時: 2026-11-03
- 対象issue: 起票予定
- ブランチ: work/1104-pil
- 着手時HEAD: 68f0411e8

## 指示

【Claude作成】Claude Code 向け指示：Pillow を 12.3.0 から 13.0.0 に上げる（Dependabot alerts の対応） Chat-Ref: CHAT-1104-PIL-01 マージ: 承認済み（チャットで）。ただし下の「止まる条件」を1つでも満たしたらマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
GitHub の Dependabot alerts（2026-10-27〜11-03 の週の通知）が、`scripts/requirements-ogp.txt` の Pillow（`<= 12.3.0`）に既知の脆弱性 29件（Critical 4件を含む）があり 13.0.0 で直ると知らせてきた。Pillow を 13.0.0 に上げ、アラートを解消する。
決定（2026-11-04、平野さん）

* Pillow を今すぐ 13.0.0 に上げる（版とハッシュの照合〈`--require-hashes`〉の形は保つ）。`.github/dependabot.yml` に pip を足すことはこの指示ではしない。四半期の棚卸し（#601）までは待たない
* マージは次の条件をすべて満たしたら承認済み: (1) 差分が `scripts/requirements-ogp.txt`（版とハッシュ）と、非互換への対応で変えた Pillow を使うスクリプト・そのテスト・docs（`docs/decisions/` を含む）だけ (2) テストがすべて成功 (3) 帰り道の OGP 画像を 13.0.0 で作り直しても、コミット済みの画像と寸法が同じ（中身の差は報告する） (4) 作業ブランチで `regenerate-page.yml` の帰り道の手順を dry-run で動かして success

前提（チャット側。平野さんの決定ではない）

* Pillow 13.0.0 の修正は oss-security の告知（2026-10-21）による。多くは信頼できない画像を開くことで起きる（JPEG のバッファ溢れ・TIFF・WMF/EMF・解凍爆弾の検査のすり抜けなど）。ryoei.pro では YouTube のサムネイル（帰り道の OGP 画像）と、RYO の系列で issue（#657）に添付された画像の加工（CHAT-1103-RYO-04/05）で外部の画像を Pillow で開く（要確認）
* Pillow を入れるのは `scripts/requirements-ogp.txt` と `regenerate-page.yml` の帰り道の手順、ほかに RYO の加工スクリプトがこのファイルを使う（CHAT-1103-RYO-05 の報告。要確認）。ほかにも Pillow を使う・入れる箇所が無いか `git grep -n -i 'pillow\|requirements-ogp\|from PIL\|import PIL'`（リポジトリ全体。docs/logs・handover-archive の過去の行は除く）で洗い出す
* 13.0.0 はメジャー版。外された・変わった API（定数・`Image.ANTIALIAS` 系の名前、`resize` の既定、Python の対応版など）を上流のリリースノートで確かめ、使っている箇所に当たるかを表にする
* `docs/decisions/security.md` の「Dependabot のメジャー版は四半期の棚卸しでまとめて見る」は github-actions の PR の扱い。今回は平野さんの決定で手で上げる（NOD-01 の action の手での版上げと同じ扱い）。`docs/decisions/pros.md` の「Pillow の取得は同じ版（12.3.0）・ハッシュの照合つき」の行は、版の数字を置き換える旨を security.md か pros.md に決定として足す（書き場所は実物に合わせてよい）
* `docs/notes/github-actions.md`・`static-generation.md` などの版の記述（12.3.0）も `git grep -n '12\.3\.0'` で洗い出して直す（過去の決定・ログの行は除く）

手順

1. 同じ論点（Pillow の版上げ・Dependabot alerts）の open issue・未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）を確かめる。あれば止まって報告する。無ければ起票する（題: 「Pillow を 13.0.0 に上げる（Dependabot alerts）」。本文に上の目的と、修正の件数の内訳〈Dependabot の画面の件数〉を書く。alerts の一覧は `gh api repos/retroeater/mj/dependabot/alerts?state=open` で読めれば `## 経過` に表〈番号・GHSA/CVE・重大度・パッケージ・ファイル〉を全件書く。権限で読めなければその旨を書く）
2. `scripts/requirements-ogp.txt` を 13.0.0 にし、ハッシュを PyPI の 13.0.0 の配布物から取り直す（Actions の runner と、手元のテストで使う Python の版の wheel を含める。取り方を報告に書く）。前提の洗い出しの表を作り、非互換に当たる箇所があれば直す。テストを全部流す
3. 確かめ: (a) `build_wayhome_ogp.py` を 13.0.0 で動かし、コミット済みの `img/ogp/wayhome/index-*.jpg` と寸法（1200×630）・ファイル名が同じか、中身のバイトの差の有無（差があれば画素の差の最大値）を報告する。作り直した画像はコミットしない（差が無ければ不要、あれば止まる条件のとおり） (b) `build_ogp_image.py --check` は環境のフォントで一致しないことがある（docs/notes/static-generation.md）。12.3.0 と 13.0.0 の `render()` の出力同士を比べる (c) 作業ブランチで `regenerate-page.yml` を帰り道の手順が動く入力・dry-run で手動実行し success を確かめる（dry-run の入力の名前と、帰り道の手順が実際に動いたかを実行ログで確かめる。dry-run で帰り道の手順が動かないなら、動かせる別の安全な方法を案として書いて止まる）。条件をすべて満たせば cloudflare にマージし、マージ後の check-run と、Dependabot alerts が閉じたか（読めれば）を報告し、手順1の issue をクローズする（閉じない alerts があれば issue は閉じずに残りを書く）

止まる条件

* 前提が食い違う（Pillow を入れる経路が上と違う、13.0.0 が PyPI に無い・告知と版が違う）
* 同じ論点の open issue か、未マージの work/ ブランチが `scripts/requirements-ogp.txt`・`regenerate-page.yml` の Pillow の手順・Pillow を使うスクリプトの同じ行・関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらずログに書く）
* 差分が決定 (1) の範囲を超える（ワークフローの変更が要る、ほかの依存の版も上げる必要がある、Python の版を変える必要がある など）
* テストが1件でも失敗する（自分の変更で落ちると分かっているテストは直してよい）
* 帰り道の OGP 画像の寸法・ファイル名が変わる、または中身に差が出る（差の内容を報告して止まる）
* `regenerate-page.yml` の手動実行が success にならない。ただし mj-logs の actions/morning.md の最新の節に出ている失敗と同じジョブ・同じメッセージだけで落ちたときは、無関係として原因を報告に書いたうえで進めてよい
* マージ後の check-run の失敗は、今回の変更による失敗なら報告して止まる（直しをマージしない）。無関係な失敗なら原因を報告に書いて残りの手順を進めてよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PIL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PIL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d772aeb2）: https://github.com/retroeater/mj-logs/tree/main/guide/d772aeb2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d772aeb2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/68f0411e.md
