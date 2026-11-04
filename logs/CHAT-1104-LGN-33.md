# CHAT-1104-LGN-33

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-lgn-retro（origin/cloudflare から作った）
- 着手時HEAD: ada6990de

## 指示

【Claude作成】Claude Code 向け指示：LGN 系列（2026-11-04、LGN-25〜32）の振り返りの申送り
Chat-Ref: CHAT-1104-LGN-33
マージ: ドキュメントのみ（docs/notes・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-retro の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-lgn-retro を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgn-retro origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾への追記どうしが衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
LGN 系列の 2026-11-04 の指示（CHAT-1104-LGN-25〜32。LGN-26 は送る前に差し替えたため欠番）について、チャット側で振り返りを行い、Fable 5.1 にレビューさせた。その申送りを残す。ガイド文には1行だけ足し、残りは docs/decisions/operations.md の申送りの節に事実と約束として書く。

### 決定（2026-11-04、平野さん）
- 振り返りと Fable 5.1 のレビューを受けて、申送りを行う（「申送り」の指示）

### 前提（チャット側。平野さんの決定ではない）
- ガイド文に足すのは次の1行だけ。docs/notes/chat-routines.md「外部サービスの設定を頼むとき」の箇条の末尾に足す（文はチャット側の案。既存の書きぶりに合わせて Code が直してよい。直したらログに書く）:
  - 「**外部サービスの設定値（割り当て・上限・単価）は、指示文に値を写さず docs/notes/google-cloud.md・cloudflare.md の節を前提として指す。平野さんが設定を変えたと聞いたら、未送信・実行中の指示文の前提をその場で見直す**」
- 知見 → 選んだ層 → 理由（CLAUDE.md「Chat-Ref」節の求め）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| CHAT-1104-LGN-28 の指示文に GetPlace の1日の割り当て 125,000 と書いた。送る前に平野さんが 3,000 に下げたが、チャット側は LGN-28 の前提を直さず、本番の2回目が1日の割り当てに達した（約 3,000回を使い、11月の判定は見送り） | docs/notes/chat-routines.md「外部サービスの設定を頼むとき」の1行 | 指示文の値と画面の値の食い違いは、テスト・hook・ワークフローでは見られない（画面はセッションから見えない）。Code の着手前の確認でも、画面の今の値は分からない。値を文書（google-cloud.md、LGN-32 で作った）の1か所に寄せ、指示文はそこを指すのが、写した値が古くなるのを防ぐいちばん軽い形。CLAUDE.md・chat-side-operations.md には足さない（上限があり、場面限定のため） |

- docs/decisions/operations.md の末尾（直近の振り返り・申送りの節と同じ形）に、次の節を足す。書くのは規則と事実と約束で、長い経緯は書かない（詳細はこのログと各ログ）:
  - 見出し: LGN 系列（log/ の公開の後始末・閉業の判定・Google Cloud の設定の文書、2026-11-04、CHAT-1104-LGN-25〜32。LGN-26 は送る前に差し替えたため欠番）の振り返りと、Fable 5.1 のレビューの申送り（中身はチャット側の案）
  - 事実:
    - LGN-28 は、送る前に変わった割り当ての値（1日 125,000 → 3,000）を直さずに渡した。本番の1回目は接続の切断で止まり、Code が「接続の切断を1回繰り返す」直しをマージして2回目を動かした（指示のマージの条件は差分の範囲を限っておらず、止まる条件も割り当て・権限の失敗だけだったので、この進め方は指示の範囲内）。2回目は1日の割り当てに達して止まった。指示文に本番の実行の回数を書いていなかったのが穴で、チャット側も報告の「2回目」を平野さんに伝える前に拾えなかった。止めたときに回数を出す直しは LGN-31
    - LGN-27 は、サイト全体の利用規約・プライバシーポリシーのページ /policy/ を作ってプレビューで止めたが、規約の本体（Google Maps Platform Terms of Service 3.2.2(a)(i)）を読み直すと、必須なのは Google の2文とリンクだけだった。LGN-30 で log/ の末尾の「利用条件」に替えた。根拠にしていたのは LGN-04 の時点の Places API の Policies のページの読みで、指示を書く前に確かめ直していなかった
    - /policy/ をやめたため、任意の開示（AI の質問欄の文を Anthropic の Claude API に送り90日記録すること、Cloudflare Web Analytics を使っていること、localStorage・現在地の扱い）は、サイトのどこにも書かれていない。2026-11-04 に平野さんが /policy/ を作らないと決めた（開示を書かないことを個別に決めたのではない）
    - チャット側は、AI であることの表示を「実質必須（Anthropic）」と平野さんに伝えたが、根拠は第三者のまとめで、Anthropic の Usage Policy の原文は確かめていない
    - LGN-29 は、既存の #601（認証情報の台帳）と主題が重なって止まった（止まる条件は設計どおり働いた）。チャット側が、docs/notes（public の mj-logs に写る）にサービスアカウント・API キーの行を書かせようとしていた。LGN-32 で公開してよい設定だけの google-cloud.md にした
    - 前回の約束: CHAT-1102-AIC-15 の (a)（前提に開いた場所を添える）は LGN-30・31 では守れたが、LGN-28 の割り当ての値と、LGN-27 の規約の要件では守れなかった。(b)（言い回しの直しを週次にまとめる）は当たる場面が無い。(c)（承認の例外で止まる条件を満たさずにマージした例）は無い（LGN-28 の2回目のマージは条件の内）。CHAT-1104-PIL の (a) は守れた（スクリーンショットの値に日時と出典を添えた）、(b) は LGN-28 の「割り当て・権限で失敗したら止まる」が働いた。CHAT-1103-PLG と CHAT-1104-TTL の約束は当たる場面が無い
  - 約束（次の振り返りで確かめる）: 新しく立てず、既存の約束の具体例として次を確かめる。(a-3)（CHAT-1102-AIC-15 の (a) の例）規約・ポリシーを根拠にページや表示を作る指示で、書く前に規約の本体の条文を開き、必須の範囲と出典を前提に書いたか (a-4)（同）第三者のまとめを根拠にしたとき、「必須」「実質必須」の判定語を使わず「原文未確認」と書いたか (c-1)（CHAT-1102-AIC-15 の (c) の例）有料の API を本番で呼ぶ指示に、本番の実行の回数と失敗したときの扱い（やり直さずに回数を報告して止まる、など）を書いたか
