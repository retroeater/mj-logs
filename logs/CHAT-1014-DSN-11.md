# CHAT-1014-DSN-11

- 着手日時: 2026-10-14（JST）
- 対象issue: なし（#363 は参照のみ）
- ブランチ: work/1014-dsn
- 着手時HEAD: 705aa42

## 指示

【Claude作成】Claude Code 向け指示：DSN の振り返りの申送り — マージの前のチャット側のプレビュー確認（別名 URL と「印」）、部品の状態ごとの高さの確かめ、`[hidden]` の決まりを文書に書く Chat-Ref: CHAT-1014-DSN-11 マージ: ドキュメントのみ（文書とログ）なので完了報告のうえ cloudflare へ入れてよい（下の「止まる条件」に当たれば止まる） 貼る時機: いつでも（CHAT-1014-DSN-10 は完了・マージ済み〈522a4323f〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-dsn の作成と push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-dsn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-dsn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。このセッションが新しく始めたものかをログの経過に書く。

目的
DSN（houou/ のデザインの作り直し、DSN-01〜10）の振り返り（2026-10-14）で決めた3つを、機械的に確かめられる手順として文書に書き残す。コードは変えない。issue は起こさない。
決定（2026-10-14、平野さん）

1. 見た目を変える指示では、「マージしてよいか」を平野さんに聞く前に、チャット側がプレビューを見て気になる所を先に挙げる。そのためにチャット側は、ブランチ名の別名 URL を自分で開く（プレビューの URL をログに書く案 (b) は、#363 論点4〈2026-09-29、URL を非公開として扱う〉を覆すので採らない。平野さんが URL を貼る案 (a) も採らない）。別名 URL が古い版を返す問題は、Code がログに「この版で見えるはずの印」を書き、チャット側がクエリ付きで開いてその印を確かめることで防ぐ
2. 見た目を直す指示の確かめに「部品の状態ごと」を足す。開閉・選択・触れたときの情報の有無・再生の前後など、状態ごとに高さを測り、出していない状態でも場所が空いていることを確かめる（DSN-06〜08 で、触れたときの情報の場所が Bootstrap の `[hidden] { display: none !important }` に負けてつぶれていたのを3本の確かめが通り抜けた）
3. `hidden` 属性で消す要素の決まりを書く: Bootstrap Reboot の `[hidden]` は `display: none !important` なので、「出していない間も場所を空ける」要素には `hidden` を使わず、クラスか `aria-hidden` ＋ `visibility` などで切り替える。やむを得ず `hidden` を使うときは `[hidden]` のセレクタで `display` を上書きする（`style.css` に既にある例: `.mj-pager[hidden]`・`.mj-video-card[hidden]`・DSN-09 の `display: grid !important`）

前提（チャット側。平野さんの決定ではない）

