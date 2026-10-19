# CHAT-1019-WRC-19

- 着手日時: 2026-10-19
- 対象issue: 起票（下の経過）
- ブランチ: work/1019-wrc19 → cloudflare へマージ（ログと docs/decisions だけ）
- 着手時HEAD: 4f73f4036

## 指示

【Claude作成】Claude Code 向け指示：「連続通過」で、シードで予選に出なかった期を途切れにしない仕様を、別の課題として起票する（コードは変えない） Chat-Ref: CHAT-1019-WRC-19 マージ: ログだけなので、完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs だけ。コード・生成物・シートは変えない） 貼る時機: CHAT-1019-WRC-18 の後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrc19 の作成・push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-wrc19 を origin/cloudflare から作る。rebase・force push はしない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ランキング「連続通過」の数え方を直す課題を issue にしておく（優先度は低い。今は直さない）。
決定（2026-10-19、平野さん）

* 今の「連続通過」は、出場しなかった期（シードで予選に出なかった期を含む）で途切れる（2026-10-18 の WRC-09 で確定した仕様）。出場しなかった期で途切れること自体は問題ない
* ただし、シードで予選に出ずトーナメントから出た期は、連続を途切れさせないようにしたい。その期は予選を通過していないので、回数は増やさない
   * 例: 第1〜3期は予選から出て通過、第4期はシードでトーナメントから出場、第5期は予選から出て通過 → 「4連続通過」（第1〜3期と第5期。第4期は途切れにも数にも入れない）
* 予選にもトーナメントにも出なかった期は、今どおり途切れにする
* 個人成績（部門別のタブなど）・質問欄の答えの「連続通過」も、ランキングと同じ数え方にそろえる
* 同じ仕様を WRC-Rリーグ（wrcr/）・JPMLリーグにも当てる
* 優先度は低い。別の課題として起票する

手順

1. 起票の前に、同じ趣旨の issue が無いかを `gh issue list --state all --search "連続通過"` などで探す。あれば新しく起票せず、その issue に上の決定をコメントする
2. 無ければ issue を1件起票する。題の案「ランキング『連続通過』で、シードで予選に出なかった期を途切れにしない（WRC・WRC-R・JPMLリーグ）」。本文に次を書く（末尾に `Chat-Ref: CHAT-1019-WRC-19`）:
   * 上の「決定」（例を含む）
   * 今の仕様と、それが書かれている所（docs/decisions の WRC-09 の決定・jwrc/ の仕様の文書・連続通過の集計の関数。実物を `grep` で探して場所を書く）
   * 直すときに決めること（チャット側の見立て。平野さんの決定ではない）: ① ある期が「シードでトーナメントから出た期」だと何で見分けるか（予選の表に行が無い期のうち、「タイトル」タブの決勝や、トーナメントの出場者のデータで分かるか。今のシートに見分けられるデータがあるかを調べる）② WRC-R・JPMLリーグの側の担当（それぞれのページを作っている系列）
   * 優先度は低い旨
3. issue の番号と URL をログに書き、上の「決定」を docs/decisions の WRC の決定と同じ置き場所に足す（起票した issue を参照する1項目）。CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージする

止まる条件

* 変更がログと docs/decisions のほかに出る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue の番号を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRC-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRC-19 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f73f403）: https://github.com/retroeater/mj-logs/tree/main/guide/4f73f403

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
