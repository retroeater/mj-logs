# CHAT-1017-MCK-15

- 着手日時: 2026-10-17
- 対象issue: #367, #365
- ブランチ: work/1017-mck-367-places
- 着手時HEAD: 0962f9e0c

## 指示

【Claude作成】Claude Code 向け指示：（MCK-14 の続き）#367 の比較を Places API 込みで Sonnet だけもう一度動かし、駅・店名・店URL を確かめる。あわせて #365 の WRC 第18・19期の表示を確かめて記録する Chat-Ref: CHAT-1017-MCK-15 マージ: コードを変えたときだけ承認済み（チャットで）。条件: テストが通る・入力の既定が dry-run のまま・「ログ」タブと【1】元データには書かない・`scripts/generate_resource_logs.py` は変えない・下の「止まる条件」に当たらない。コードを変えないときはドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-mck-367-places の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-mck-367-places を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-mck-367-places origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1015-MCK-14 と CHAT-1015-MCK-13 のログの `## 報告` を読み、どちらも状態が「判断待ち」でなければ止まる。

目的
MCK-14 の比較は Places API の鍵が無く、駅は本文から読んだ値、店URL は空だった。平野さんが鍵を登録したので、比較を Places 込みで、全件に使う Sonnet だけでもう一度動かす。駅・店名・店URL が使えるかと、Places の料金の段を確かめる。全件の変換（`mode=all`）はこの結果を見てから別の指示で行う。あわせて、#365 の WRC 第18・19期が表示されたことを確かめて記録する。
決定（2026-10-15〜17、平野さん）

* 全件の変換に使うモデルは `claude-sonnet-5-5`
* 全件の変換は、Places の鍵を用意し、Places 込みの比較で駅・店名を確かめてから行う
* `GOOGLE_PLACES_API_KEY` は登録済み（2026-10-16〜17）。シートのサービスアカウントと同じ GCP プロジェクトで作り、Places API (New) だけに制限した。Google Maps Platform は従量課金制（月額のプランは使わない）
* JWRC タブの第18・19期の「表示」は、平野さんが `Y` にした（K 列 2〜171行、2026-10-16）

前提（チャット側。平野さんの決定ではない）

* Places の料金（Google の料金表、2026-10-16 にチャット側が確認）: Text Search Pro・Nearby Search Pro はどちらも毎月 5,000回まで無料、超えると 1,000回あたり $32。Text Search Essentials（IDs Only）は無料で上限なし。どの返す項目（FieldMask）でどの段になるかは料金表に書かれていなかった。Places の文書の SKU の説明で、今のスクリプトの FieldMask（Text Search・Nearby Search）がどの段になるかを確かめる（要確認）。Enterprise の段になる項目を返しているなら、Pro の段で済む項目に減らす（コードの変更。冒頭の「マージ:」の条件のとおり）
* 比較の費用の見込み: X 30件 約 $0.15、Claude（Sonnet だけ）約 $0.10、Places は Text Search・Nearby Search がそれぞれ 30回前後（無料の枠の中）
* Places 込みの一致の見方（チャット側の案）: 店名（Places の名前と「ログ」の店名）、駅（「ログ」の駅のどれかが 800m 以内の駅に入っているか）、店URL（Places で店が見つかった件数。「ログ」の店URL は共有のリンクなので、同じ店かは place_id を比べず、店名・住所で分かる範囲）、要確認の件数と理由ごとの内訳、本文の駅が圏内に無い例外の件数
* #365 の確かめ方（チャット側の案）: MCK-13 と同じ gviz の経路で JWRC タブの第18・19期の「表示」が全行 `Y` か読む。WRC のページ（`wrc_results.js` を使うページ）で、第18・19期が出るかを、ページと同じ読み方（`wrc_results.js` の絞り込み）で確かめる

手順

