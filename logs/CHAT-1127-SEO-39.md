# CHAT-1127-SEO-39

- 着手日時: 2026-11-27
- 対象issue: なし（手順1で確かめる）
- ブランチ: work/1127-seo
- 着手時HEAD: 890e95ee1

## 指示

【Claude作成】Claude Code 向け指示：Cloudflare「Search for Agents」を ryoei.pro で有効にするかの判断材料を集める（読むだけ。何も有効にしない） Chat-Ref: CHAT-1127-SEO-39 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1127-seo を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1127-seo origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Cloudflare の新機能「Search for Agents」（ https://blog.cloudflare.com/search-for-agents/ ）を ryoei.pro のゾーンで有効にするかを平野さんが決めるための材料を集める。この指示では何も有効にせず、変わるのはログ（と完了時の docs/decisions/）だけ。
決定（2026-11-27、平野さん）

* Search for Agents を有効にするかを決める前に、読むだけの調査を SEO 系列で行う

前提（チャット側。平野さんの決定ではない）

* 記事の要点（2026-11-27 にチャット側が WebFetch で読んだ。小さいモデルの要約で、原文の全文は読んでいない。（要確認））:
   * ゾーンのトグル（ダッシュボードの Quick Actions）か API で有効にすると、Cloudflare が AI Search のインスタンスを自動で作り、サイトをクロールしてインデックスする
   * 検索のエンドポイント `/.well-known/search?q=` がサイトのドメインの下にできる。返るのは生成した回答ではなく、サイトの本文の Markdown
   * HTML のレスポンスの `<head>` に、検索の場所を知らせるメタタグが足される。Markdown for Agents が有効なら `Accept: text/markdown` の応答の YAML front matter にも同じ案内が入る
   * Free プランを含め全プランで使える。料金は AI Search の料金体系（取り込み・インデックスの保存・クエリ）で、Workers Free/Paid の枠と上限が適用される
   * 検索へのアクセスは、ボット管理・AI Crawl Control・WAF の設定に従う
* ryoei.pro の今の設定（mj-logs のガイド〈guide/cde56134〉で 2026-11-27 にチャット側が読んだ）:
   * プランは Workers Paid。Workers の Spend limits は Block usage が有効で、上限に達すると `mj-scheduler` を含む Worker の呼び出しが止まる（docs/notes/cloudflare.md「Workers の Spend limits（2026-11-19）」、docs/decisions/cloudflare.md）。値はここに写さないので、その節を読む
   * AI クローラーの扱いは docs/notes/cloudflare.md「AIクローラーの扱い（現在の設定）」（Search・Agent は Allow、Training は Disallow、Bot Preference Sync は ON）。「ボット系の設定は1つずつ変える」とある
   * `llms.txt` は手書きの静的ファイル1枚（CLAUDE.md「構成」、#161。生成対象にする issue は #227〈open〉）
   * サイトは静的 HTML で、ページ本体と同名の `.js` を分ける作りがある（CLAUDE.md「構成」）。どのページの本文が、配信される HTML に入っているか（ブラウザ側でデータを描くか）は、チャット側は確かめていない （要確認）
* 同じ論点の issue（2026-11-27 にチャット側が `gh api` の検索で確かめた）: 「Search for Agents」「Markdown for Agents」の語を含む issue は 0 件。「AI Search」の語では #304・#130（closed）・#230・#555 が出たが、本文は読んでいない （要確認）
* 自前の質問欄（Worker `mj-ask`）の仕様は docs/notes/ask-worker.md、決定は docs/decisions/houou.md
* この調査で禁じること: Search for Agents・Markdown for Agents・AI Search を有効にしない、インスタンスを作らない、Cloudflare の設定を書き換える API を呼ばない。理由は、有効にすると本番の全ページの HTML が書き換わり課金が始まるうえ、有効にするかは平野さんがまだ決めていないため。読むだけの API・本番のページの取得（`curl`。UA は CLAUDE.md「判断・作業の原則」のとおり）・公式ドキュメントの取得は禁じない

