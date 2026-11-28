# CHAT-1128-LVP-52

- 着手日時: 2026-11-28
- 対象issue: #403・#688・#690・#354・#362
- ブランチ: work/1128-lvp-close
- 着手時HEAD: 7145b5a8f

## 指示

【Claude作成】Claude Code 向け指示：#403・#688 を閉じ、#690・#354 を /live の告知の後に回す決定を記録する
Chat-Ref: CHAT-1128-LVP-52
マージ: ドキュメントのみ（docs/decisions とログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は状態と公開ログの URL の2行）。平野さんは、この指示のための作業ブランチ work/1128-lvp-close の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1128-lvp-close を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1128-lvp-close origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
/live の告知（#362）の前に片付ける issue を閉じ、残りの順番を記録する。同じチャットから並行して CHAT-1128-LVP-53（告知前のレビュー、work/1128-lvp-rev）を出しているので、作業ブランチを分けている。

### 決定（2026-11-28、平野さん）
- #403（対局日の残り）と #688（日本シリーズ）を閉じる
- #690（title/ と live/ の期ページの相互リンク）と #354（他団体の選手の所属団体の表示）は、/live の告知の後に回す（#690 の「着手は 2026-12-01 以降」を置き換える）
- 告知の前に、live/ の着手以降の他ページの変更・改善との平仄と、機能を援用できるかを包括的にレビューする（CHAT-1128-LVP-53）

### 前提（チャット側。平野さんの決定ではない）
- 2026-11-28 11:4x JST に REST で見た状態: #362・#403・#688・#690・#354 はいずれも open
- #403: 範囲は 2026-11-23 の決定で「『タイトル』シートの決勝日から【3】の対局日に写せる行だけ」に狭めてある。2026-11-25 のコメントの47行のうち、世界選手権の4行は 2026-11-28 の取り込み（run 46097887188）の後、【2】の対局日が 2014-07-20・2017-10-08 になった（チャット側が gviz で読んだ）。残る43行（準決勝 40・予選Aブロック決勝戦 3）は決定の範囲の外で、残る作業ではないと見ている（要確認）
- #688: 決めたことは docs/decisions/live.md（CHAT-1127-LVP-47・48 まで）に入ったと見ている。2026-11-25 のコメントの個別の項目（2023 第3節の【3】行3989、2021・女流 2022 の保留、2022 第5節の15秒の動画）が今どうなっているかはチャット側は確かめていない（要確認）
- 閉じるときに残る作業は別の issue に起票する（平野さんの決定。docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」）

## 手順
1. #403・#688 の本文・コメントと docs/decisions/live.md を読み、決めたことがコード・シートの規則・docs に入っているか、閉じた後に残る作業があるかを表にしてログの `## 経過` に書く（#688 の 2026-11-25 のコメントの項目は1行ずつ、今の【3】か生成物で確かめる。シートを読む実行は、ほかの読み書きの実行が無いことを見てから）。残る作業があれば、新しい issue に起票する（本文に元の issue と経緯を書く）
2. #403・#688 に、閉じる理由（入ったこと・範囲の外としたこと・起票した issue）のコメントを書いて閉じる。#690・#354 に「/live の告知の後に回す（2026-11-28 の平野さんの決定）」とコメントする（#690 の本文の「着手は 2026-12-01 以降」も直す）。#362 には、この2つを閉じたことと、残りが CHAT-1128-LVP-53 のレビューと告知であることをコメントする
3. docs/decisions/live.md に、上の決定を足す（追記先の今の内容を読み、同じ趣旨の記述があれば置き換える）

## 止まる条件
- 他セッションの着手中コメントが #403・#688・#690・#354 にあり、まだ終わっていない
- 手順1で、決めたのにまだ入っていないこと（コード・規則の直しが要るもの）が見つかった。起票で済むもの（新しい判断・作業）は止まらずに起票する
- docs/decisions/live.md の追記先が上の決定と矛盾していて、どちらが正か判断が要る
- 未マージの work/ ブランチが docs/decisions/live.md の同じ節を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1128-LVP-52.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット無し・0章（指示欄の末尾が指示文の最後の行と一致）。雛形の行の欠け無し。#403・#688・#690・#354・#362 はいずれも open。最後の着手中コメントはこのセッションのもの（LVP-37・47 など。どれも終わっている）で、他セッションの着手中は無い
- 着手中コメント: #403・#688
- 未マージの work/ ブランチで docs/decisions/live.md を変えるものは無い
- 読み込み: 実行中の Actions の run が無いことを確かめてから、一時コミットの check（run 46100883367、戻し済み）で【3】（【2】と合わせた値）の該当行を読んだ

