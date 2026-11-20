# CHAT-1120-WBK-09

- 着手日時: 2026-11-20
- 対象issue: #408, #684
- ブランチ: work/1120-wbk
- 着手時HEAD: de4edb2ff

## 指示

【Claude作成】Claude Code 向け指示：WBK-04 で読んだ6ページ（決勝のページ4つと鳳凰戦・十段戦の歴代の表）を PC で読み直し、2位以下の順位・点の一覧に足して「タイトル」と突き合わせる（PC で実行。読むだけ。WBK-08 の続き）
Chat-Ref: CHAT-1120-WBK-09
マージ: ドキュメントのみ（ログ・決定・notes の追記）なので完了報告のうえ cloudflare へ入れてよい〈読むだけの指示。上限や 429・503 で途中で止まったときも、そこまでの記録は cloudflare へ入れてよい〉
貼る時機: いつでも（CHAT-1120-WBK-08 のマージの後。マージは済んでいる）
セッション: 必須（平野さんの PC〈R-HP〉の Claude デスクトップアプリの Code タブで、フォルダ C:\Users\hiran\Documents\claude\mj を選んでローカルのセッションを始め、そこに貼る。WBK-06 までのローカルのセッションの続きでも可。クラウドのセッションには貼らない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している。CLAUDE.md・docs/notes のうちクラウドのセッションだけに当てはまる部分（割り当てられた claude/… のブランチ、環境の許可するホスト、GitHub MCP）は、この指示では当てはまらない。
作業ブランチ: 平野さんの PC のローカルのセッションで実行する。work/1120-wbk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1120-wbk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルに古い work/ のブランチが残っているときを含め、手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1120-WBK-08（判断待ち）の続き。WBK-08 の「2位以下の一覧」で「PC で読み直しが要る」とした6ページを Wayback Machine で読み直し、名前（と点）を一覧に足して「タイトル」と突き合わせる。とくに麻雀マスターズ 第8期（#684 の1位だけの期）の2〜4位を確かめる。読むだけで、シート・候補タブ・コード・ページは変えない。

### 決定（2026-11-20、平野さん）
- WBK-08 の「PC で読み直すか」は (a)（PC のローカルのセッションで6ページを読み、一覧に足して「タイトル」と突き合わせる）
- 第25期新人王戦の3位は、「タイトル」の「朝比奈涼」が正しい（平野さんが今の公式の大会のページ https://www.ma-jan.or.jp/title-fight/shinjinou.html で確かめた）。「タイトル」・「別名」は変えない。WBK-08 の一覧の「食い違い」1行は、古いページの表記の違いとして扱う
- 「R_タイトル戦_マスタ」の live-channel-writer の共有は外した（2026-11-20、平野さんの画面の写しをチャット側が見た。残っているのは sheets-reader の閲覧者だけ）

### 前提（チャット側。平野さんの決定ではない）
- 読む6ページ（WBK-08 のログの `## 経過`「PC で読み直しが要る期」。WBK-04 で読んだのと同じ保存を、同じ `https://web.archive.org/web/<timestamp>id_/<元の URL>` の形で読む。timestamp は WBK-04 のログの要求の表から取る。要確認。食い違えば止まる）:
  - 鳳凰戦 第15期: `/titlehouou.htm`（2004-02-18 の保存。1〜4位の全員と点）
  - 王位戦 第24期: `/oui.htm`（2000-06-11 の保存。文章の中に順位が散る）
  - 王位戦 第33期: `/title_fight/oui_33_fin.php`（2008-01-16 の保存。5人の点と順位）
  - 麻雀マスターズ 第8期: `/masters.htm`（2001-02-14 の保存。1〜4位と点。#684）
  - 鳳凰戦の歴代の表: `/title-fight/houousen.html`（2019-01-01 の保存。第1〜34期の年度と1〜4位、点なし）
  - 十段戦の歴代の表: `/title-fight/10dansen.html`（2019-01-27 の保存。第1〜34期の年度と1〜5位、点なし）
