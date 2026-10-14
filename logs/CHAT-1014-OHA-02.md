# CHAT-1014-OHA-02

- 着手日時: 2026-10-14
- 対象issue: なし（検索の結果で追記）
- ブランチ: work/1014-oha-cal
- 着手時HEAD: 66e8343cd

## 指示

【Claude作成】Claude Code 向け指示：handover.md の期日の表を消し、期日はカレンダー「ryoei.pro」を正にする（文書のみ） Chat-Ref: CHAT-1014-OHA-02 マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい（止まる条件に当たらなければ） 貼る時機: いつでも（CHAT-1014-OHA-01 と同じセッションで続けてよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-oha-cal の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-oha-cal を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-oha-cal origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
期日のあるタスクはすべて Google カレンダー「ryoei.pro」で管理し、.md にだけ書かれた日付を無くす。着手・再開はカレンダーから判断するので、handover.md 5章の「期限付き・確認待ちタスク」の表を消し、handover を小さくする。
決定（2026-10-14、平野さん）

* 期日（日付）のあるタスクの正はカレンダー「ryoei.pro」（件名【R#番号】…、作るのはチャット側）。handover.md・CLAUDE.md・docs/notes に期日の一覧は置かない
* handover.md 5章の「期限付き・確認待ちタスク」の表は削除し、「期日のあるタスクはカレンダー「ryoei.pro」が正。handover に日付は書かない」の1〜2行に置き換える
* 予約実行を足す・変える変更では、初回の予約実行の日時を最終報告の「未確認の項目」に書く（チャット側がカレンダーに写し、翌朝の報告で確かめる）。効果の確認日なども同じ

前提（チャット側。平野さんの決定ではない）

* 表の7行（#298 10/21、#513 10/19・10/26、#486 10/30、#485 11/02、#492 11/16、#504 11/30、#97 12/22）は、チャット側が 2026-10-14 にカレンダーに全部あることを確かめた。日付の無い #390（随時）の行は、下の「着手可能な主なもの」の表へ1行で移す（要確認: 既に同趣旨の行があれば置き換え）
* 5章の冒頭「次の会話の順番」の (2)「期日待ちは下の表の期日の順」は「期日待ちはカレンダーの順」に直す。(1) の待ち（#388・#389・#497/#488）は日付でなく条件なので残す
* CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の「docs/handover.md は『現状・ルール・次にやること』のみ」の行に「期日（日付）は書かない（カレンダー「ryoei.pro」が正。チャット側が作る）」の一句を足す。CLAUDE.md「作業ログ」節の「未確認の項目」に、上の決定の3つ目を一句で足す（場所は実物に合わせてよい）
* docs/decisions/operations.md に決定を1行（既存の形に倣う）
* docs/notes/chat-side-operations.md は警告域の直前（26,556 バイト）なので変えない。「期日とカレンダー」節は今の規則のままで足りる

手順

1. 「同じ論点の open issue」（handover の期日・カレンダーへの一本化、#96・#496 を含む）を検索し（クローズ済みも含めて確認）、同じ変更を扱う open issue があれば止まって報告する。あれば着手中コメントを書く（本文・状態・ラベルは変えない）
2. handover.md 5章・CLAUDE.md の該当行を読み、前提のとおり直す。消す表の7行は、ログの「経過」に（消す前の全文を）写す
3. 各文書のサイズ（前後のバイト数）を `## 報告` に書く

止まる条件

* 同じ論点の open issue がある／追記先に矛盾する記述があり、どちらが正か判断が要る（同じ趣旨なら置き換えてよく、処理を報告に書く）
* 表の7行のうち、issue が既にクローズ済みで予定の扱いが分からないものがある（行を消さず止まる）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが handover.md 5章か CLAUDE.md の同じ節を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-OHA-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-OHA-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件（`git log --all --grep="CHAT-1014-OHA-02"`）。OHA は同じセッションの CHAT-1014-OHA-03 で使ったもので、02 はその後に届いた（番号の順と貼られた順が逆）
- 作業ブランチ: ローカル・リモートとも無いため `git checkout -b work/1014-oha-cal origin/cloudflare`
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも揃っている。「貼る時機」の「CHAT-1014-OHA-01 と同じセッション」は、01 が欠番（OHA-03 の指示文）のため OHA-03 のセッションと読んだ
- 0. 指示欄の末尾は指示文の最後の行と一致

### 消す前の handover.md 5章の冒頭と「期限付き・確認待ちタスク」の表（全文、cloudflare 66e8343cd）

