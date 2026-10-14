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

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj acddd1d8）: https://github.com/retroeater/mj-logs/tree/main/guide/acddd1d8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/acddd1d8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/acddd1d8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/acddd1d8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/acddd1d8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/acddd1d8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/acddd1d8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
