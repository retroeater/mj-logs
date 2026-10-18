# CHAT-1018-SEC-07

- 着手日時: 2026-10-18
- 対象issue: #598・#601・#300
- ブランチ: work/1018-sec7
- 着手時HEAD: 0930b969d

## 指示

【Claude作成】Claude Code 向け指示：#598「平野さんの確認」11項目と Rate limiting rule・Claude Security の回し方の結果を記録する（ドキュメントと issue だけ） Chat-Ref: CHAT-1018-SEC-07 マージ: ドキュメントのみ（ログ・docs/notes・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい。コード・生成物・`.github/`・`_headers`・Worker は変えない 貼る時機: いつでも。新しいセッション: 任意（続きでも可） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-sec7 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-sec7 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-sec7 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-18 にチャットで平野さんと一問一答で確かめた #598「平野さんの確認」（ダッシュボード側）の結果、`/api/ask` の Rate limiting rule、Claude Security プラグインの回し方の決定を、#598・#601・#300 と docs に記録する。docs/notes・docs/decisions は public の mj-logs の `guide/` に写るので、トークン・鍵・サービスアカウントの名前や権限の詳しい一覧は書かない（それは private の issue #601 に書く）。
決定・確認結果（2026-10-18、平野さん。証跡はチャットのスクリーンショット）
Rate limiting rule（#598 中-1）