* 同じ論点の issue: #363（クローズ済み。論点4「誰でも開けるプレビューの URL は今のままにし、URL を非公開として扱う」）。この指示は URL をどこにも書かないので、#363 の決定と食い違わない。#363 にはコメントしない。ほかに同じ論点の issue が無いことは手順1で確かめる
* 別名 URL が古い版を返すことと、クエリを付けると新しい版が返ったことは `docs/notes/cloudflare.md`「work/ ブランチのプレビュー」付近と CHAT-1012-DSN-03 のログにある（要確認。無ければこの指示で足す）
* 追記先と文面の案（追記先の今の内容を読んでから書く。同じ趣旨の記述があれば置き換え・拡張し、処理を報告に書く）:
   * `docs/notes/chat-side-operations.md`「確認用 URL」の節: 「平野さんに送ってもらう」を次に置き換える。「チャット側はブランチ名の別名 URL（`docs/notes/cloudflare.md`「work/ ブランチのプレビュー」）を自分で開く。別名は古い版を返すことがあるので `?v=<作業ブランチの HEAD の SHA>` のようなクエリを付け、ログの「確認用URL」の印が出ていることを確かめる。見た目を変える指示では、『マージしてよいか』を平野さんに聞く前に、チャット側が 375px 相当（できなければ PC の幅）の明・暗でプレビューを見て、気になる所を先に挙げる」。この文書は警告域（26KB）まで約 900 バイトしか無いので、節の書き換えで収め、超えるなら `docs/notes/chrome-reading.md` へ手順を置いて参照だけ残す。前後のサイズをログに書く
   * `docs/logs/_template.md` の「確認用URL」の行: 「見た目を変えた作業では、この版で見えるはずの印を1つ書く（例: 順位変動の題名が「第43期前期 B1 ▾」。URL は書かない）」を足す。最終報告の「確認用:」の行の書き方は変えない
   * `docs/notes/design.md`「3. 文字・寸法」の表（「文字の拡大」「余白の下限」の並び）: 「部品の状態ごとの高さ」の行を足す。開閉・選択・触れたときの情報の有無・再生の前後の各状態で高さを測り、出していない状態でも場所が空いていること（切れ・つぶれ・ずれが無いこと）を確かめる。適用範囲は「状態で中身が変わる部品」、出典は DSN-09 の経緯でなく `style.css` の該当（`display: grid !important` の行）
   * `docs/notes/design.md`「4. 部品」: 「`hidden` で消す要素」の行を足す（決定の3の内容。出典は `style.css` の `.mj-pager[hidden]`・`.mj-video-card[hidden]` と DSN-09 の上書きの行）
   * `docs/notes/cloud-sessions.md` の「プレビューの URL は非公開…」の行は変えない（受け手側の規則はそのまま）
* CLAUDE.md・docs/handover.md・docs/instruction-template.md は変えない（instruction-template.md は警告域〈14KB〉まで約 500 バイト）
* 決定の記録: `docs/decisions/design-renewal.md` に 2026-10-14（DSN-11）の節として上の「決定」を足す（運用の決まりなので `operations.md` のほうが合えばそちらにし、理由を報告に書く）

手順

1. 同じ論点（プレビューの URL の扱い、見た目の確かめの手順、`hidden` の扱い）の open issue を検索し（クローズ済みも含めて確認）、#363 以外にあれば止まって報告する。追記先の4文書の今の内容とサイズを読む
2. 上の「前提」のとおりに文書を直し、サイズ（前後）をログに書く。`assets-check.yml` の上限（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）に触れないことを確かめる
3. 決定を記録し、cloudflare へマージする（`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1014-dsn:cloudflare`）。マージ後の自動処理（docs のみなので再生成は走らない見込み。走ったら run と結論を書く）を確かめる

止まる条件

* 同じ論点の open issue がある（#363 は除く）
* 追記先の記述と矛盾し、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張する）
* `docs/notes/chat-side-operations.md` が警告域（26KB）を超える（超えるときは上の退避の形にし、それでも超えるなら止まる）
* 未マージの work/ ブランチが、この指示で変える文書の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の検索の結果、変えた文書と箇所、前後のサイズ、決定の記録先がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-DSN-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-DSN-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0: 「指示」欄の末尾は指示文の最後の行と一致
- **このセッションは新しく始めたものではない。** DSN-01〜10 を実行したセッション（同じ Claude-Session）に続けて貼られた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- Chat-Ref の確認: `CHAT-1014-DSN-11` のコミットなし。work/1014-dsn はローカル・リモートとも無い → `git checkout -b work/1014-dsn origin/cloudflare`（705aa42）

### 手順1（検索・読む）

- issue の検索（クローズ済みを含む）: 「プレビュー URL 別名 古い版 チャット側 確認」で #363（クローズ済み）のほかに #529・#291・#527・#532・#485・#186・#422 が出たが、どれも別の論点（#291 は check-run・Chat-Ref・マージ判定の検証のスクリプト化、#422 は画面計測のスクリプト、#529 は新サイトの要件）。「hidden 属性 display none Bootstrap 場所 高さ 確かめ 状態」は 0 件。同じ論点の open issue は無い（止まらない）
- 別名の URL が古い版を返す件は `docs/notes/cloudflare.md` に無く、CHAT-1012-DSN-03 のログ（「別名の URL は、HTML と CSS が前の版で返った（クエリを付けると新しい版）」）にだけあった → この指示で `cloudflare.md` に足した
- `docs/notes/chat-side-operations.md`「確認用 URL」の節は「平野さんに送ってもらう」だった（決定1で置き換える。矛盾ではなく同じ節の置き換え）
- 未マージのブランチ（work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1013-acf-xsize・1014-acf-cron・1014-gds）は、この指示で変える5文書のどれにも触れない

