# CHAT-1116-WBK-05

- 着手日時: 2026-11-16
- 対象issue: #408, #684
- ブランチ: work/1116-wbk
- 着手時HEAD: b2698d07e

## 指示

【Claude作成】Claude Code 向け指示：Wayback Machine の決勝のページ（「ありそう」の33期）と年間の予定のページから、タイトル戦の対局日（#408）の事実を取り出す（PC で実行。読むだけ。WBK-04 の続き） Chat-Ref: CHAT-1116-WBK-05 マージ: ドキュメントのみ（ログ・決定・notes の追記）なので完了報告のうえ cloudflare へ入れてよい〈読むだけの指示。上限や 429・503 で途中で止まったときも、そこまでの記録は cloudflare へ入れてよい〉 貼る時機: いつでも（CHAT-1116-WBK-04 のマージの後。マージは済んでいる） セッション: 必須（平野さんの PC〈R-HP〉の Claude デスクトップアプリの Code タブで、フォルダ C:\Users\hiran\Documents\claude\mj〈WBK-04 で作ったクローン〉を選んでローカルの新しいセッションを始め、そこに貼る。クラウドのセッションには貼らない） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している。CLAUDE.md・docs/notes のうちクラウドのセッションだけに当てはまる部分（割り当てられた claude/… のブランチ、環境の許可するホスト、GitHub MCP）は、この指示では当てはまらない。 作業ブランチ: 平野さんの PC のローカルのセッションで実行する。work/1116-wbk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-wbk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルに WBK-04 の work/1116-wbk が残っているときを含め、手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1116-WBK-04（判断待ち）の続き。WBK-04 の見積もりで「ありそう」とした33期の決勝のページから決勝の日付（と順位が載っているか）を取り出す。あわせて、年間の予定のページ `/title-fight/schedule.html` に決勝の日付が載るかを数ページで確かめる。PC で読み、事実を表にしてログに残すだけ。シート・候補タブ・コード・ページは変えない。候補タブへの書き込みと #408・#684 へのコメントは、この結果を見てから、チャット側が別の指示（クラウドのセッション）で出す。
決定（2026-11-16、平野さん）

* WBK-04 の「#408・#684 を次にどう進めるか」は、チャット側の案 A（「ありそう」の33期の決勝のページと、`/title-fight/schedule.html` 数ページを、PC で1本の指示で読む。取り出した事実を候補タブに書くのは、別のクラウドの指示）
* クラウドの環境「Claude-iPhone」の許可するホストから web.archive.org を外した（平野さんが設定の画面で外した。平野さんの申告で、証跡はまだ無い）

前提（チャット側。平野さんの決定ではない）

* #408・#684 へのコメントは、平野さんが issue を直接更新しない運用なので、WBK-04 の案 (b)（コネクタのあるクラウドのセッションに書かせる）にする。この指示ではコメントを書かず、案もログに書かなくてよい（WBK-04 の案と、この指示の結果から、次の指示で書く）
* #684 の1位だけの19期は、WBK-04 の見積もりのとおり第8期麻雀マスターズの1期のほかは Wayback には無さそう。この指示では #684 の分は読まない（第8期の1〜4位と点は WBK-04 の本文5で確かめ済み）
* 対象は WBK-04 のログ（docs/logs/CHAT-1116-WBK-04.md）の `## 経過`「見積もり（212期、全件）」の期ごとの表で、見込みが「ありそう」の行（33期）。そのうち本文で確かめた4期（鳳凰戦 第15期・王位戦 第24期・第33期・麻雀マスターズ 第8期）は読み直さず、WBK-04 の本文の表の事実を写す。読むのは残りの29期。数がこれと違えば止まる
* WBK-04 の一時のスクリプトと取得した索引は、そのセッションの一時フォルダにあったので、残っていない見込み（要確認。残っていれば使ってよい）
* 決勝が2日以上にわたる期は、すべての日を書く（「タイトル」シートの日付の列にどの日を入れるかは、次の指示でシートの書き方に合わせて決める）
* 年が本文に無い日付（「１１月２５日（日）」など）は、期の年（WBK-04 の表の「年」）と曜日で年を確かめ、曜日が合わないときは「曜日が合わない」と書いて候補にしない
* Internet Archive への要求の上限（この指示の分。WBK-04 までの分とは別に数える）: 索引は10回まで、本文は合計60ページまで（決勝の29期で1期あたり2ページまで、予定のページで5ページまで）、要求の間隔は5秒以上。429・503 が返ったら、やり直さずに止まる。UA に mj-internal を含める（CLAUDE.md「判断・作業の原則」）。`/web/<年>/<URL>` の形で読むときのリダイレクトは、1ページに数える
* 記事の文章・取得した HTML は、ログ・issue・リポジトリに入れない（事実〈日付・順位・名前・点〉と、元の URL・保存日だけ）。一時のスクリプトと取得した HTML はクローンの外（このセッションの一時フォルダ）に置き、コミットしない
* 別の経路（写しのサイト・中継・プロキシ・VPN、Claude in Chrome・アプリ内ブラウザでの閲覧、WebFetch を含む）は試さない
* クローンの `core.autocrlf` は WBK-04 で `false` にした（要確認。`git config --local core.autocrlf` が false でなければ止まる）