手順

1. 同じ論点の issue を検索する（クローズ済みとコメントの決定まで。検索語は「Search for Agents」「AI Search」「Markdown for Agents」「well-known」「エージェント」「llms.txt」）。出た issue の番号・状態・関係する決定を表でログの `## 経過` に書く。関係する open issue があれば着手中のコメントを書く（本文・状態・ラベルは変えない）。無ければ起票せず、その旨を報告に書く。あわせて上の「前提」の（要確認）を実物（記事の原文・リポジトリ）で確かめ、食い違いを書く
2. 公式ドキュメント（記事の原文、AI Search の料金・上限のページ、Workers の Spend limits のページ、Markdown for Agents のページ）を読み、次を出典の URL と読んだ日付きで表にする。届かなかった URL は「未確認」と書いて先へ進む（推測で埋めない）
   * (a) 料金の数え方（取り込み・保存・クエリの単位と単価）と、Workers Paid に含まれる枠
   * (b) その料金が Workers の Spend limits に数えられるか。数えられるなら、上限に達したとき `mj-scheduler`・`mj-ask` がどうなるか（docs/notes/cloudflare.md の同じ節の表と突き合わせる）
   * (c) クロールが JavaScript を実行して描いた後の本文を取るか、配信された HTML だけを読むか。クロールの頻度と、クロールの対象を絞る・外す方法（パス・sitemap・noindex のプレビューの扱い）
   * (d) `<head>` に足されるメタタグの中身と、足される範囲（全ページか）。Markdown for Agents は必須か、別に有効にするものか、料金は
   * (e) やめ方。無効にしたとき、インスタンス・インデックス・メタタグ・課金がどうなるか
   * (f) 検索のエンドポイントへのアクセスが、今の AI クローラーの設定（Search・Agent は Allow、Training は Disallow）・WAF でどう扱われるか。`robots.txt` の `Content-Signal` との関係
3. リポジトリと本番を読み、次を数えて案を出す
   * (a) ページの区分ごと（docs/notes/static-generation.md「ページの一覧」の区分）に、配信される HTML に本文（表・成績・名前）が入っているページと、ブラウザ側でデータを描くページを数える。区分・ページ数・どちらか・確かめたページの例の表を全件で書く（略さない）。sitemap の URL の数も書く
   * (b) 2 の (a) の単位に (a) の数を当てはめ、月の費用の見込みを式つきで出す（クエリの数は仮の値を2つ置き、置いた値を書く）
   * (c) 自前の質問欄（`mj-ask`）と Search for Agents の役割の違いを表にする（相手・返すもの・データの出どころ・費用を持つ側）。`llms.txt`（#161・#227）・AI 検索の言及の計測（#230）との関係も1行ずつ
   * (d) 案を「有効にする」「条件つきで有効にする（条件を書く）」「見送る」で並べ、それぞれの得・損と、推奨とその理由を `## 報告` の「判断が必要なこと」に書く。有効にする案には、平野さんの手作業の手順（画面の名前は公式ドキュメントで確かめたものだけ。直接開けるリンクを付ける）、有効にした後に動きで確かめる方法、やめる手順、外すきっかけを添える

止まる条件

* 同じ論点（Search for Agents・AI Search・Markdown for Agents を使う・使わない）の決定が、issue か docs/decisions/ に既にある（番号・ファイルと行を書いて止まる）
* 関係する issue に、他のセッションの着手中のコメントがある
* 手順2の (a)(b) の両方が「未確認」のまま（費用を出せないので、届かなかった URL を書いて止まる）
* 調べるうちに、何かを有効にしないと確かめられない点が出た（有効にせず、確かめられない点として報告に書く。これでは止まらない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-SEO-39.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1127-SEO-39 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1127-seo
- ログ: https://github.com/retroeater/mj/blob/work/1127-seo/docs/logs/CHAT-1127-SEO-39.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1127-seo
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 890e95ee）: https://github.com/retroeater/mj-logs/tree/main/guide/890e95ee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/890e95ee.md