- 書かないもの: プレビューの URL、サービスアカウント・API キーの名前、ID

## 手順
1. docs/notes/chat-routines.md と docs/decisions/operations.md の今の容量を測ってログに書く（chat-routines.md に上限が無ければ、その旨を書く）
2. 上の1行と節を足す。足した後の容量を測ってログに書く
3. 完了報告のうえ cloudflare へ入れる

## 止まる条件
- 足す先の文書が、容量の上限の警告域に入る（足す前に止まって報告する）
- chat-routines.md「外部サービスの設定を頼むとき」に、同じ趣旨の行がすでにある（足さずに、その行を引用して報告する）
- 冒頭に書いた以外の取り込みの衝突がある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-33.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-33 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1104-LGN-33` のコミットなし。雛形の行の欠けなし。知見 → 選んだ層 → 理由の表あり
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1104-lgn-retro origin/cloudflare`（ada6990de）
- 手順1（足す前の容量）: docs/notes/chat-routines.md 21,330 バイト、docs/decisions/operations.md 201,137 バイト。どちらも上限は無い（`check-assets.yml` が測るのは CLAUDE.md・handover.md・chat-side-operations.md・instruction-template.md だけ）
- 同じ趣旨の行: chat-routines.md「外部サービスの設定を頼むとき」の4つの箇条（権限・共有の洗い出し、API を1件だけ呼ぶ確かめ、設定の変更を頼む前の確かめ、検索を止めうる変更）に、設定値を写さず文書を指す趣旨の行は無い
- 手順2: chat-routines.md の箇条の末尾に1行（文は案のまま。既存の箇条と同じく太字にし、ファイル名をバッククォートで囲み、末尾に事例の括弧「（CHAT-1104-LGN-28 は送る前に下げた割り当ての値を直さずに渡した）」を足した。chat-routines.md はチャット側の文書で、既存の箇条にも Chat-Ref の事例がある）。docs/decisions/operations.md の末尾に「2026-11-04（CHAT-1104-LGN-33）」の節（RSC-11・TTL-10 の節と同じ形。事実・前回の約束・足したガイド文の1行・約束）
- 足した後の容量: chat-routines.md 21,734 バイト（+404）、operations.md 205,432 バイト
- 書かないものの確かめ: 足した行にプレビューの URL（`workers.dev`）・サービスアカウントの名前・Secret 名は無い（`git diff` の追加行で0件）

## 報告

- 状態: 完了
- ブランチ: work/1104-lgn-retro
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-LGN-33.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-retro
- 確認用URL: なし（docs のみ）
- マージ: 済（docs のみ。docs/notes/chat-routines.md・docs/decisions/operations.md・docs/logs）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a4a9ed1b）: https://github.com/retroeater/mj-logs/tree/main/guide/a4a9ed1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
