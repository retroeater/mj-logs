# CHAT-1104-LVP-17

- 着手日時: 2026-11-04
- 対象issue: #362
- ブランチ: work/1104-lvp-auto
- 着手時HEAD: 23efd467c

## 指示

【Claude作成】Claude Code 向け指示：公開している大会の新しい動画の掲載を自動で決める規則を、今までの手入力の判断に当てて一致率を測る（調べだけ。シート・コード・生成物は変えない） Chat-Ref: CHAT-1104-LVP-17 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（調べだけの指示。シート・コード・生成物・issue の本文は変えない） 貼る時機: CHAT-1104-LVP-16 の完了の後（どちらも【2】【3】を読むので、並走すると Sheets API の1分あたりの上限〈#662〉に当たりやすいため。同じセッションの続きでも新しいセッションでもよい。毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lvp-auto の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lvp-auto を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lvp-auto origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-LVP-16 が完了（ログの `## 報告` の状態）していなければ止まる。

目的
公開している大会に新しい動画が出たとき、【3】の掲載を平野さんが毎回入れなくて済むように、掲載を自動で決める仕組みを作りたい。その前に、規則の案を今までの手入力の判断に当てて、どれだけ一致するか・どこで外れるか・どれだけ保留になるかを数で確かめる。
決定（2026-11-04、平野さん）

* 公開している大会に新しい対局（動画）が現れたら、掲載を自動で決める（冒頭のみ無料の公開版は N、完全版は Y）。自動で決めるのが難しいものだけ保留にして知らせる運用にする
* 保留は、issue にコメントしたうえで、保留が残っている限り、解消するまで毎回メールにも載せる（SNS の画像が無い場合などと同じ形）
* 実装の前に、規則を今までの手入力の判断に当てて一致率を測る（この指示）

前提（チャット側。平野さんの決定ではない）

* 規則の案（実物に合わせて変えてよい。変えたら理由を書く）:
   * 置き場所は【2】（自動変換）の層。【3】（手動補正）は上書きしたいときだけ使い、【3】が空欄でも【2】の自動の値で掲載が決まる（3層の役割どおり）
   * 範囲は、公開している大会（「タイトル戦」シートに行がある大会）で、ステージの記号があるもの（今の掲載範囲。docs/notes/live-page-design.md「2-1」）
   * 同じ対局（大会・期・ステージ・卓・日）にメンバー限定版（層1の「限定」Y）があれば、それを Y、公開版を N（冒頭のみ）。メンバー限定版が無い対局は公開版を Y。題名の【メンバー限定】ではなく層1の「限定」で見る（第24期プロクイーン決定戦の最終日 `WnnCehyq0yU` は「限定」Y だが題名に【メンバー限定】が無い）
   * 保留: 題名から大会・期・ステージが読めない、ステージの記号が無い、メンバー限定版と公開版の組が揃わない（片方だけ先に出た等）、新しい大会、配信予定のまま、など
* 知らせの形の案: 毎朝の取り込み（`update-live-channel.yml`）で、保留の行を常設の issue にコメントし、保留が残る限りワークフローを注釈つきで失敗にして失敗のメールで知らせる（docs/notes/github-actions.md の「飛ばしたページ」の扱いと同じ形の案）。平野さんの言う「SNS の画像が無い場合などと同じ形」の今の仕組みが何かは、チャット側では確かめていない（要確認）。同じ形の仕組みが既にあればそれに合わせる
* 通知先の issue は、既存の常設の issue（#475 など）に足すか、新しく作るかを案として出す（この指示では作らない）
* 【3】の掲載がある行（CHAT-1104-LVP-16 の時点で Y 約3,000・N 約600。要確認）を「正解」として使う

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。「SNS の画像が無い場合など」に、issue のコメントと、解消するまで続くメールで知らせている今の仕組みを、ワークフロー・スクリプト・docs から探し、どの issue・どのメール（失敗の通知か、別の送信か）かを書く。見つからなければ、近い仕組みと、そう判断した理由を書く
2. 【2】【3】と層1を読み、上の規則の案を、【3】に掲載がある全行に当てる。一致・不一致・保留の件数を大会ごとに表にし、不一致と保留は全件（動画ID・題名・【3】の値・規則の値・理由）を `## 経過` に書く。不一致を減らす規則の直しの案があれば、直した後の件数も書く。今【3】が空欄の行（未確認）に規則を当てたときの Y・N・保留の件数も書く
3. 実装の案を書く: 【2】に足す列（または既存の列の使い方）、判定を置くコード（`lib/live_extract.py` など）、保留の知らせの仕組み（手順1の形に合わせる）、【3】の既存の値との関係、テスト、外すトリガの要る暫定があればそのトリガ。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 【2】【3】の読み込みが失敗する、または読み直すたびに行数が変わる（件数を書いて止まる）
* 不一致が【3】に掲載がある行の 10% を超える（大会ごとの表と不一致の上位の理由を書いて止まる。全件は書かなくてよい）
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、一致率（大会ごと）、不一致と保留の主な理由、規則の直しの案、通知の形の案（issue・メール）、実装の案を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LVP-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LVP-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1104-LVP-17` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1104-LVP-16 のログの状態は「完了」
- 作業ブランチ: work/1104-lvp-auto はローカルにもリモートにも無いので、`git checkout -b work/1104-lvp-auto origin/cloudflare` で作った
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-04 23:56 JST

## 報告

- 状態: 作業中
- ブランチ: work/1104-lvp-auto
- ログ: https://github.com/retroeater/mj/blob/work/1104-lvp-auto/docs/logs/CHAT-1104-LVP-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lvp-auto
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 23efd467）: https://github.com/retroeater/mj-logs/tree/main/guide/23efd467

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/23efd467/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23efd467.md
