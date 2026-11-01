# CHAT-1101-MCK-26

- 着手日時: 2026-11-01
- 対象issue: #304・#124
- ブランチ: work/1101-mck-chrome
- 着手時HEAD: ac750f5e8

## 指示

【Claude作成】Claude Code 向け指示：チャット側が平野さんの Chrome から本番を一括で取得しない規則を chrome-reading.md に足し、#304 に 10/31 のチャレンジの出どころを書き足す
Chat-Ref: CHAT-1101-MCK-26
マージ: 承認済み（チャットで、2026-11-01。この指示単独で聞いた）。条件: cloudflare との差分が、docs/notes/chrome-reading.md「使い方」への1項の追記と、ログ・docs/decisions/operations.md の追記だけ
貼る時機: CHAT-1101-MCK-25 のマージの後（#304 の「2026-11 実施」のコメントに書き足すため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-chrome の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-mck-chrome を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-chrome origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。この指示は、表が無いため着手せずに止めた版（同じ Chat-Ref、コミット0件）に表を足して出し直したもの。同じ Chat-Ref のまま使う。

## 目的

#304 の 2026-11 の回で、2026-10-31 11:00（日本時間）ごろに平野さんの回線が /pro/ で Rate limiting rule #124 に約1,180回当たっていたのが見つかった。PRO のチャットに聞いたところ、PRO のチャット（Claude）が平野さんの Chrome（Claude in Chrome）のタブから個別ページ1,098件を `fetch` で一括取得したものだった。同じことを他の系列で起こさないよう、チャット側の規則に足す。

### 決定（2026-11-01、平野さん）

- 規則は今すぐ入れる。場所は docs/notes/chrome-reading.md（chat-side-operations.md は上限があるため使わない）。マージしてよい
- #304 には「PRO のチャットによる一括の取得。実害なし」と書く。#124 の閾値（30回/分）は変えない

### 前提（PRO のチャットの返信〈平野さん経由の伝言、2026-11-01〉。平野さんの決定ではない）

- PRO のチャットは、本番の pro/ の点検のサンプルを選ぶため、平野さんの Chrome のタブで `/pro/` を開き、ページの JavaScript から個別ページ1,098件を `fetch`（25件ずつ同時・待ちなし）で取りに行った。50件目あたりから 429 になり、気づいて止めた。数分後に1件開き直すと普通に開けた
- 時刻は CHAT-1031-PRO-04（pro/ を未公開のまま本番にマージ）と CHAT-1031-PRO-05 の間。PRO-05 以降の指示文には「本番への大量の取得はしない」と書いてある
- 足す文（チャット側の案）: **本番（ryoei.pro）のページを Chrome のタブから一括で取得しない（`fetch` のループ・タブを次々に開くこと。確かめは数件まで）**（#124 のレート制限〈同じ IP から HTML を1分30回〉に当たり、平野さんの回線にチャレンジ・429 が返るため。2026-10-31 に PRO のチャットが当たった）。事例は書かず、理由の一句だけ
- 同じ趣旨の項がすでにあれば、足さずに既存の項を直す

### 知見 → 選んだ層 → 理由（CLAUDE.md「Chat-Ref」のガイド文に規則を足す指示の表、#492）

| 知見 | 計算的センサー（テスト・hook・ワークフロー） | 推論的センサー（受け手の確認手順） | 選んだ層 | 理由 |
|---|---|---|---|---|
| チャット側が Chrome のタブから本番を一括で取得すると、#124 のレート制限（同じ IP から HTML を1分30回）に当たり、平野さんの回線にチャレンジ・429 が返る（2026-10-31、PRO のチャット） | 置けない。チャット側の Chrome の操作はリポジトリの外で起き、テスト・hook・ワークフローからは見えない | 当たらない。Claude Code の着手前の確認はチャット側の Chrome の操作を通らない（Code は Chrome を使わない） | ガイド文（docs/notes/chrome-reading.md「使い方」） | チャット側が Chrome を使う前に読む文書（chat-side-operations.md の「場面ごとに読む文書」の表が指す）で、上限が無い。chat-side-operations.md は上限があるため使わない（平野さんの決定） |

## 手順

1. docs/notes/chrome-reading.md の「使い方」の今の内容を読み、同じ趣旨の項が無いことを確かめて、前提の文を足す。追記後の節の全文と `git diff` をログに貼る
2. #304 の「2026-11 実施」のコメント（CHAT-1101-MCK-25 が書いたもの）の、10/31 の KDDI の回線の行を直す（コメントの編集。消さない）: 「PRO のチャットが平野さんの Chrome から pro/ の個別ページを一括取得したもの（CHAT-1031-PRO-04〜05 の間、1,098件、25件同時）。実害なし。#124 の閾値は変えない（平野さん、2026-11-01）」。IP は書かない。編集前と後をログに引用する
3. 決定を docs/decisions/operations.md に記録し、`git diff origin/cloudflare...HEAD --stat` で冒頭の「マージ:」の条件を確かめて cloudflare へマージする。ガイド文書の写し（mj-logs の guide/）の更新が動いたかをログに書く

## 止まる条件