### 手順2（文書）

変えた文書と箇所（バイト数は前 → 後）:

- `docs/notes/chat-side-operations.md`「確認用 URL」の節（25,729 → 26,096。警告域 26,624・上限 28,672 の内側）: 「平野さんに送ってもらう」を、チャット側がブランチ名の別名 URL を自分で開き、`?v=<作業ブランチの HEAD の SHA>` を付けてログの「確認用URL」の印を確かめること、見た目を変える指示ではマージの可否を聞く前に 375px 相当（できなければ PC の幅）の明・暗で見て気になる所を先に挙げることに置き換えた。「ログイン不要でスマホでも開ける」の文は、別名を自分で開く形になったので外して収めた。`docs/notes/chrome-reading.md` への退避は要らなかった
- `docs/notes/cloudflare.md`「work/ ブランチのプレビュー」（64,989 → 65,380）: 別名の URL が前の版を返すことがあること（CHAT-1012-DSN-03 の観察）と、版を確かめるときは版ごとの URL を使うか、別名にクエリを付けて印を見ることを1行足した
- `docs/logs/_template.md` の「確認用URL」の行（6,866 → 7,097）: 見た目を変えた作業では「この版で見えるはずの印」を1つ書く（例: 順位変動の題名が「第43期前期 B1 ▾」。URL は書かない）を足した。最終報告の「確認用:」の書き方は変えていない
- `docs/notes/design.md`（23,323 → 24,567）: 「3. 文字・寸法」の「余白の下限」の次に「部品の状態ごとの高さ」の行（適用範囲は状態で中身が変わる部品、出典は `style.css` の `:is(.mj-houou-trend-tip, .mj-houou-lg-tip)[hidden]` の `display: grid !important`）、「4. 部品」の最後に「`hidden` で消す要素」の行（出典は `.mj-pager[hidden]`・`.mj-video-card[hidden]`〈クラスの `display` に負けずに消す側の例〉と、houou/ の吹き出しの上書き〈場所を空ける側の例〉）
- `docs/notes/cloud-sessions.md`・CLAUDE.md・docs/handover.md・docs/instruction-template.md は変えていない。`assets-check.yml` の上限を持つ4文書のうち変えたのは chat-side-operations.md だけで、警告域の内側

### 手順3（決定・マージ）

- 決定の記録先は `docs/decisions/design-renewal.md`（16,926 → 18,467）の 2026-10-14（DSN-11）の節。3つのうち2つ（状態ごとの高さ・`hidden`）は見た目の確かめで、残る1つ（プレビューの確認）も DSN の振り返りから出た見た目の確認の手順なので、DSN の一連の決定と同じ所に置いた（`operations.md` は 51KB で、運用全般の決定が多い）
- マージ（ドキュメントのみ）: 再 fetch → `git merge-base --is-ancestor origin/cloudflare HEAD` が真 → `git push origin work/1014-dsn:cloudflare`（705aa4237..b5512fef5）。cloudflare に入ったのは文書 5 ファイル（上のとおり）とこのログ
- マージ後の自動処理: 公開対象を検査する（2本）が成功。docs のみなので、ページの再生成は走らなかった

## 報告

- 状態: 完了
- ブランチ: work/1014-dsn
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-DSN-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-dsn
- 確認用URL: なし
- マージ: 済（b5512fef5）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b5512fef）: https://github.com/retroeater/mj-logs/tree/main/guide/b5512fef

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5512fef/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5512fef/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5512fef/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5512fef/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5512fef/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5512fef/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