- Internet Archive への要求の上限（この指示の分）: 本文は合計8ページまで（6ページと、リダイレクト・読み直しの余裕2）、索引は2回まで（timestamp が WBK-04 のログで分からないときだけ）、要求の間隔は5秒以上。429・503 が返ったら、やり直さずに止まる。UA に mj-internal を含める（CLAUDE.md「判断・作業の原則」）
- 突き合わせのやり方・列・分け方は WBK-08 のログの `## 経過`「4. 2位以下の一覧」と同じ（名前は「別名」で現在名に直してから比べる。「タイトル」の側は WBK-08 と同じく、生成済みの title/ の期のページの決勝の順位と名前を読む。PC からはシートを読まない）。歴代の表は期ごとに全順位を行にする（鳳凰戦は 34期×4、十段戦は 34期×5 の見込み）
- 第8期麻雀マスターズの2〜4位が分かっても、この指示では「タイトル」に書かない（書くかどうかは、結果を見て平野さんが決める）
- 記事の文章・取得した HTML は、ログ・issue・リポジトリに入れない（事実〈順位・名前・点〉と、元の URL・保存日だけ）。一時のスクリプトと取得した HTML はクローンの外（このセッションの一時フォルダ）に置き、コミットしない
- 別の経路（写しのサイト・中継・プロキシ・VPN、Claude in Chrome・アプリ内ブラウザでの閲覧、WebFetch を含む）は試さない
- #408・#684 へのコメントはこの指示では書かない（PC にコネクタが無い見込み。次のクラウドの指示で書く。`gh` が使えて認証済みなら書いてよい）

## 手順
1. 準備
   - `git fetch` し、`git status` がきれいなこと、`git config --local core.autocrlf` が false であることを確かめる。作業ブランチを冒頭のとおりに用意し、ログを先に push する（サインインを求められたら、平野さんにブラウザでのサインインを頼んで待つ）
   - CHAT-1120-WBK-08 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1120-WBK-09` を足す
   - docs/decisions/automation.md の WBK-01 の節に、上の決定3つを追記する（今の内容を読んでから）
2. 読む（上限は前提のとおり）
   - 6ページを読み、要求の表（時刻・種類・応答・時間・大きさ・中身）を WBK-05 と同じ形で書く
3. 一覧と記録
   - 6ページの順位・名前・点を、WBK-08 の一覧と同じ列で全件の表にし、「タイトル」と突き合わせる。突き合わせの分けごとの数と、食い違い・シートに無い行の全件を書く。歴代の表と決勝のページ・WBK-08 の一覧が同じ期を指すときは、互いの一致・食い違いも書く
   - 麻雀マスターズ 第8期の1〜4位と点を、#684 の目的（1位だけの期の2位以下）に照らして別に書く
   - docs/notes/research-candidates.md「Wayback Machine の連盟公式サイト」に、新しく分かった読み方だけを足す（今の内容を読んでから。無ければ足さない）
   - 「判断が必要なこと」に、(1) 第8期麻雀マスターズの2〜4位を「タイトル」に書くか（書くなら、そのやり方〈NWS-04・WBK-08 と同じ一時共有の形〉）、(2) 食い違い・シートに無い行の扱い、(3) #408・#684 へのコメントを書く次の指示の案を、メリット・デメリット・推奨つきで書く

## 止まる条件
- 前提の6ページの URL・保存日が WBK-04・WBK-08 のログと食い違う
- `git status` がきれいでない、`core.autocrlf` が false でない、またはサインインしても push できない
- web.archive.org に届かない（1回だけやり直してよい。2回とも届かなければ、別の経路を試さずに止まる）
- 429・503 など、相手から止められた応答が返る（やり直さずに、そこまでの結果を書いて止まる）
- 要求の上限（本文8ページ・索引2回）で読みきれない（そこまでの結果を書いて止まる。上限を自分で上げない）
- 差分が docs（notes・decisions・ログ）を超える、または docs の差分に改行（CRLF）だけの変化が出る
- 記事の文章・取得した HTML をコミット・ログ・issue に入れる必要が出た（入れずに止まる）
- 追記先（docs/decisions/automation.md・docs/notes/research-candidates.md）の今の内容と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張して報告に書く）
- 未マージの work/ ブランチが、変えるファイルの同じ行・節を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1120-WBK-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1120-WBK-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2b5f2e0f）: https://github.com/retroeater/mj-logs/tree/main/guide/2b5f2e0f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b5f2e0f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b5f2e0f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b5f2e0f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b5f2e0f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b5f2e0f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2b5f2e0f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d28d0cbb.md