### 1. #403・#688 で決めたことが入っているか
| issue | 決めたこと・項目 | 入っている所・今の状態 | 残る作業 |
|---|---|---|---|
| #403 | 範囲を「『タイトル』シートの決勝日から【3】の対局日に写せる行だけ」に狭める（2026-11-23） | 決勝が1日の8大会の決勝の行に決勝日を写す【2】の規則（`live_candidate.fill_final_dates`、CHAT-1123-LVP-36） | なし |
| #403 | 世界選手権 第1・2回の決勝の4行（「タイトル」の日付が XX だった） | 【3】行3091・3092 の対局日 2014-07-20、行3093・3094 は 2017-10-08（「タイトル」を直した後の取り込みで【2】に写った） | なし |
| #403 | 準決勝の40行（王位戦・麻雀マスターズ） | 決勝と別の日なので写さない（2026-11-25 の決定、規則のまま） | なし（範囲の外） |
| #403 | 紅龍戦の予選Aブロック決勝戦の3行 | 決勝の段階でないので決定の範囲の外 | なし（範囲の外） |
| #688 | 全段階の掲載・記号・並び・「プレイヤー解説」（2026-11-23） | `lib/live.py`・`live_extract.py`・`live_layer3.py`・`live_candidate.py`（CHAT-1123-LVP-36）、docs/notes/live-page-design.md「2-1」 | なし |
| #688 | 2022 プレーオフ第1節（「&」の題名） | `live_extract.analyze`（CHAT-1125-LVP-37）。`live/nihon-series/2022/po1.html` と s7 に YF-Tnz1ZE_0 | なし |
| #688 | 2023 第3節（【3】行3989、gMDOwdKFSLw） | 【3】でステージ・まとめ単位「第3節」・並び 4。`live/nihon-series/2023/s3.html` にあり、s2 に無い | なし |
| #688 | 2021 の5本・女流 2022 の9本（公開版だけの冒頭版） | 【3】の掲載はすべて N（行3785・3838・3978・3917・4018、女流 2022 の9行）。2021・女流 2022 の節のページは無い | なし |
| #688 | 2022 第5節の15秒の動画（【3】行3540、2UQGDePTDl0） | 掲載 N。第5節のページに出ない | なし |
| #688 | 女流 2022 の回戦の動画 | 予選（q）・プレーオフ（po）・決勝（f）のページに入る。節に分けない（2026-11-27 の決定） | なし |

- 残る作業が無いので、新しい issue は起票していない

### 2. issue
- #403・#688: 閉じる理由（入ったこと・範囲の外としたこと・起票なし）のコメントを書いて閉じた（completed。「状況:」ラベルは付いていなかった）
- #690: 「/live の告知の後に回す（2026-11-28 の平野さんの決定）」とコメントし、題（「着手は 2026-12-01 以降」→「着手は /live の告知の後」）と本文の「着手の時機」を直した
- #354: 同じコメントを書いた（「状況: 保留」のラベルは残した）
- #362: #403・#688 を閉じたことと、残りが CHAT-1128-LVP-53 のレビューと告知であることをコメントした

### 3. docs/decisions/live.md
- 2026-11-28 の節を足した。#690 の着手の時機は CHAT-1123-LVP-35 の節（決定5）に「着手は 2026-12-01 以降」とあり、新しい節で置き換えると書いた（過去の節は記録なので書き換えていない）。矛盾として判断を要するものは無い

## 報告

- 状態: 完了
- ブランチ: work/1128-lvp-close
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1128-LVP-52.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1128-lvp-close
- 確認用URL: なし（docs のみ）
- マージ: 済（31ed83e46）
- issue: #403・#688（クローズ）、#690・#354・#362（コメント。#690 は題と本文も直した）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f43e7bd）: https://github.com/retroeater/mj-logs/tree/main/guide/4f43e7bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f43e7bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f18a318e.md
