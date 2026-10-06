# CHAT-1006-LGR-09

- 着手日時: 2026-10-06
- 対象issue: #507
- ブランチ: work/1006-lgr-09
- 着手時HEAD: e4bf5d12

## 指示

【Claude作成】Claude Code 向け指示：houou_race（#507、未公開）の各節のポイントの数え方を「全員が同じ速さで数える」に変える。未公開の形のまま cloudflare へマージする Chat-Ref: CHAT-1006-LGR-09 マージ: 承認済み（チャットで、2026-10-06。平野さんが試作の見比べで「同じ速さ A」を選んだ）。未公開の形のまま入れる。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1006-lgr-09 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-lgr-09 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1006-lgr-09〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-lgr-09 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
houou_race（https://ryoei.pro/houou_race.html 、未公開）の各節のポイントの数え方を変える。今は全員が節の終わりに同時に着く（動きの大きい選手ほど速く数える）。これを、全員が同じ速さで数える形にする。公開（#508）はこの指示では行わない。
決定（2026-10-06、平野さん）

* 各節のポイントの数え方は「同じ速さ A」にする: 全員が同じ速さで数える。1節は3秒のまま。その節でいちばん動いた選手が3秒かけて数え、小さく動いた選手は先に着いて止まる（たくさん動いた人は最後まで数え続ける）。試作で「同時に着く（今の本番）」「同じ速さ A」「同じ速さ B（速さを全部の節で同じにし、節の長さが変わる）」を見比べて選んだ
* CHAT-1006-LGR-07 の直しは本番で確かめる対象のまま（この指示では触れない）。正式な公開の作業は、24後 A1 のシートの直しが終わってから行う

前提（チャット側。平野さんの決定ではない）

* CHAT-1006-LGR-07 のログ（mj-logs）で確かめたこと: daff02ec で cloudflare にマージ済み、houou_race.html は noindex・メニュー未掲載、数える処理は houou_race.js にある（要確認）。今の中身は実物で確かめる
* 数え方の式（付録のとおり）: 節 k の「いちばん大きい動き」M ＝ その節を打った選手（`end` が k より大きい選手）の、その節のポイントの絶対値の最大。節の中の経過を f（0〜1、1節は今と同じ 3秒）とすると、各選手がここまでに数えたポイントは `min(その選手のその節のポイントの絶対値, M × f)`。向きはその選手の符号。その節のポイントが0の選手と、順位から外れた選手は動かさない
* 変えないもの: 1節の長さ（3秒）、節の間（0.7秒）、帯が入り終わるまでの待ち、色を付ける時機、順位の並べ方（数えている途中の値の順）、同点の並び、節のラベルの色の進み方、節のラベルを押したとき・動きを減らす設定の動き、JSON の項目、生成スクリプト
* M は houou_race.js が JSON の `cum` から表ごとに求める案（生成スクリプトと JSON は変えない）。作りの都合で JSON に持たせるほうがよければ、そうしてよい（変えた点を報告に書く）
* 付録は試作（Claude 作成、平野さんがチャットで見て選んだもの）の該当の関数だけを抜き出したもの。変数名（`node`・`f`・`N`・`nodeMax`・`p.cum`・`p.end`）は試作のもので、本番の名前に合わせる
* マージの後の自動の再生成（regenerate-page.yml）で、houou_race.html と `houou_race/` がシートの今の値で作り直されることがある（24後 A1 の直しが済んでいれば、その反映を含む）。ほかのページの生成物に差分が出ても、この指示とは関係が無く、シートの変化として扱う
* #507 は閉じない
* 使う skill は無い

手順

1. 確かめる。#507 に着手中のコメントを残す。`docs/new-page-checklist.md` の段1（未公開の形）と、houou_race.js の今の数える処理を読む
2. 直す。上の「決定」と「前提」のとおりに数え方を変える。docs/notes/houou-race.md の数え方の記述を今の形に直す（古い記述は消して置き換える）
3. プレビューで確かめ、マージする。スマホの幅（390px）で、既定（43前 B1）と 42後 A1 を再生し、次を確かめる: 節の途中（1.5秒の頃）で、小さく動いた選手はその節の終わりの値に着いて止まり、いちばん動いた選手はまだ数えている／同じ時点で、まだ数えている選手どうしは数えたポイントの絶対値が同じ／節の終わりで全員が JSON の累計と同じ値になる／最後まで再生して最終のポイントと並びが今の本番と同じ。動きを減らす設定でも確かめる。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする。マージの後、check-run と本番の HTML（https://ryoei.pro/houou_race.html に noindex があること、navbar.js・`sitemap-pages.xml`・`llms.txt` に houou_race が無いこと）を確かめる。待つのは15分までで、超えたらその時点の状態を「未確認の項目」に書いて先へ進む

止まる条件

* #507 に他セッションの着手中コメントがある。未マージの `work/` ブランチに houou_race を触るものがある
* cloudflare に入る変更が、次で説明できる差分だけでない: houou_race.js・docs/・（JSON に持たせる作りにしたときだけ）scripts/generate_houou_race.py・scripts/tests/test_houou_race.py・`houou_race/`・再生成によるシートの変化の反映
* マージする時点で、houou_race.html に noindex が無い、または navbar.js・`sitemap-pages.xml`・`llms.txt` に origin/cloudflare との差分がある（未公開の形でなくなっている）
* 最後まで再生した結果（最終のポイント・並び）が、今の本番と違う
* unittest か CLAUDE.md の検証が通らない。プレビューで横のはみ出し・ページのエラー（Cloudflare Web Analytics の beacon を除く）がある
* origin/cloudflare の取り込みで、自分で直せない衝突が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、確かめた結果、付録から変えた点、平野さんに確かめてほしい点（本番の URL を添える）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-09 を書く

付録: 試作の該当の関数（Claude 作成）

```js
// リーグを読み込んだ時に、節ごとの「いちばん大きい動き」を求める（その節を打った選手の中で）
nodeMax = Array.from({ length: N }, (_, k) => Math.max(.1, ...players.filter(p => p.end > k).map(p => Math.abs(p.cum[k + 1] - p.cum[k]))));

/* 全員が同じ速さで数える。速さは「その節でいちばん動いた選手が1節（COUNT_MS）かけて着く」速さ。
   小さく動いた選手は先に着いて止まり、いちばん動いた選手が節の終わりまで数え続ける。f は節の中の経過（0〜1） */
function valueOf(p){
  const a = Math.min(node, p.end), b = Math.min(node + 1, p.end, N), delta = p.cum[b] - p.cum[a];
  if (!delta) return p.cum[a];
  const moved = Math.min(Math.abs(delta), nodeMax[node] * f);      // ここまでに数えたポイント（全員同じ）
  return p.cum[a] + Math.sign(delta) * moved;
}

```

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1006-LGR-09"` は0件（同じチャットの LGR の続き）
- ブランチ: work/1006-lgr-09 はローカルにもリモートにも無い。`git checkout -b work/1006-lgr-09 origin/cloudflare`（e4bf5d12）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1006-lgr-09
- ログ: https://github.com/retroeater/mj/blob/work/1006-lgr-09/docs/logs/CHAT-1006-LGR-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-lgr-09
- 確認用URL: なし（作業中）
- マージ: 未
- issue: #507
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4be9367f）: https://github.com/retroeater/mj-logs/tree/main/guide/4be9367f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4be9367f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4be9367f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4be9367f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4be9367f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4be9367f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4be9367f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/824dc807.md