```
**次の会話の順番（2026-10-09）:** (1) 待ち: #388（平野さんが「映画」のタブを作ってから）・#389（平野さんの校正した書籍の一覧の整備）・#497／#488（連盟の予定表で JPMLリーグの「(仮)」が外れてから）。
(2) 期日待ちは下の表の期日の順。決定は `docs/decisions/features.md`・`operations.md`。

**期限付き・確認待ちタスク**（期日の順）

| # | 内容 | 期限・目安 |
|---|---|---|
| #298 | Actions の使用量: 10/7 に Billing を実測済み（10/1〜10/7 で 928 / 3,000 分、請求 $0。平野さんのカレンダーの記録）。写しの移設（mj-logs 側へ）は実装4の後半（`[sync-logs]` の規則・`sync-logs.yml`・`MJ_LOGS_TOKEN` の削除）が残る | 次の Billing の確認は **2026-10-21**。実装4の後半は **2026-10-21** |
| #513 | 作業ログの自動削除の見直しの効果の確認（規則を入れた日 D は 2026-10-08） | **2026-10-19** と **2026-10-26** の週次で測り、#513 へ報告する（指標は `docs/notes/branch-operations.md`「作業ログの寿命」） |
| #486 | Bing の Recommendations | **2026-10-30** に再確認 |
| #485 | 旧表 `jpml_titles.html` の転送と title/ の `?name=` の受け取りを終える | **2026-11-02** に 11/1 の取得（廃止後はじめての値。10/1 は廃止直前の基準値）で旧 URL への着地を見て、十分に減ったら終える（基準は未定。作業は #485 の本文） |
| #492 | ガイド文書の上限の見直し | **11月中旬** |
| #504 | 予約実行を Worker から起動する（段階1・2は済。設計・段階は #504 の本文） | 今の `schedule` を外す予定日 **2026-11-30**（仮） |
| #97 | 書籍ページ開発凍結中の楽天データ保存期限 | **2026-12-22**（最後に取得した2026-09-22の3か月後）までに再取得するか削除する（`docs/notes/books-freeze.md`「楽天の期限」） |
| #390 | 書き込み済みの月の自動更新（予定の書き換え・削除）は、画像の差し替えが起きるまで本番で動いていない（サービスアカウントの削除の権限も未確認） | 随時: 差し替えで #426 に「自動で更新しました」か「自動では更新していません」が出たら、カレンダーの当日以降が画像と合っているかを確かめる |
```

### 確かめたこと

- 同じ論点の issue の検索（handover の期日・カレンダー・ryoei.pro・一本化）: 同じ変更を扱う issue は無い。#96（カレンダーの参照・更新の自動化、open）・#496（issue のクローズで予定を済みにする、open）は別の主題なので止まらない。着手中コメントは書いていない（対象の issue が無いため）
- 表の8行の issue の状態: #298・#513・#486・#485・#492・#504・#97・#390 はすべて open
- 未マージの work/ ブランチ: `work/1014-gds` が CLAUDE.md（「判断・作業の原則」「ブランチ運用」の参照先）・handover.md（2章「自動化」・6章の表）・decisions/operations.md（末尾に追記）を変えている。handover.md 5章と、CLAUDE.md の今回の節（「更新ルール」「作業ログ」「issueの着手ルール」）は変えていないため止まらない。operations.md は末尾の追記どうしなので、後からマージする側で両方を残す衝突になる見込み
- `work/1014-snm-handover` は cloudflare との差がログ1件だけ（まだ handover を変えていない）

### 変更（e552a7d7f）

- handover.md 5章: 表（見出しを含む12行）を消し、「期日のあるタスクはカレンダー「ryoei.pro」が正（件名【R#番号】…、作るのはチャット側）。handover に日付は書かない。」の1行に置き換えた。(2) を「期日待ちはカレンダーの順」に直した。(1) は変えていない
- #390 の行を「着手可能な主なもの」の表の #96 の次へ1行で移した（同趣旨の行は無かった）
- 「最終更新」を 2026-10-14 にし、1行を足して一番古い1行（houou/ の件）を消した（3行以内の規則）
- CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の handover の行に「期日（日付）は書かない（カレンダー「ryoei.pro」が正。チャット側が作る）。」を足した
- CLAUDE.md「作業ログ」節の `## 報告` の行の続きに「予約実行を足す・変える変更では、初回の予約実行の日時（効果の確認日なども）を「未確認の項目」に書く（チャット側がカレンダーに写す）。」を足した
- CLAUDE.md「issueの着手ルール」の「優先順位と状況は issue の本文・ラベル・期日で表す」が今回の決定と食い違うため、同じ趣旨の置き換えとして「issue の本文・ラベルで、期日はカレンダー「ryoei.pro」で表す」に直した
- docs/decisions/operations.md に今回の決定を足した。chat-side-operations.md は変えていない

### 取り込み

- マージ前の確認で origin/cloudflare が CHAT-1014-SNM-22 のマージ（acddd1d81）で進んでいたため `git merge origin/cloudflare` で取り込んだ。
  `docs/decisions/operations.md` の末尾の追記どうしが衝突したので、両方を残して日付・取り込みの順（SNM-22 → OHA-02）に並べて解いた。解いた後の該当箇所:

```
## 2026-10-14（CHAT-1014-SNM-22）

- SNM のチャットの振り返りの知見を申送りにする（機械的に確かめられる手順に落とせるものだけ。抽象的な心得は書かない）

## 2026-10-14（CHAT-1014-OHA-02）

- 期日（日付）のあるタスクの正はカレンダー「ryoei.pro」（件名【R#番号】…、作るのはチャット側）。…
```

- 取り込んだ変更（cloud-sessions.md・instruction-template.md など）は、handover.md 5章と CLAUDE.md には触れていない
- `python3 -m unittest discover -s scripts/tests`: OK
- 途中の節目のコミット（経過を書く）のメッセージを `git commit -m "…"` で渡した（バッククォート・`$` は含まず、展開は起きていない。CLAUDE.md「コミットのルール」の `-F - <<'EOF'` の形から外れた）

## 報告

- 状態: 完了
- ブランチ: work/1014-oha-cal
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-OHA-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-oha-cal
- 確認用URL: なし
- マージ: 済（作業ブランチの先頭を cloudflare へ fast-forward で push。ドキュメントのみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし
- 文書のサイズ（前 → 後、バイト）: CLAUDE.md 22,444 → 22,787・docs/handover.md 23,794 → 22,200・docs/decisions/operations.md 48,977 → 49,789（cloudflare の SNM-22 の追記を含む。今回の分は +602）・docs/notes/chat-side-operations.md 26,556（変えていない）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e076630a）: https://github.com/retroeater/mj-logs/tree/main/guide/e076630a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e076630a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e076630a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e076630a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e076630a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e076630a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e076630a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
