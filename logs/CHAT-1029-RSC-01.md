# CHAT-1029-RSC-01

- 着手日時: 2026-10-29
- 対象issue: #628・#630・#408（参照: #633）
- ブランチ: work/1029-rsc
- 着手時HEAD: 4aa1e3f

## 指示

【Claude作成】Claude Code 向け指示：Claude API の調べもの（名前かな・過去の順位・対局日・最強戦の転記案）の前提の洗い出しと設計案
Chat-Ref: CHAT-1029-RSC-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。work/1029-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-rsc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Claude API（web search・citations・structured outputs）で、平野さんの手作業の調べもの4件を「出典付きの候補」に置き換える。この指示は調査と設計案だけで、コード・ワークフロー・シートは変えない。実装は次の指示から1件ずつ行う。

### 決定（2026-10-29、平野さん）
- Claude API の活用案のうち、次の4件に着手する: (A) 連盟外の出場者の「名前かな」の調べ（#628）、(B) 最強戦の過去の対局の順位（F 列）の候補（#630）、(C) タイトル戦の対局日（`YYYY-XX-XX`）の候補（#408）、(D) 最強戦の成績記事からの転記案
- 順番は A → B → C → D。共通の土台（web search＋citations で調べ、出典付きの候補を出す仕組み）は A で作る
- API キーは既存の `ANTHROPIC_API_KEY` を使う（専用のワークスペース・キーは作らない）
- 結果は、シートの「候補」タブに書き、issue には要約を書く。本体のタブ（「最強戦」「タイトル」「連盟プロ以外」）に入れるのは今どおり平野さん
- D は毎朝動かし、新しい成績記事があるときだけ API を呼ぶ（読んだ記事は記録して二度呼ばない）

### 前提（チャット側。平野さんの決定ではない）
- 最強戦の公式サイトを資料に使うことは主催者の許可がある（docs/decisions/saikyo.md の #469 の行）
- abema.tv は読まない（国外から読めない・規約。#501）。動画のコマも取らない
- Claude が出すのは候補と出典（URL・引用文）だけ。名前は「プロ」「連盟プロ以外」「別名」との完全一致で照らす（道場部ゲスト〈docs/notes/dojo-guest-calendar.md〉と同じ）
- チャット側の見込み（要確認）: #628 の残りは約20名、#630 の F 列が空の卓は約397（CHAT-1024-SKP-08 のログ）、#408 の `YYYY-XX-XX` は 2026-09-19 時点で937行（docs/notes/title-pages.md「日付列の書式」）
- 既存の `ANTHROPIC_API_KEY` は道場部ゲスト（run-daily）と X 投稿の変換（run-weekly）でも使う。ワークスペースの月の上限が低いと、調べものが上限に当たってほかのジョブも止まるおそれがある（要確認）

## 手順
1. 前提を確かめる（issue は着手中のコメントだけ書き、本文・状態・ラベルは変えない）
   - #628・#630・#408・#633 の状態（Open/Closed）と本文の残りを読み、上の見込みの数を今のシート・生成物から数え直す。#628 は残りの名前の一覧、#630 は年度ごとの卓の数、#408 は大会ごとの行数（期の数）を全件の表で書く
   - 同じ論点（Claude API で調べる・候補タブ）の issue をクローズ済みも含めて検索し、あれば報告する
2. 制約を洗い出す
   - `ANTHROPIC_API_KEY` の使われ方（ワークフロー・モデル・1回の費用の見込み）と、docs に書かれたワークスペース・上限・組織（月次クレジットを連携した組織か）。docs に無ければ「未記載」と書き、平野さんが Console で見る画面（直接開ける URL）を挙げる
   - 公式ドキュメントで確かめる: web search ツールの料金（月次クレジットで払えるか）、今のモデル（Haiku 5.5・Sonnet 5.5）で web search・web fetch・citations が使えるか、Batch API でサーバー側のツールが使えるか。読めなかったものは「未確認」と書く（推測で埋めない）
   - B・D の資料: 最強戦の公式サイトに成績記事がどの年度から残っているか、記事に順位が載る形（表・本文）。セッションから読めなければ読めないと書く
   - シートへの書き込み: `WRITABLE`（scripts/lib/sheets_write.py）の今の対象、候補タブを置けるブックの候補（読み取り・書き込みのサービスアカウントの共有の有無）
   - この手順では Claude API・Actions を呼ばない（理由: 設計と上限が決まる前に費用がかかるため。キーもセッションには無い）
3. 設計案を書く（ログの「## 経過」）
   - 共通の土台: スクリプトとワークフローの形（手動実行の入力、1回の呼び出しの数・費用の上限で止める仕組み、出典の URL と引用文の照合、名前の完全一致）
   - 候補タブ: 置くブックの案（2〜3案、メリット・デメリットと推奨）、列（例: 対象・候補・確度・出典 URL・引用文・調べた日・状態）
   - A〜D それぞれの入力・出力・1回の費用の見込み（前提のトークン数と単価の出典つき）・止まる条件
   - 次の指示（A の実装）の前に平野さんの判断・手作業が要るものを「判断が必要なこと」に、選択肢・メリット・デメリット・推奨つきで書く

## 止まる条件
- #628・#630・#408 のいずれかが Closed で、残りの作業が無い（その件を外して残りを進め、外したことを報告に書く。全部なら止まる）
- 同じ論点の open issue に他のセッションの着手中のコメントがある
- 見込みの数と数え直した数が大きくずれる（#628 ±5名、#630 ±20卓、#408 ±50行を超える）。止めずに両方を書き、ずれの理由の見立てを添える

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-RSC-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-RSC-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし。識別子 `RSC` の重複なし（全ブランチのコミット・`docs/logs/` の履歴）。0章ゲートは実装を含まない指示のため対象外。#628・#630・#408・#633 はいずれも Open（2026-10-29）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 作業ブランチ: リモート・ローカルとも work/1029-rsc が無かったため `git checkout -b work/1029-rsc origin/cloudflare`

## 報告

- 状態: 作業中
- ブランチ: work/1029-rsc
- ログ: https://github.com/retroeater/mj/blob/work/1029-rsc/docs/logs/CHAT-1029-RSC-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-rsc
- 確認用URL: なし
- マージ: 未
- issue: #628・#630・#408
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4aa1e3f2）: https://github.com/retroeater/mj-logs/tree/main/guide/4aa1e3f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4aa1e3f2.md
