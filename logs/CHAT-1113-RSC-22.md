# CHAT-1113-RSC-22

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: 8d06eb325

## 指示

【Claude作成】Claude Code 向け指示：（RSC-21 の再開）書く卓に関わらない名前の警告では止まらずに、旧公式の結果の8件を候補タブに書く
Chat-Ref: CHAT-1113-RSC-22
マージ: ドキュメントのみ（ログ・docs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（RSC-21 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-21 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-22` を足す。

目的
CHAT-1113-RSC-21（中断）の再開。目的は RSC-18〜21 の指示文のとおり。
決定（2026-11-13、平野さん。RSC-21 の判断待ちへの回答）

* 1: 案A。「石渡正譲 → 石渡正志」の名前の警告は今回の書き込みに関わらないので、RSC-21 の dry-run の8件を書く。名前の警告で止まるのは、書く卓の選手に関わるものだけにする（この系列の指示の止まる条件の読み方）
* 「石渡正譲」の登録の重複（TYP-03 の No.25 の確認の途中と見込む）は、平野さんが別に片付ける（この指示では扱わない）

前提（チャット側。平野さんの決定ではない）

* RSC-21 の dry-run の「書く行」は8件。今の dry-run で8件と同じ卓・同じ候補になることを確かめてから書く

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめる（RSC-21 までの着手中のコメントは終わった指示のものとして扱う）
2. dry-run で「書く行」が RSC-21 の8件と同じ（対象キー・候補・確度）で、書く卓の選手に関わる名前の警告が無いことを確かめ、dry_run を外して実行し候補タブに書く。書いた行の表をログに書き、#630 に要約をコメントする
3. RSC-18 の手順3（docs の1文・決定の記録）が済んでいなければ行う。決定1（名前の警告の読み方）も docs/decisions に記録する。「判断が必要なこと」に、平野さんの手作業 (1) 「別名」から2行（トレンディエンジェルたかし・林下清志（ビッグダディ））を消す (2) 候補タブの RSC-16 の古いキーの3行（`2014-08-09 著名人代表決定戦雷神編 決勝`・`2013-08-17 著名人代表決定戦雷神編 決勝`・`2013-05-18 著名人代表決定戦風神編 決勝`）を「却下」にする、を書く

止まる条件

* dry-run の「書く行」が RSC-21 の8件と違う、または書く卓の選手に関わる名前の警告が出る（書かずに止まり、差と理由を書く）
* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: docs（notes・decisions・ログ）。コード・ワークフローが変わったら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし。`RSC` は続き（22）。作業ブランチ: origin/work/1113-rsc は origin/cloudflare の祖先（マージ済み）なので `git merge --ff-only origin/cloudflare` で進めた（8d06eb325）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1113-RSC-21 の `## 報告` の状態を「中断 / 続き: CHAT-1113-RSC-22」にした

### 1. #630

- Open。最後のコメントはこのセッションの RSC-21 の着手中（終わった指示のもの）。着手中をコメントした（issuecomment-6846833906）

### 2. dry-run と本実行（cloudflare）

- dry-run（run 43385341503）: 候補タブ 428行・見出し 一致。「書く行」は8件で、RSC-21 の dry-run（run 43383636129）の8件と対象キー・候補・確度がすべて同じ（両方の表を突き合わせた）
- 名前の警告は1行も出なかった（RSC-21 の「石渡正譲 → 石渡正志」も、今回の実行には無い）
- 本実行（run 43385561912）: 候補タブに 8行を書いた（428行 → 436行）

#### 書いた行（8件）

| 対象キー | 候補 | 確度 | 備考 |
|---|---|---|---|
| 2018-06-30 男子プレミアトーナメント 天鳳ニューパワー ベスト16 | 1位 朝倉康心・2位 山田独歩・3位 ゆうせー・4位 木原浩一 | 照合済み |  |
| 2016-05-21 著名人代表決定戦 決勝 | 1位 堀江貴文・2位 加賀まりこ・3位 たかし・4位 本郷奏多 | 照合済み |  |
| 2015-06-13 女流プロ代表決定戦菊の陣 決勝 | 1位 和泉由希子・2位 松岡千晶・3位 愛内よしえ・4位 波奈美里 | 照合済み |  |
| 2014-05-17 著名人代表決定戦 風神編 決勝 | 1位 萩原聖人・2位 加藤哲郎・3位 ビッグダディ・4位 勝間和代 | 照合済み |  |
| 2014-08-09 著名人代表決定戦 雷神編 決勝 | 1位 藤田晋・2位 片山まさゆき・3位 じゃい・4位 金子昇 | 照合済み |  |
| 2014-09-21 全日本プロ代表決定戦（女流） 決勝 | 1位 波奈美里・2位 美波智子・3位 菅原千瑛・4位 稲毛千佳子 | 照合済み |  |
| 2013-08-17 著名人代表決定戦 雷神編 決勝 | 1位 金子昇・2位 先崎学・3位 片山まさゆき・4位 上田浩二郎 | 照合済み |  |
| 2013-05-18 著名人代表決定戦 風神編 決勝 | 1位 じゃい・2位 木谷高明・3位 加藤鷹・4位 植田佳奈 | 照合済み |  |

- 8件とも照合済みで、備考は空
- #630 に要約をコメントした: https://github.com/retroeater/mj/issues/630#issuecomment-6846903436

### 3. docs

- RSC-18 の手順3の1文を `docs/notes/research-candidates.md` の「B の旧公式の結果一覧」の節に書いた（旧公式のためだけの「別名」〈訂正〉の行は、本実行で候補タブに書けたら消してよい。消した後に回しても、候補タブに旧公式の行がある卓は飛ばす）
- 決定（名前の警告の読み方を含む）を `docs/decisions/automation.md` に記録した

### マージ

- ドキュメントのみ。cloudflare が先に進んでいたので取り込んだ（衝突なし、ほかのセッションのログ・decisions）。`git push origin work/1113-rsc:cloudflare` で入れた。push 直前に再 fetch して `merge-base --is-ancestor` を確かめ、範囲のコミットの件名・本文に閉じるキーワードの直後の `#番号` が無いことも確かめた。この行を含むコミットが範囲の終わり

## 報告

- 状態: 判断待ち / 続き: CHAT-1113-RSC-23
- ブランチ: work/1113-rsc（cloudflare にマージ済み。ドキュメントのみ）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1113-RSC-22.md
- 比較URL: なし（マージ済み）
- 確認用URL: なし
- マージ: 済み（ドキュメントのみ。範囲は「マージ」の行）
- issue: #630（8件を書いた要約をコメント。Open のまま）
- 判断が必要なこと（平野さんの手作業）:
  1. 「別名」から2行（トレンディエンジェルたかし・林下清志（ビッグダディ））を消す
  2. 候補タブで、RSC-16 が古いキーで書いた3行を「却下」にする: `2014-08-09 著名人代表決定戦雷神編 決勝`・`2013-08-17 著名人代表決定戦雷神編 決勝`・`2013-05-18 著名人代表決定戦風神編 決勝`
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 06a55663）: https://github.com/retroeater/mj-logs/tree/main/guide/06a55663

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/06a55663/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