- 冒頭の「マージ:」の条件を満たさない
- #304 に「2026-11 実施」のコメントが無い（CHAT-1101-MCK-25 が済んでいない。手順1・3だけ行い、手順2はしないでその旨を書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-26.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-26 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: 「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: `git log --all --grep="MCK-26"` は0件（表が無いため着手せずに止めた前の版は、ブランチ・ログ・コミットを作っていない）
- 「知見 → 選んだ層 → 理由」の表（#492）: 今回の指示文にある
- `XXX` の重複は対象外。0章ゲートは実装を含まないため対象外。雛形の行の欠けなし。指示文の issue の state: #304 は Open（#124 は Closed だが経緯の参照で、着手の対象ではない）
- CLAUDE.md: `git log HEAD..origin/cloudflare -- CLAUDE.md` は0件
- 作業ブランチ: work/1101-mck-chrome はローカル・リモートとも無く、`git checkout -b work/1101-mck-chrome origin/cloudflare` で作った

### 1. docs/notes/chrome-reading.md「使い方」

- 追記前の「使い方」の項は6つ（Claude プロファイル・1回の操作とタイムアウト・応答しない事象・タブを閉じる・タブのグループ）。本番への取得の量を扱う項は無かったので、末尾に1項足した（442501794）

追記後の節の全文:

```
## 使い方

- **Chrome は平野さんが用意した「Claude」プロファイル（接続名「Claude in Chrome」）で使う。** 平野さん自身のプロファイルは使わない
- **1つの確認につき、ブラウザの操作は1回（タイムアウト4分）まで。** タイムアウトしたら同じ操作をやり直さず、
  `docs/notes/chat-side-operations.md`「読み方」に切り替える（待つより速い）。
  **後で別の確認をするときは、また1回試してよい。** 一度のタイムアウトを理由に、その会話でブラウザを使うのをやめない
  この規則は GitHub の mj を読むときのもの。**Cloudflare ダッシュボードの API の読み（`docs/notes/cloudflare.md`「Chrome からダッシュボードの API を読む」）には適用しない**（2026-10-12 に十数回の操作で問題なかった）
- 応答しなくなる事象は一時的なもので、恒久的な故障として扱わない（navigate は通るが get_page_text / screenshot / find だけが
  4分でタイムアウトする形がある。Claude Desktop の再起動では直らないことがある）
- **タブを閉じる操作はタイムアウトしやすい。** 閉じられずに残ったタブは、平野さんが閉じてよい
- **タブのグループは、会話のターンをまたぐと無効になることがある。** URL を指定して開き直すほうが確実
- **本番（ryoei.pro）のページを Chrome のタブから一括で取得しない**（`fetch` のループ・タブを次々に開くこと。確かめは数件まで）。#124 のレート制限（同じ IP から HTML を1分30回）に当たり、平野さんの回線にチャレンジ・429 が返るため
```

`git diff`:

```diff
@@ -15,6 +15,7 @@
   4分でタイムアウトする形がある。Claude Desktop の再起動では直らないことがある）
 - **タブを閉じる操作はタイムアウトしやすい。** 閉じられずに残ったタブは、平野さんが閉じてよい
 - **タブのグループは、会話のターンをまたぐと無効になることがある。** URL を指定して開き直すほうが確実
+- **本番（ryoei.pro）のページを Chrome のタブから一括で取得しない**（`fetch` のループ・タブを次々に開くこと。確かめは数件まで）。#124 のレート制限（同じ IP から HTML を1分30回）に当たり、平野さんの回線にチャレンジ・429 が返るため
```

- 前提の文の「2026-10-31 に PRO のチャットが当たった」は事例なので入れていない（「事例は書かず、理由の一句だけ」に従った）

### 2. #304 の「2026-11 実施」のコメントの編集

- 対象: https://github.com/retroeater/mj/issues/304#issuecomment-6551825397 （MCK-25 が書いたもの。編集前の updated_at は 2026-11-01T01:34:20Z で、MCK-25 の後に誰も編集していなかった）
- 編集前:

> - 10-31 11:00 JST ごろ、KDDI の IPv6 アドレス1つ（Chrome 156）が `/pro/` に約1.18k回 Managed Challenge を受けた（#124 のルール）。平野さんご本人かは確認中

- 編集後:

> - 10-31 11:00 JST ごろ、KDDI の IPv6 アドレス1つ（Chrome 156）が `/pro/` に約1.18k回 Managed Challenge を受けた（#124 のルール）。PRO のチャットが平野さんの Chrome から pro/ の個別ページを一括取得したもの（CHAT-1031-PRO-04〜05 の間、1,098件、25件同時）。実害なし。#124 の閾値は変えない（平野さん、2026-11-01）

- 末尾に `Chat-Ref: CHAT-1101-MCK-26` を足した（MCK-25 の行の次）。ほかの行は変えていない（編集前後の本文を diff で確かめた。IP は書いていない）
- 編集は `gh api -X PATCH repos/retroeater/mj/issues/comments/6551825397 -F body=@<ファイル>`（本文はファイルから渡した）

### 3. 決定の記録とマージ

- docs/decisions/operations.md に「2026-11-01（CHAT-1101-MCK-26）」を足した
- マージの条件を `git diff origin/cloudflare...HEAD --stat` で確かめてからマージする

## 報告

- 状態: 完了
- ブランチ: work/1101-mck-chrome
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1101-MCK-26.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-mck-chrome
- 確認用URL: なし
- マージ: 未（このあと cloudflare へ入れる）
- issue: #304（「2026-11 実施」のコメントの1行を編集）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3c65b911）: https://github.com/retroeater/mj-logs/tree/main/guide/3c65b911

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c65b911/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c65b911/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c65b911/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c65b911/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c65b911/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3c65b911/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/27bafd9b.md
