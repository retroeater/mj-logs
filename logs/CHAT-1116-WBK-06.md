# CHAT-1116-WBK-06

- 着手日時: 2026-11-16
- 対象issue: #408
- ブランチ: work/1116-wbk
- 着手時HEAD: f533d3bfd

## 指示

【Claude作成】Claude Code 向け指示：Wayback Machine の年間の予定のページの残りの年と、日付が取れなかった期の別のページを読み、タイトル戦の対局日（#408）の事実を足す（PC で実行。読むだけ。WBK-05 の続き） Chat-Ref: CHAT-1116-WBK-06 マージ: ドキュメントのみ（ログ・決定・notes の追記）なので完了報告のうえ cloudflare へ入れてよい〈読むだけの指示。上限や 429・503 で途中で止まったときも、そこまでの記録は cloudflare へ入れてよい〉 貼る時機: いつでも（CHAT-1116-WBK-05 のマージの後。マージは済んでいる） セッション: 必須（平野さんの PC〈R-HP〉の Claude デスクトップアプリの Code タブで、フォルダ C:\Users\hiran\Documents\claude\mj を選んでローカルのセッションを始め、そこに貼る。WBK-05 のローカルのセッションの続きでも可。クラウドのセッションには貼らない） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している。CLAUDE.md・docs/notes のうちクラウドのセッションだけに当てはまる部分（割り当てられた claude/… のブランチ、環境の許可するホスト、GitHub MCP）は、この指示では当てはまらない。 作業ブランチ: 平野さんの PC のローカルのセッションで実行する。work/1116-wbk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-wbk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルに WBK-05 の work/1116-wbk が残っているときを含め、手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1116-WBK-05（判断待ち）の続き。WBK-05 の案 (b) を行う。`/title-fight/schedule.html` のまだ読んでいない年の保存と、「ありそう」の33期のうち日付が取れなかった期の別のページを読み、決勝の日付の事実を足す。最後に、WBK-04〜06 で取れた日付を期ごとの1つの表にまとめる（次のクラウドの指示で候補タブに書くときの元にする）。読むだけで、シート・候補タブ・コード・ページは変えない。
決定（2026-11-16、平野さん）

* WBK-05 の「次の指示の案」は、WBK-05 の推奨のとおり「(b) の後に (a)」。先にこの指示（PC）で (b) を行い、その後にクラウドの指示で、取れた日付を候補タブ（種別 C と同じ形）に書き、#408・#684 にコメントする（(a)）
* 開催概要・年間の予定のページの日付（予定）も候補にする。備考に「予定」と書いて本文（観戦記など）による日付と分け、採るかどうかは平野さんが決める

前提（チャット側。平野さんの決定ではない）

* 読む対象（WBK-05 のログの `## 経過`「決勝の日付（33期、全件）」「予定のページ」と「判断が必要なこと」の (b) から。要確認。食い違えば止まる）:
   * `/title-fight/schedule.html` の、まだ読んでいない年の保存（WBK-05 は 2013・2015・2017・2019・2020年の保存を読んだ。2012・2014・2016・2018年の保存など）。WBK-04 の見積もりで「分からない」89期のうち、WBK-05 で当たらなかった2012〜2019年の期を埋めるのが目的
   * 日付が取れなかった期の別のページ: 鳳凰戦 第21期（`/21houou.htm`・`/21houou-1〜3.htm` など）、麻雀グランプリMAX 第1期、新人王戦 第21・22期、プロクイーン 第1・4・5期（決勝・開催概要の別のページ）。あわせて、プロクイーン 第6期の二日目の日付
   * 鳳凰戦 第15期（「99年３月」で月まで）は、別のページが見つかれば読んでよい（見つからなければそのまま）
