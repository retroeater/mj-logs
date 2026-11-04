# CHAT-1104-LGN-29

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-lgn-gcp（origin/cloudflare から作った）
- 着手時HEAD: ff3a11794

## 指示

【Claude作成】Claude Code 向け指示：Google Cloud の設定値を1つの文書（docs/notes/google-cloud.md）にまとめる
Chat-Ref: CHAT-1104-LGN-29
マージ: ドキュメントのみ（docs/notes・docs/handover.md・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（LGN-25・27・28 と別のセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-gcp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-lgn-gcp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgn-gcp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/handover.md` の関連文書の表に、ほかのセッションの行と同じ位置に足された衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Google Cloud の設定（有効にした API・API キーとその制限・割り当て〈クォータ〉・サービスアカウントと共有先・請求の形）は、今は docs/notes/sheets-reader.md・birthday-calendar.md・yotei-sheet.md・live-channel-write.md・github-actions.md・docs/decisions など各所に分かれている。Cloudflare の設定を docs/notes/cloudflare.md にまとめているのと同じように、1つの文書にまとめる。

### 決定（2026-11-04、平野さん）
- Google Cloud の設定値は、どこか1つの文書にまとめる（Cloudflare のように）
- Places API (New) の GetPlaceRequest（Place Details）の割り当てを、1日 125,000 から 3,000 に下げた（平野さんが Google Cloud の画面で設定した、2026-11-04。呼びすぎたときの費用の歯止め）。1分は 600、1分・ユーザーごとは無制限のまま

### 前提（チャット側。平野さんの決定ではない）
- 置き場所は docs/notes/google-cloud.md の案（cloudflare.md と並べる）。docs/handover.md「6. 関連文書」の表に行を足す
- docs/notes/ は mj-logs（public）のガイドに写る。サービスアカウントのメールアドレス・プロジェクトの ID・スプレッドシートの ID・鍵の値は書かない（CLAUDE.md「作業ログ」節と平野さんの決定〈2026-10-13〉）。サービスアカウントは役割の名前（例 sheets-reader）、鍵は GitHub の Secret の名前で書く
- 載せる項目（案）: プロジェクト（名前だけ）／請求（従量課金、月額のプランは使わない〈docs/decisions/operations.md〉）／有効にした API（Sheets・Drive・Calendar・Search Console・YouTube Data・Places API (New) など。実物はリポジトリのコードとワークフローの Secret の使い方から洗い出す）／API キー（名前・制限・使う Secret。例 `GOOGLE_PLACES_API_KEY` は Places API (New) だけに制限〈2026-10-16〜17 の決定〉）／割り当て（Places の Text Search・Nearby Search・GetPlace の1日・1分の値。Text Search・Nearby Search の1日の値は 2026-10-19 の決定〈変換のあいだ 3,000、済んだら 1,000 に戻す〉の後の今の値が分からないので「要確認（平野さんが画面で確かめる）」と書く。Nearby Search は 2026-10-29 の決定で使わなくなった）／サービスアカウント（役割の名前・何に共有しているか・使う Secret・関係する文書）／無料の枠と単価（Places の区分ごと、Google の料金表を確かめた日）／設定を変えたときの手順（この文書を直す）
- 各所の文書にある設定の記述は、この文書への参照に置き換えるか、残すなら重ならないように書き分ける（同じ内容を2か所に書かない、CLAUDE.md の更新ルール）
- この指示は、ガイド文（handover.md）に規則を足すので、次の表を指示に付ける（CLAUDE.md「Chat-Ref」節の求め）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| Google Cloud の設定が各所の文書に分かれていて、割り当てなどの今の値を探しにくい（2026-11-04、GetPlace の割り当ての確かめで、平野さんが画面を開くまで値が分からなかった） | docs/notes/google-cloud.md（新しい文書）と handover.md「6. 関連文書」の1行 | 設定の画面はセッションから見られず、テスト・hook で確かめられない。cloudflare.md と同じ形の手で直す文書が合う。CLAUDE.md・chat-side-operations.md には足さない（上限があり、場面限定の内容のため） |

## 手順
1. 同じ主題の文書・issue が無いかを確かめる（`docs/notes/` と issue を検索。あれば止まって報告する）。リポジトリから Google Cloud の設定に関わる記述と、コード・ワークフローが使う Secret（`git grep -n "googleapis\|GOOGLE_\|_SA_KEY\|service account\|サービスアカウント"`）を洗い出し、ログの表にする
2. docs/notes/google-cloud.md を書き、handover.md の表に行を足し、各所の重なる記述を参照に置き換える。docs/decisions に今回の決定を足す
3. 要確認の値（Text Search・Nearby Search の1日の割り当ての今の値など）を、平野さんが画面で確かめる項目として報告に書く（Google Cloud の割り当ての画面のリンク: https://console.cloud.google.com/apis/api/places.googleapis.com/quotas ）。完了報告のうえ cloudflare へ入れる

## 止まる条件
- 同じ主題の文書・issue がある
- ガイド文（CLAUDE.md・handover.md・chat-side-operations.md）が容量の上限の警告域に入る（足す前に止まって報告する）
- 書くのに、メールアドレス・ID・鍵の値が要る（書かずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-29.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-29 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-lgn-gcp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgn-gcp/docs/logs/CHAT-1104-LGN-29.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-gcp
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ff3a1179）: https://github.com/retroeater/mj-logs/tree/main/guide/ff3a1179

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
