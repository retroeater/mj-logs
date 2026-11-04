# CHAT-1104-LGN-32

- 着手日時: 2026-11-04
- 対象issue: #601（コメントのみ）
- ブランチ: work/1104-lgn-gcp（LGN-29 の未マージのブランチを続けて使う。origin/cloudflare を merge で取り込んだ）
- 着手時HEAD: 12f23348e

## 指示

【Claude作成】Claude Code 向け指示：docs/notes/google-cloud.md を公開してよい設定だけで書く（LGN-29 の続き）
Chat-Ref: CHAT-1104-LGN-32
マージ: ドキュメントのみ（docs/notes・docs/handover.md・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1104-LGN-29 が判断待ちで止まった後。同じセッションでも新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-gcp への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lgn-gcp を続けて使う（LGN-29 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/handover.md` の関連文書の表に、ほかのセッションの行と同じ位置に足された衝突、`docs/decisions/*.md`・`docs/logs/` の末尾への追記どうしの衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1104-LGN-29 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-LGN-32` を足す。

## 目的
LGN-29 は、#601（認証情報の台帳。置き場所は未決）と主題が重なるため止まった。docs/notes は public の mj-logs に写る。サービスアカウントや API キーの名前・権限の範囲を載せると、攻撃の手がかりになる。そこで google-cloud.md には公開してよい設定だけを書き、Google Cloud の設定値を1か所にまとめる（LGN-29 の目的）。

### 決定（2026-11-04、平野さん）
- 案 (a): docs/notes/google-cloud.md は、公開してよい設定だけにする。載せるのは、有効にした API・割り当ての値・請求の形・無料の枠と単価・設定を変えたときの手順。サービスアカウント・API キー（名前・制限・共有先・Secret 名）の行は #601 の台帳に回す
- LGN-29 の決定（設定値を1つの文書にまとめる。GetPlaceRequest の1日の割り当てを 125,000 から 3,000 に下げた）はそのまま
- 11月の閉業の判定はやり直さず、2026-12-01 の予約に任せる（LGN-29 の判断が必要なこと (2) の答え。直しは CHAT-1104-LGN-31 が行う）

### 前提（チャット側。平野さんの決定ではない）
- 割り当ての値（平野さんが Google Cloud の画面で確かめた、2026-11-04）: GetPlaceRequest は1日 3,000・1分 600。2026-11-04 に、閉業の判定（LGN-28）の2回の本番の実行で、1日の 3,000 に 100% 達した（1分は 43%）。Text Search・Nearby Search の1日の値は「要確認（平野さんが画面で確かめる）」と書く。Nearby Search は 2026-10-29 の決定で使わなくなった
- 書かないもの: サービスアカウントの名前・メールアドレス、API キーの名前・制限、Secret 名、プロジェクトの ID・番号、スプレッドシートの ID、共有先。これらに触れる必要があるところは「#601 の台帳」と書く
- 各所の文書にある、公開してよい設定（割り当て・無料の枠・単価など）の記述は、google-cloud.md への参照に置き換える。サービスアカウント・鍵の記述は、この指示では動かさない（#601 で扱う）。動かさなかった記述の場所はログの表に残す
- #601 に、google-cloud.md を公開してよい設定だけで作ったこと、サービスアカウント・鍵の行は台帳に回したこと、ログの洗い出しの表（ファイル名と節の名前だけ）をコメントする
- 知見 → 選んだ層 → 理由（handover.md に行を足すため。CLAUDE.md「Chat-Ref」節の求め）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| Google Cloud の設定が各所の文書に分かれていて、割り当てなどの今の値を探しにくい（2026-11-04、GetPlace の割り当ての確認で、平野さんが画面を開くまで値が分からなかった） | docs/notes/google-cloud.md（新しい文書。公開してよい設定だけ）と handover.md「6. 関連文書」の1行 | 設定の画面はセッションから見られず、テスト・hook では確かめられないので、cloudflare.md と同じく手で直す文書が合う。認証情報は #601 の台帳に分ける（public に写るため）。CLAUDE.md・chat-side-operations.md には足さない（上限があり、場面限定の内容のため） |

## 手順
1. LGN-29 の洗い出しの表から、公開してよい設定と、#601 に回すものを分けてログに書く
2. docs/notes/google-cloud.md を書く。handover.md の表に行を足し、各所の重なる記述を参照に置き換え、docs/decisions に決定を足す。#601 にコメントする
3. 書いた文書に、メールアドレス・ID・鍵の値・Secret 名・サービスアカウント名が無いことを `git grep` で確かめ、結果をログに書く。完了報告のうえ cloudflare へ入れる

## 止まる条件
- ガイド文（CLAUDE.md・handover.md・chat-side-operations.md）が、容量の上限の警告域に入る（足す前に止まって報告する）
- 書くのに、メールアドレス・ID・鍵の値・サービスアカウント名・Secret 名が要る（書かずに止まる）
- 冒頭に書いた以外の取り込みの衝突がある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。要確認の値（Text Search・Nearby Search の1日の割り当て）を、平野さんが確かめる項目として「判断が必要なこと」に書く（確かめる画面: https://console.cloud.google.com/apis/api/places.googleapis.com/quotas ）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-32.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-32 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-lgn-gcp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgn-gcp/docs/logs/CHAT-1104-LGN-32.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-gcp
- 確認用URL: なし
- マージ: 未
- issue: #601（コメントのみ）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 339f291e）: https://github.com/retroeater/mj-logs/tree/main/guide/339f291e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/339f291e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/339f291e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/339f291e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/339f291e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/339f291e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/339f291e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