* 保存日・URL の候補を探すのに、CDX の索引を使ってよい（索引に無い URL は読まない）
* Internet Archive への要求の上限（この指示の分。WBK-05 までの分とは別に数える）: 索引は5回まで、本文は合計25ページまで（予定のページで8ページまで、期の別のページで17ページまで）、要求の間隔は5秒以上。429・503 が返ったら、やり直さずに止まる。UA に mj-internal を含める（CLAUDE.md「判断・作業の原則」）。読み方は WBK-05 と同じ `https://web.archive.org/web/<timestamp>id_/<元の URL>` の形
* 年の決め方・曜日の確かめ・「当て」（期の記載が無く、同じ大会の決勝の時期と年から当てたもの）の書き方は、WBK-05 と docs/notes/research-candidates.md「Wayback Machine の連盟公式サイト」のとおり
* 記事の文章・取得した HTML は、ログ・issue・リポジトリに入れない（事実〈日付・順位・名前・点〉と、元の URL・保存日だけ）。一時のスクリプトと取得した HTML はクローンの外（このセッションの一時フォルダ）に置き、コミットしない
* 別の経路（写しのサイト・中継・プロキシ・VPN、Claude in Chrome・アプリ内ブラウザでの閲覧、WebFetch を含む）は試さない
* #408・#684 へのコメントはこの指示では書かない（次のクラウドの指示で書く）

手順

1. 準備
   * `git fetch` し、`git status` がきれいなこと、`git config --local core.autocrlf` が false であることを確かめる。作業ブランチを冒頭のとおりに用意し、ログを先に push する（サインインを求められたら、平野さんにブラウザでのサインインを頼んで待つ）
   * CHAT-1116-WBK-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1116-WBK-06` を足す
   * docs/decisions/automation.md の WBK-01 の節に、上の決定を追記する（追記先の今の内容を読んでから）
2. 読む（上限は前提のとおり）
   * 予定のページ: 保存日ごとに、載っている決勝（大会・期〈無ければ「期なし」〉・日付〈予定〉）を WBK-05 の「予定のページ」と同じ形で表にし、当たる期を大会ごとに書く（WBK-05 で当たった31期と重なるものは「重なり」と書き、日付が違えば両方を書く）
   * 期の別のページ: WBK-05 の「決勝の日付（33期、全件）」と同じ列で、読んだ期の行を書く（全件。取れなかった期は理由を書く）
   * 要求の表（時刻・種類・応答・時間・大きさ・中身）を WBK-05 と同じ形で書く
3. まとめと記録
   * 期ごとの1つの表（全件）: WBK-04〜06 で日付が取れたすべての期について、大会・期・年・日付（複数日はすべて）・出典の種類（観戦記などの本文／開催概要〈予定〉／年間の予定〈予定〉／うち「当て」）・元の URL・保存日・2位以下の順位と点の有無。同じ期に出典が2つ以上あれば全部書き、日付が違えばそう書く。最後に、出典の種類ごと・大会ごとの期の数と、日付が取れなかった「ありそう」の期の名前の一覧を書く
   * docs/notes/research-candidates.md「Wayback Machine の連盟公式サイト」に、新しく分かった読み方だけを足す（今の内容を読んでから。無ければ足さない）
   * 「判断が必要なこと」に、次のクラウドの指示（上の表を候補タブ〈種別 C と同じ形〉に書き、#408・#684 にコメントする）で決めることを書く: 複数日の決勝の候補の日付をどれにするか（「タイトル」シートの今の書き方は次の指示で確かめるので、案だけ）、同じ期で出典の日付が違うときの扱い、「当て」の期を候補に入れるか。それぞれメリット・デメリット・推奨つき

止まる条件

* 前提の読む対象が WBK-05 のログと食い違う
* `git status` がきれいでない、`core.autocrlf` が false でない、またはサインインしても push できない
* web.archive.org に届かない（1回だけやり直してよい。2回とも届かなければ、別の経路を試さずに止まる）
* 429・503 など、相手から止められた応答が返る（やり直さずに、そこまでの結果を書いて止まる）
* 要求の上限（索引5回・本文25ページ）で読みきれない（そこまでの結果と、足りない分の見込みを書いて止まる。上限を自分で上げない。まとめの表は、そこまでの分で作る）
* 差分が docs（notes・decisions・ログ）を超える、または docs の差分に改行（CRLF）だけの変化が出る
* 記事の文章・取得した HTML をコミット・ログ・issue に入れる必要が出た（入れずに止まる）
* 追記先（docs/decisions/automation.md・docs/notes/research-candidates.md）の今の内容と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張して報告に書く）
* 未マージの work/ ブランチが、変えるファイルの同じ行・節を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-WBK-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-WBK-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8b20a20f）: https://github.com/retroeater/mj-logs/tree/main/guide/8b20a20f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