* Cloudflare の Rate limiting rules に「Throttle /api/ask (#598)」を足した: 条件 `(http.request.uri.path eq "/api/ask" and http.request.method eq "POST")`、数える単位 IP、1分に5回を超えたら Block（10分、応答は Cloudflare 既定の 429、順序 Last、Active）。応答の種類を既定のままにしたのは、`assets/houou_ask.js` が上限の JSON（`status: "limit"`）を「翌0時（日本時間）から受け付けます」と表示し、10分で解ける Block と食い違うため（既定の HTML なら「いまは答えられません。」になる）
* Pro プランの Rate limiting rules の枠は 2/2 で埋まった（#124 と合わせて）。#598 の直し方の案にあった「IPv6 は /64 で数える」は Pro では選べない（数える単位は IP だけ）

Claude Security プラグインの回し方

* 定期では回さない。`workers/` を大きく変えたときに差分だけの診断（Scan changes）を回し、#9（CSP）の後の再点検で1回回す。#9 の後に回した時点で、1回あたりの使用量のコストを踏まえて、今後のサイクルを改めて検討する（CHAT-1018-SEC-05 の実測: 約12分・サブエージェント約109万トークン、指摘0件）

#598「平野さんの確認」の結果

1. GitHub のアカウントの 2FA: 有効（認証アプリ・GitHub Mobile・パスキー、リカバリーコード確認済み）。SMS は外した → 済
2. `mj-scheduler` のトークン: fine-grained、対象は mj と mj-logs だけ、権限は Metadata 読み取り・Actions と Issues の読み書きだけ、期限 2027-10-05（再発行の予定はカレンダー「ryoei.pro」の 2027-09-21）→ 済。あわせて、約2か月使われていない期限なしのトークン `mj-actions-dispatch` を削除した。`mj-logs-sync` は #298 で `sync-logs.yml` を消すときに消す（CHAT-1018-SEC-06 で #298 に記載済み）
3. Secret scanning／Push protection: 個人の private リポジトリでは設定の欄が無く、使えない → 見送り。代わりは `security-check.yml` の履歴の走査（push を止める仕組みではなく、後から見つける仕組み）
4. Dependabot alerts: On（Dependency graph も On）→ 済。Malware alerts・Dependabot security updates は Off のまま（npm の依存が無い。自動の PR は SEC-04 で `dependabot.yml` と一緒に決める）
5. Actions の Workflow permissions の既定: 今は「Read and write permissions」で、「Allow GitHub Actions to create and approve pull requests」にチェックあり。まだ変えない。SEC-04 で各ワークフローに `permissions` を明示し、PR を作るワークフローが無いことを確かめてから、平野さんが既定を読み取りだけにしてチェックを外す（`permissions` の無い `regenerate-page.yml` が push しているため、先に変えると生成と本番反映が止まる）。Access は「Not accessible」で推奨どおり
6. Cloudflare の API トークン: User API Tokens は Workers Builds が作ったもの1本だけ（権限が広いのは Workers Builds の仕様。手で絞らない）、Account API tokens は Analytics Read だけのもの1本（`CF_ANALYTICS_TOKEN`）→ 済。Global API Key は使っていない
7. `/api/ask` の Rate limiting rule: 上のとおり足した → 済
8. Workers の Secret: `mj-ask` は Secret 3つ（`ANTHROPIC_API_KEY`・`GOOGLE_SA_KEY`・`IP_HASH_SALT`）と秘密でない Variable、`mj-scheduler` は Secret 1つ（`GITHUB_TOKEN`）と Variable 3つ。鍵が Variable に入っているもの・使っていない Secret は無い → 済
9. Cloudflare のアカウントの 2FA: Google ログインでパスワードが無かったため、パスワードを再設定してから有効にした。認証アプリ（TOTP）だけにし、メールの 2FA は外した。バックアップコードは作成・保存済み → 済
10. Google のサービスアカウントの鍵: プロジェクトのサービスアカウントは4つ（birthday-calendar・gsc-export・live-channel-writer・mj-ask）で、どれも鍵は1つずつ → 済
11. サービスアカウントの権限: `mj-ask` と共有しているのは「R_AIボット」のシート1つだけ（編集者、一般的なアクセスは制限付き）→ 済。ほかの3つのサービスアカウントとシートの組み合わせは #601 の棚卸しで、コードから一覧を作って突き合わせる
12. Anthropic: 組織の月間支出上限は $100（$50・$90 でメール通知）、ワークスペース「ryoei-pro-ask」の月間上限は $30。`mj-ask` のキーは ryoei-pro-ask にあり期限 2027-10-11（再発行の予定はカレンダー「ryoei.pro」の 2027-09-27）。もう1本のキー（Default のワークスペース、期限なし）がどのワークフローで使われているかは #601 で確かめる → 済

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* #598 の本文の「平野さんの確認」のチェックボックスは、上の「→ 済」「→ 見送り」の項目にチェックを付け、各行の末尾に「（2026-10-18、CHAT-1018-SEC-07）」と結果を短く書き足す（本文の書き換えはこの節だけ）。5 は「SEC-04 待ち」と書いてチェックしない
* #598 にコメント: 上の結果の要約、残り（SEC-04: Actions の SHA 固定・`permissions`・`secrets: inherit`・template-injection・`dependabot.yml`・Workflow permissions の既定の切り替えの順番、WFL の段④の後。中-3 で外した2か所は OUK・WRC・LOG のマージ後）
* #601 にコメント: 棚卸しの最初の材料として、上の 2・6・8・10・11・12 の現状（名前・置き場所・権限の範囲・期限。値は書かない）と、期限の予定をカレンダーで管理していること
* #300 にコメント: Secret scanning／Push protection は個人の private リポジトリでは使えないので見送り、代わりは `security-check.yml` の履歴の走査。#300 をクローズしてよいかは平野さんに聞く（報告の「判断が必要なこと」に書く。クローズはしない）
* docs/notes/cloudflare.md の「Rate limiting rules」の節に、上の Rate limiting rule を #124 の記録と同じ形で足す（値は平野さんの申告で、セッションからは検証できない旨。枠が 2/2 で埋まったこと）
* docs/notes/security.md: 「Claude Security プラグイン」の節に回し方の決定を、「点検の観点」の後に「ダッシュボード側の確認（年1回か大きな変更の後）」として 11項目の観点だけ（名前・値・権限の詳細は書かない）を足す
* docs/decisions/security.md に「2026-10-18（CHAT-1018-SEC-07）」の節を足し、上の決定のうち方針にあたるもの（Rate limiting rule を足したこととその値、応答を既定のままにした理由、Claude Security の回し方、Secret scanning の見送り、Workflow permissions の切り替えの順番、Cloudflare・GitHub の 2FA を認証アプリにそろえた）を書く

手順

1. #598・#601・#300 の今の本文とコメント、docs/notes/cloudflare.md の「Rate limiting rules」の節、docs/notes/security.md、docs/decisions/security.md を読む。上の結果と食い違う記述（例: #598 の項目の文面が違う）があれば、同じ趣旨なら置き換え・合わせて進め、ログに書く
2. issue（#598 本文の「平野さんの確認」節とコメント、#601・#300 のコメント）を書く
3. docs を直し、ログを書いて push し、マージする

止まる条件

* #598 の「平野さんの確認」の節が無い、または項目が上の 12 と対応しない（対応表をログに書いて止まる）
* 未マージの work/ ブランチが docs/notes/cloudflare.md の「Rate limiting rules」の節・docs/notes/security.md・docs/decisions/security.md の同じ節を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、#300 をクローズしてよいかと、#598 に残った項目（SEC-04 待ち・中-3 の残り）を書く
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみ。完了報告のうえ cloudflare へ入れてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-SEC-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-SEC-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1018-sec7
- ログ: https://github.com/retroeater/mj/blob/work/1018-sec7/docs/logs/CHAT-1018-SEC-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-sec7
- 確認用URL: なし
- マージ: 未
- issue: #598・#601・#300
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8ab126e4）: https://github.com/retroeater/mj-logs/tree/main/guide/8ab126e4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f5956855.md
