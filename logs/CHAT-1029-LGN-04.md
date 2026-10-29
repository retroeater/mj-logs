# CHAT-1029-LGN-04

- 着手日時: 2026-10-29
- 対象issue: #367・#639（調査のみ）
- ブランチ: work/1029-lgn-terms
- 着手時HEAD: 2df4bf7c7

## 指示

【Claude作成】Claude Code 向け指示：「ログ」のデータを Google の規約（place_id と座標だけを保存）に合わせる設計の調査（ログだけ）
Chat-Ref: CHAT-1029-LGN-04
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1029-LGN-03 の判断待ちのままでよい。work/1028-lgn には触らない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-lgn-terms の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-lgn-terms を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-lgn-terms origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Google Maps Platform の規約では、Places API から保存してよいのは place_id と座標（最大30日）だけで、ほかの内容の保存は禁じられている（チャット側の読み。下の前提）。「ログ」（#367、log/〈#639〉）の今のデータと LGN-02・03 の作りには、これに当たる保存がある見込み。平野さんは「全部合わせる」と決めた。この指示では、どこに何が保存されているかを洗い出し、規約に合う作りの設計案を出す。コード・シートは変えない（調べるだけ）。

### 決定（2026-10-29、平野さん）
- 書き込み（座標などを【2】に埋める）の前に、Google の規約の今の版を確かめ、保存する項目を規約に合わせる。既存の「Places の店名」・駅・駅の候補の列も対象に含める
- 規約には全部合わせる。Google から保存するのは place_id と座標だけにする。座標は30日を超えないように取り直す
  - 閉業: 取り直しのときにその場で判定して保存せず、閉業の店の一覧を平野さんに知らせる。平野さんが【3】で「表示」を N にする
  - ジャンル: Google のジャンルは使わない。Claude が店名・本文から付ける
  - 駅・都道府県: 国などのオープンデータと座標から計算して保存する
  - 「Places の店名」の列は消す
- 「過去メシ」（谷岡育夫の再投稿、128件）を外すのは log/ だけでよい（resource_logs.html は公開〈#640〉で log/ へ転送するので変えない）

### 前提（チャット側。平野さんの決定ではない）
- 規約: チャット側が 2026-10-29 に Google Maps Platform Service Specific Terms（2026-10-28 改定の版）を読んだ。Places API の節に「緯度・経度は最大30日まで一時的に保存してよく、その後は消す」「Places API の Google マップのコンテンツを Google 以外の地図と一緒に使わない」とあり、place_id は共通の節（Google ID のキャッシュ）で保存してよい。それ以外の内容の保存を禁じる条文は、規約本体（Google Maps Platform Terms of Service）の側にある見込み（チャット側は本体を読んでいない。要確認）。Places API の Policies のページには「地図なしで Places のデータを出すときは Google のロゴか『Google Maps』の文字を出す」とある
- 今の変換（`scripts/convert_x_posts.py`）は、Text Search（店）と Nearby Search（800m 内の駅）の結果から「Places の店名」「駅」「駅の候補」「店URL」などを【2】に書いている（要確認）。駅名の【千葉】などの印も Google の駅名から来ている（LGN-03 の報告）
- 2026-11-02 02:00 JST の週次の取り込み（`run-weekly.yml`、今の cloudflare のコード）は、LOG 系列が初回を確かめて #367 を閉じる。この指示はそれを止めない
- オープンデータの候補（チャット側の案。ほかに良いものがあれば挙げてよい）: 国土数値情報の駅データ、駅データ.jp。海外の駅（Bond Street など）は対象外になる見込み

## 手順
1. 規約を確かめる。今の版の Google Maps Platform Terms of Service（本体）と Service Specific Terms の Places API の節、Places API の Policies のページを読み（WebFetch。読めなければ読めなかったことを書く）、保存・キャッシュ・表示（帰属の表記）に関する条文を、版の日付と一緒にログの `## 経過` に引用する。チャット側の前提の読みと違えば、違いを書く（止まらない）
2. 洗い出す。`origin/cloudflare` と未マージの `work/1028-lgn` のそれぞれで、Places API から来た値を保存・表示している所を全部挙げる（シートの【2】【3】の列、生成物〈resource_logs.html・log/ の HTML と JSON〉、`docs/`・テストの固定データ、ワークフローの出力）。列ごとに「Google から来た値か・規約で保存してよいか・どう置き換えるか」の表にする。`git grep` の範囲はリポジトリ全体（除外は docs/decisions・docs/logs・handover-archive の過去の行だけ）
3. 設計案を書く。上の決定を満たす作りを、次の点ごとに、案・費用の見込み（Places の区分・回数、Claude の費用）・手間・リスクで比べ、推奨を付ける。シートへの書き込みと外部 API（Places・Claude）の呼び出しはしない（費用と本番のデータの変更を伴うため。オープンデータのダウンロードと中身の確かめはしてよい。セッションのネットワークで取れなければ、取れなかったことを書く）
   - 座標の取り直し（30日を超えない周期。週次・4週ごと・ずらして少しずつ、など）と、取り直せなかった行の座標の消し方。Place Details の項目を座標だけにすれば区分は Essentials（無料の枠 月10,000回）になる見込み。閉業の判定にだけ Pro の項目を取る回の扱い
   - 閉業の知らせ方（issue のコメントなど。平野さんが【3】を直しやすい形。docs/notes/chat-routines.md の「層の行番号と投稿IDを並べる」の規則）
   - ジャンル（Claude が付ける。既存の行に付ける費用の見込み、ジャンルの一覧の作り方）
   - 駅・駅の候補・都道府県（オープンデータの名前・ライセンス・帰属の表記・更新の頻度・大きさ・海外の扱い。今の「駅」の決め方〈本文の駅が 800m 内ならそれ〉との違い）
   - 「Places の店名」の列を消すこと、【3】に Google から来た値が平野さんの手で入っている行の扱い
   - 店URL（place_id から組み立てる形でよいか）と、ページの帰属の表記
   - 移す順番と、2026-11-02 の週次の取り込み・`work/1028-lgn`（LGN-02・03 の作り）との関係。指示を何本に分けるか（1本3項目程度）

## 止まる条件
- 同じ論点の open issue がある（「Places」「規約」「キャッシュ」などで、クローズ済みも含めて検索。あればその番号と中身を書いて止まる）
- 調べるのに、シートへの書き込み・Places や Claude の API の呼び出しが要る（呼ばずに、要る理由を書いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（設計案の推奨と、平野さんに決めてもらう点は「判断が必要なこと」に書く）
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/logs と docs/decisions だけ）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-LGN-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-LGN-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1029-lgn-terms
- ログ: https://github.com/retroeater/mj/blob/work/1029-lgn-terms/docs/logs/CHAT-1029-LGN-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-lgn-terms
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5b39b154）: https://github.com/retroeater/mj-logs/tree/main/guide/5b39b154

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5b39b154/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b39b154.md
