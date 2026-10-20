# CHAT-1019-WRR-13

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1019-wrr13
- 着手時HEAD: 9321bdc10

## 指示

【Claude作成】Claude Code 向け指示：告知の前後の変更の決まりを確定し（提案の印を外す）、docs/decisions に記録する。WRR-12 を完了にする（文書だけ）
Chat-Ref: CHAT-1019-WRR-13
マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/notes/page-announcement.md・docs/decisions だけ。コード・生成物は変えない）
貼る時機: いつでも（CHAT-1019-WRR-12 はマージ済み・判断待ち）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrr13 の作成・push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-wrr13 を origin/cloudflare から作る。rebase・force push はしない。`docs/decisions/` の末尾への追記どうしの衝突は、両方の節を残して日付・番号の順に並べて解いてよい。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1019-WRR-12 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら状態を「完了」にし、末尾に ` / 続き: CHAT-1019-WRR-13` を足す。

## 決定（2026-10-20、平野さん）

- WRR-12 の提案（告知の前後の変更）を、次の形で決まりとして確定する: **告知（告知動画）の制作を始めてから投稿までの間に、告知で挙げるページ・機能（質問欄など）を変えるマージを入れるときは、平野さんの明示的な指示・判断を必須とする**。指示文に平野さんの承認が書かれていない変更は、この間はマージしない
- 新しい大会の確かめの観点（同率・境目の値）の置き場所は league-pages-compare.md の末尾のままで確定（WRR-12 の仮置き）

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

- 「制作を始めてから」は、告知動画の初版を作る指示（例 CHAT-1019-WRR-07）に着手した時点とする。終わりは投稿の翌日まで（投稿の直後の直しも同じ扱い）とする（仮置き。報告に書く）
- Code の側の守り方: 告知の期間中に、告知で挙げる機能のファイル（`workers/ask/src/`・その大会の生成のスクリプトなど）を変えてマージする指示で、冒頭の「マージ:」の行に平野さんの承認（日付と「告知の期間中の変更を承認」の趣旨）が無ければ、マージせずに止まって報告する。告知の期間中かどうかは、page-announcement.md の手順に従って、その大会の告知動画の指示のログと投稿の記録から判断する

## 手順

1. docs/notes/page-announcement.md「進め方」の WRR-12 の提案の行を、上の決定の文に置き換える（「提案」の印を外し、「決定（2026-10-20、平野さん）」を付ける）。前提の2点（期間の始まりと終わり、Code の側の守り方）を続けて書く。CLAUDE.md や指示の雛形（docs/instruction-template.md）に告知の期間の確かめを足す必要があるかを見て、要るなら報告の「判断が必要なこと」に案を書く（この指示では変えない）
2. 上の「決定」を docs/decisions の WRR-02〜11 と同じ置き場所に「## 2026-10-20（CHAT-1019-WRR-13）」として足す
3. 容量の検査（`check-assets.yml` と同じもの）を手元で回し、警告域・上限との差を書く。CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。check-assets の成否を書く

## 止まる条件

- 0章の確かめに当たる
- 変更が、冒頭の「マージ:」の行の文書のほかに出る。容量の検査が上限に入る
- 追記先に矛盾する記述がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、期間の始まりと終わりの仮置き、CLAUDE.md・指示の雛形に足す案（要るなら）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRR-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRR-13 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- 識別子確認: 同じ Chat-Ref のコミットは 0 件
- 作業ブランチ: work/1019-wrr13 はローカル・リモートとも無し → `git checkout -b work/1019-wrr13 origin/cloudflare`（9321bdc10）
- 0. 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。CHAT-1019-WRR-12 の状態は「判断待ち」→「完了 / 続き: CHAT-1019-WRR-13」にした
- ガイド文（docs/notes/）の規則の変更だが、この指示には「知見 → 選んだ層 → 理由」の表が無い。WRR-12 の表の「告知した機能を、投稿の直前・直後に本番で変えた → page-announcement.md「進め方」に1行」の行を確定する指示なので、止まらずに進めた
- 1. docs/notes/page-announcement.md「進め方」: WRR-12 の「提案（…）」の行を「決定（2026-10-20、平野さん）: …」に置き換え、字下げで期間（仮置き: 始まりは告知動画の初版の指示に着手した時点、終わりは投稿の翌日まで）と Code の側の守り方（「マージ:」の行に告知の期間中の変更の承認が無ければ止まる。期間は告知動画の指示のログと「実例」の投稿の日時で判断）の2行を足した。矛盾する記述は無し
- CLAUDE.md・docs/instruction-template.md: この指示では変えていない。足す案は報告の「判断が必要なこと」
- 2. docs/decisions/houou.md の末尾に「## 2026-10-20（CHAT-1019-WRR-13）」（WRR-12 は decisions に節を足していないので、WRR-11 の節の次）
- 3. バイト数: page-announcement.md 11735 → 12612、docs/decisions/houou.md 155330 → 156021（対象外）。check-assets の4文書は変えていない: CLAUDE.md 24859（警告域 26624 まで 1765・上限 28672 まで 3813）、handover.md 22937（3687・5735）、chat-side-operations.md 21976（4648・6696）、instruction-template.md 14325（警告域 14336 まで 11・上限 16384 まで 2059）。`scripts/check_asset_limits.py` はすべて OK

## 報告

- 状態: 作業中
- ブランチ: work/1019-wrr13
- ログ: https://github.com/retroeater/mj/blob/work/1019-wrr13/docs/logs/CHAT-1019-WRR-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-wrr13
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9321bdc1）: https://github.com/retroeater/mj-logs/tree/main/guide/9321bdc1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9321bdc1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
