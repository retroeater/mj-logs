# CHAT-1010-RDN-03

- 着手日時: 2026-10-10
- 対象issue: なし
- ブランチ: work/1010-rdn-yama
- 着手時HEAD: 81a53d18

## 指示

【Claude作成】Claude Code 向け指示：「鳳凰」タブの「山口哲也（17期）」への改名後、現役の山口哲也プロと混ざる箇所・部分一致で出る箇所を調べる
Chat-Ref: CHAT-1010-RDN-03
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。判断が残れば状態は判断待ち）
貼る時機: いつでも（CHAT-1010-RDN-02 は判断待ちで止まっており、この指示はそれと別のブランチで行う）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rdn-yama の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1010-rdn-yama を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rdn-yama origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。未マージの work/1010-rdn（RDN-02 の判断待ち）は使わず、触らない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「鳳凰」タブには、現役の山口哲也プロとは別人の、旧い「山口哲也」さんの4行がある。これまでは名前の末尾に全角空白を付けて区別していたが、生成スクリプトは名前の前後の空白を除いて読むため、サイト側では区別が効いていなかった可能性がある（CHAT-1010-RDN-01 のログ「手順5」の山口哲也の所見）。平野さんがこの4行を「山口哲也（17期）」に改名したので、改名後のシートで、現役の山口プロと混ざる箇所・部分一致などで出てしまう箇所が残っていないかを確かめる。コード・シート・issue は変えない。
決定（2026-10-10、平野さん）

* 「鳳凰」タブの旧い山口哲也さんの4行の名前を「山口哲也（17期）」にした（平野さんが変更済み）。4行がどの期のものかは平野さんが確認済み

前提（チャット側。平野さんの決定ではない）

* 改名前の名前の末尾の全角空白は、`scripts/lib/sheets.py` の読み取り（`_normalize()`）で除かれていた（RDN-01 のログ。要確認）
* 「山口哲也（17期）」は「プロ」にも「連盟プロ以外」にも無いはず（要確認）。無ければ、名前の辞書（`lib/names.py` の `NameBook`）を使うページでは未登録の名前として扱われる見込み

手順

1. 改名が反映されているか: 生成と同じ経路（gviz）で「鳳凰」タブを読み、「山口哲也」を含む名前の行を全件、表記ごと（`repr()` で空白・括弧の種類が分かる形）に件数と期・前後・リーグで書く。改名した4行が「山口哲也（17期）」の1表記にそろっているか、末尾の空白付きなどが残っていないかを書く。同じブックのほかのタブ（「桜花」「最強戦」「対局」「別名」など、読み取れるもの全部）と、/live の3層（【1】【2】【3】、ID は `docs/notes/live-channel-write.md`）、title/ のシート、「連盟プロ以外」で、「山口哲也」を含むセルも表記ごとに件数とタブ・列で書く（現役か旧い方かは判断せず、日付・期など見分けに使える値を並べる）。
2. 混ざる・出る箇所の洗い出し: 改名前と改名後の名前で、次を確かめる。
   * 本番に出ている生成物（origin/cloudflare の `houou_race` の期ごとの JSON、`houou_leagues_data.json`、`houou_leagues.html`、`jpml_pros.html`、title/、saikyo/、live/ など、名前で引くもの全部）に、旧い4行の成績が現役の山口プロのものとして入っているか（改名前の状態で生成された物から調べる）
   * 改名後のシートで `python3 scripts/regenerate.py all` を作業ブランチ上で実行し（生成物はコミットしない）、origin/cloudflare の生成物との差のうち「山口哲也」に関わるものを全件書く。旧い4行が現役の山口プロから外れ、「山口哲也（17期）」として出る（または出ない）ことを確かめる
   * 部分一致・正規化で出る可能性: 名前を部分一致・前方一致・NFKC・空白や記号の除去で比べている箇所（ページ側の JS の検索欄〈title/・saikyo/・houou 系・jpml_pros など〉、シートの数式〈「プロ」W列の `"*"&$A2&"*"` など〉、`lib/names.py` の正規化）を grep で洗い出し、「山口哲也」で探したときに「山口哲也（17期）」が出るか、その逆も出るかを箇所ごとに書く。全角括弧が `?name=` の URL・JSON のキー・ファイル名・id に入ったときに壊れないかも書く
   * 未登録の名前の検知（`check_saikyo_unregistered.py`・/live の未登録の知らせ・`NameBook` の警告など）で「山口哲也（17期）」が出るか。出るなら「連盟プロ以外」への登録（所属団体 `-`・所属補足 `元連盟` の形）で消えるかを書く（登録は平野さんがするので、行の形の案だけを書く）
3. 未マージの work/1008-hou（鳳凰戦の新ページ houou/、別チャットで判断待ち）にだけある `scripts/generate_houou_pages.py` も、そのブランチのファイルを読んで（ブランチは変えない）、名前の照合のしかたから、改名後に混ざる・出る可能性があるかを書く。実行はしなくてよい。

止まる条件

* 「鳳凰」タブで「山口哲也（17期）」の行が4行でない（件数と表記を書いて止まる。手順1の一覧は書いてから止まる）
* 「鳳凰」の行数が2回の読みで変わった（件数を書いて止まる）
* 変更が `docs/logs/`・`docs/decisions/` の外に及びそうになった（コード・シート・issue は変えない。起票もしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。直すべき箇所があれば「判断が必要なこと」に、直し方の案と一緒に書き、状態は判断待ちにする
* マージは冒頭の「マージ:」の行のとおり（ログと decisions のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RDN-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RDN-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き
- ブランチ: work/1010-rdn-yama はローカル・リモートとも無し。`git checkout -b work/1010-rdn-yama origin/cloudflare`（work/1010-rdn は触っていない）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した

## 報告

- 状態: 作業中
- ブランチ: work/1010-rdn-yama
- ログ: https://github.com/retroeater/mj/blob/work/1010-rdn-yama/docs/logs/CHAT-1010-RDN-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rdn-yama
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 81a53d18）: https://github.com/retroeater/mj-logs/tree/main/guide/81a53d18

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/81a53d18/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/81a53d18/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/81a53d18/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/81a53d18/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/81a53d18/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/81a53d18/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/116fea10.md
