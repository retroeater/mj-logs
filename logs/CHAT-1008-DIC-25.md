# CHAT-1008-DIC-25

- 着手日時: 2026-10-11
- 対象issue: #515・#522
- ブランチ: work/1008-dic
- 着手時HEAD: a5c59f89

## 指示

【Claude作成】Claude Code 向け指示：DIC-24 の続き。辞書の告知動画を v2 で確定し、制作のスクリプトと文書をマージする Chat-Ref: CHAT-1008-DIC-25 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1008-DIC-24 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-24 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-24 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
平野さんが辞書の告知動画を v2 で確定し、X に予約投稿した。`docs/notes/page-announcement.md`「進め方」の5のとおり、制作のスクリプトと文書を cloudflare へマージする。動画はリポジトリに入れない。
決定（2026-10-11、平野さん）

* 告知動画は v2（`dictionary-promo-v2.mp4`、25.0秒）で確定する
* X に予約投稿した（2026-10-11 12:00 に送信）。投稿文は平野さんが直した次の文面（入力したとおり）:


```
リソース「辞書」を更新しました。
https://ryoei.pro/resource_dictionary.html

・麻雀用語1870語をスマホ・PCで一発変換
・Windows・Mac・Android（Gboard）に対応
・Mリーグの全チーム・全選手を追加

```

* 制作のスクリプトと文書をマージする

前提（チャット側。平野さんの決定ではない）

* 文書に書くこと（追記先の今の内容を読んでから。容量は CLAUDE.md の更新ルールのとおり測る）:
   * `docs/notes/page-announcement.md`「実例」の表に辞書の行（2026-10-11、25秒・白地・操作デモ〈Gboard を押す〉とカテゴリの語数、`scripts/promo_video/dictionary/`）。「投稿文の型」の実例は増やさない（型の元は順位変動のまま）
   * 同じ文書の「次に作る時に考える点」の「X のサムネイルは動画の最初のコマ」は、辞書の動画で最初のコマからタイトルをはっきり出す形をとった（平野さんの決定、2026-10-10）ことを短く足すか、型の「告知動画の型」の形式の行へ移す。どちらにしたか報告する
   * 作り直しの手順は、辞書のページの資料（無ければ `docs/notes/static-generation.md` の辞書の行の近く、または `scripts/promo_video/dictionary/` の README）に「告知動画」として短く書く。置き場所は実物に合わせ、報告する
* 投稿文の「Mリーグの全チーム・全選手を追加」が辞書の中身と合うかを確かめる（予約の送信は 2026-10-11 12:00 なので、合わなければその前に平野さんが直せるよう、報告の「判断が必要なこと」の先頭に書く）。確かめ方: 「辞書」タブの Mリーグのチーム名と選手名が、Mリーグ公式（または `docs/decisions/` の 2026-27 シーズンの決定）のチーム数・選手数と一致するか、連盟プロとの重複分を含めて辞書のどこかに全員入っているか。合っていれば、合っていたことだけを書く
* 曲・撮影の共有の部品（`scripts/promo_video/title/`・`lib/`）は変えていない（DIC-23・24 の報告のとおり）。差分が `scripts/promo_video/dictionary/` と docs だけであることを確かめる

手順

1. 確かめる: CHAT-1008-DIC-24 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-25` を足す。上の決定を `docs/decisions/` に足す。比較（`/compare/cloudflare...work/1008-dic`）の差分が `scripts/promo_video/dictionary/`・`docs/` だけであることを確かめる。前提の投稿文の確かめを行う。
2. 書く: 前提のとおり文書に書く。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）。`regenerate-page.yml` は帰り道の件で止まることがある。止まったら、止まった場所が `video_wayhome` で今回の変更と無関係であることを確かめて書く。

止まる条件

* CHAT-1008-DIC-24 の状態が「判断待ち」でない
* 差分に `scripts/promo_video/dictionary/`・`docs/` 以外の変更がある（共有の部品の変更を含む）
* 追記先が容量の上限を超える
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* 投稿文の確かめで合わない点が見つかっても止まらない（報告の先頭に書いてマージまで進める）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- `git log --all --grep="CHAT-1008-DIC-25"` は0件。CHAT-1008-DIC-24 の状態は「判断待ち」
- ローカルの work/1008-dic は origin/work/1008-dic（a5c59f89）と一致。cloudflare は祖先でなかったので、ログを push した後に `git merge origin/cloudflare`（衝突なし）

### 手順1（確かめ）

- DIC-24 のログの状態に ` / 続き: CHAT-1008-DIC-25` を足し、`docs/decisions/features.md` に DIC-25 の決定を足した
- 比較の差分（`git diff --name-only origin/cloudflare...HEAD`）は `docs/`（decisions・logs・notes/page-announcement.md）と `scripts/promo_video/dictionary/` だけ。`scripts/promo_video/title/`・`houou_race/` は変えていない（`lib/` はまだ無い）
- 投稿文の「Mリーグの全チーム・全選手を追加」の確かめ: **合っていた。** `dic/mleague.json`（本番と同じ版）に 10チーム（EX風林火山・U-NEXT Pirates・KONAMI麻雀格闘倶楽部・KADOKAWAサクラナイツ・渋谷ABEMAS・赤坂ドリブンズ・EARTH JETS・セガサミーフェニックス・BEAST X・TEAM RAIDEN / 雷電）と各4名の40名が入っている。2026-27 シーズンの Mリーガーは全10チーム・40名（Mリーグ機構の 2026-07-02 のお知らせ「Mリーガー全40名との選手契約合意」）で、チームごとの顔ぶれ（新加入の尻無濱航・佐野ひなこ・朝倉康心を含む）も一致した。連盟所属の選手は Mリーグのカテゴリにも入っている。Mリーグ公式のサイトはこのセッションのネットワークから開けなかったため、Web 検索で出たお知らせ・報道の一覧と照らした

### 手順2（書く）

- `docs/notes/page-announcement.md`:
  - 「実例」の表に辞書の行（2026-10-11、25秒・白地・操作デモ〈Gboard を押す〉とカテゴリの語数、`scripts/promo_video/dictionary/`）を足した。「投稿文の型」の実例は増やしていない
  - 「次に作る時に考える点」の「X のサムネイルは動画の最初のコマ」は、**型の「告知動画の型」へ移した**（「最初のコマ: X のサムネイルになるので、タイトルはフェードインさせず最初のコマからはっきり出す（辞書の動画で採用、平野さん、2026-10-10）」。テロップの行の前）。「次に作る時に考える点」からは消した
  - 容量: 6,005 バイト（上限の対象外）
- 作り直しの手順: 辞書のページの資料（`docs/notes/` の辞書の文書）が無く、`static-generation.md` の辞書の行は「ページの一覧」の表の中（未マージの work/1008-hou が同じ節を変えている）なので、**`scripts/promo_video/dictionary/README.md`** に「告知動画」として書いた（houou-race.md「告知動画」と同じ項目: 確定版・作り直しのコマンド・借りる部品・撮り方・語数）。`scripts/` は `.assetsignore` で公開されない

## 報告

- 状態: 完了
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1008-DIC-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 済（cloudflare へ push した SHA は最終報告の「ログ（公開）」の行）
- issue: #515・#522（コメントはしていない）
- 判断が必要なこと: なし（投稿文の「Mリーグの全チーム・全選手を追加」は辞書の中身と合っていた）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 396a7262）: https://github.com/retroeater/mj-logs/tree/main/guide/396a7262

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/21efaaec.md