手順

1. 準備
   * `git fetch` し、`git status` がきれいなこと、`core.autocrlf` が false であることを確かめる。作業ブランチを冒頭のとおりに用意し、ログを先に push する（push で GitHub へのサインインを求められたら、平野さんにブラウザでのサインインを頼んで待つ）
   * CHAT-1116-WBK-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1116-WBK-05` を足す
   * docs/decisions/automation.md の WBK-01 の節に、WBK-04 の結果の要約（PC から届いた、見積もりの数〈ありそう33・分からない89・無さそう90、#684 は1期だけ〉）と、上の決定2つを追記する（追記先の今の内容を読んでから。矛盾があれば止まる）。docs/notes/cloud-sessions.md「ネットワーク」の web.archive.org の行に「2026-11-16 に許可するホストから外した（平野さん）」を足す
2. 読む（上限は前提のとおり）
   * 29期の決勝のページを読み、期ごとに表にする（全件）: 大会・期・元の URL・保存日・文字コード・決勝の日付（本文の書き方をそのままの意味で、年月日に直したもの。複数日はすべて）・年と曜日の確かめ・1位から最下位までの順位と点が載っているか（載っていれば名前と点も事実として書く）・形（表か文章か）。日付が取れなかった期は理由を書く。WBK-04 で確かめた4期は、同じ表に WBK-04 の本文の表から写す（「WBK-04 の本文 n」と書く）
   * `/title-fight/schedule.html` を、保存の年が偏らないように5ページまで読み、決勝の日付が大会・期つきで載っているか、載っていればどの大会・期の分が分かるかを表にする（WBK-04 の見積もりで「分からない」89期のうち、当たる期の数を大会ごとに書く）
   * 要求の表（時刻・種類・応答・時間・大きさ）を WBK-04 のログと同じ形で書く
3. 記録
   * 数をまとめる: 日付が取れた期・取れなかった期の数（大会ごと）と、名前の一覧
   * docs/notes/research-candidates.md の「Wayback Machine の連盟公式サイト」に、分かった読み方（決勝のページの日付の書き方・年の決め方・複数ページの決勝の URL の形）だけを足す（今の内容を読んでから）
   * 「判断が必要なこと」に、次の指示（クラウドで、取り出した日付を候補タブ〈連盟ニュースのときの種別 C と同じ形〉に書き、#408・#684 にコメントする）の案を、メリット・デメリット・推奨つきで書く。予定のページで「分からない」の期が埋まりそうなら、その続きの案（PC で読む期の数・要求の数）も書く

止まる条件

* 対象の「ありそう」の期が WBK-04 のログの表で33期でない、または読み直さない4期を除いて29期にならない
* `git status` がきれいでない、`core.autocrlf` が false でない、またはサインインしても push できない
* web.archive.org に届かない（1回だけやり直してよい。2回とも届かなければ、別の経路を試さずに止まる）
* 429・503 など、相手から止められた応答が返る（やり直さずに、そこまでの結果を書いて止まる）
* 要求の上限（索引10回・本文60ページ）で読みきれない（そこまでの結果と、足りない分の見込みを書いて止まる。上限を自分で上げない）
* 差分が docs（notes・decisions・ログ）を超える、または docs の差分に改行（CRLF）だけの変化が出る
* 記事の文章・取得した HTML をコミット・ログ・issue に入れる必要が出た（入れずに止まる）
* 追記先（docs/decisions/automation.md・docs/notes/research-candidates.md・docs/notes/cloud-sessions.md）の今の内容と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張して報告に書く）
* 未マージの work/ ブランチが、変えるファイルの同じ行・節を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-WBK-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-WBK-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8ae7af4d）: https://github.com/retroeater/mj-logs/tree/main/guide/8ae7af4d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8ae7af4d.md