1. #367・#365 に他セッションの着手中コメントが無いことを確かめ、#367 に着手中のコメントを残す（本文・状態・ラベルは変えない）。今の `scripts/convert_x_posts.py` の Text Search・Nearby Search の FieldMask と、Places の文書の SKU の説明を照らし、どの段になるかをログに書く。Enterprise の段になる項目があれば、Pro の段で済む形に直し、テストを通して cloudflare へマージする
2. cloudflare で比較（`mode=compare`・`models=claude-sonnet-5-5`）の dry-run を1回動かして予定（X の件数・Claude と Places の呼び出し回数・鍵の有無）を確かめ、続けて `dry_run=false` で動かす。前提の「Places 込みの一致の見方」をログの表にし、MCK-14 の Sonnet の結果と並べる。Places の呼び出し回数（Text Search・Nearby Search 別）と、全件（【1】の行数）で見込まれる回数・費用を書く
3. #365 を前提の確かめ方で確かめ、#365 に「第18・19期を表示した（2026-10-16）。第20期は終わってから」とコメントする（本文・状態・ラベルは変えない）。#367 に結果をコメントする。「判断が必要なこと」に、全件の変換に進めるかの判断材料（一致の割合・要確認の見込み件数・費用）を書く。CHAT-1015-MCK-14 と CHAT-1015-MCK-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1017-MCK-15` を足す

止まる条件

* #367 が Closed、または #367・#365 に他セッションの着手中コメントがある
* コードを変えるとき、マージの条件のどれかが満たされない
* dry-run で `GOOGLE_PLACES_API_KEY` が「無」と出る（本番を動かさずに止まる）
* Places API が権限の拒否（API が有効でない・キーの制限など）・認証の失敗・割り当ての超過を返した（それ以上呼ばずに、応答の分類とエラーの説明の要点を書いて止まる）
* 費用の上限（Claude API $3・X 40件・Places の呼び出しの上限）に届いた、または X API・Claude API が認証の失敗・クレジット切れ・レート制限・権限の拒否を返した（それ以上呼ばずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-MCK-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-MCK-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1015-MCK-14・CHAT-1015-MCK-13 の `## 報告` の状態はどちらも「判断待ち」
- Chat-Ref の重複: `CHAT-1017-MCK-15` のコミット 0件（`MCK` はこのセッションの MCK-01 で使ったもの）
- 作業ブランチ: `work/1017-mck-367-places` はローカル・リモートとも無かったため `git checkout -b work/1017-mck-367-places origin/cloudflare`（起点 0962f9e0c）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行ともある（1行に続けて書かれている）

### 1. issue と Places の料金の段

- #367: Open。最後の着手中のコメントはこのセッションのもの（MCK-14）。着手中のコメントを残した: https://github.com/retroeater/mj/issues/367#issuecomment-6227243250
- #365: Open。着手中のコメントはこのセッションのもの（MCK-05・MCK-13）だけ
- `scripts/convert_x_posts.py` の FieldMask と、Places の文書（Text Search (New)・Nearby Search (New) の英語版、2026-10-17 に取得）の SKU の説明:

| 呼び出し | 今の FieldMask | 文書でその項目が入る段 | この呼び出しの段 |
|---|---|---|---|
| Text Search | `places.id` | Text Search Essentials ID Only | |
| | `places.displayName`・`places.formattedAddress`・`places.location` | Text Search Pro | **Text Search Pro** |
| Nearby Search | `places.displayName`・`places.location` | Nearby Search Pro（Nearby Search に Essentials の段は無く、`places.id` も Pro） | **Nearby Search Pro** |

- Enterprise の段になる項目（`currentOpeningHours`・`nationalPhoneNumber`・`priceLevel`・`rating`・`websiteUri`・`transitStation` など）は返していない。**コードは変えない**（ドキュメントのみの指示として扱う）
- 1回の呼び出しは、FieldMask の中で一番上の段で数えられる。Text Search で店名・住所・位置を返すので Pro（`places.id` だけにすれば無料の Essentials になるが、駅を探す位置〈`location`〉と【県】に使う住所が要るため Pro のまま）

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0962f9e0）: https://github.com/retroeater/mj-logs/tree/main/guide/0962f9e0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0962f9e0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0962f9e0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0962f9e0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0962f9e0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0962f9e0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0962f9e0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0962f9e0.md
