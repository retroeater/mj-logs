# Cloudflare（Workers Builds・配信基盤）の決定

## 2026-10-04（CHAT-1004-WBD-01）

- docs だけのコミットで Workers Builds が起動している疑い（cloudflare d8f0c08）の実態を調べ、起票する（#171）

## 2026-10-04（CHAT-1004-WBD-02）

- マージコミットを含む push で配信物が変わらないのにビルドが走る件を issue に起票する（未実施: 2026-09-28 の「減らす運用は採らない」〈#363〉と重なるため止めた） → 置き換え: 2026-10-04（CHAT-1004-WBD-02、平野さんの回答）
- 起票しない。2026-09-28（CW-18、減らす運用は採らない）と 2026-09-29（CX-15、ビルド回数は今のままでよい）の決定のままとする。#363・#171 へのコメントも足さない（上の「起票する」を置き換える）

## 2026-10-12（CHAT-1012-BOT-01）

- Cloudflare のボット急増のメール（2026-10-11 着）を受けても、設定は変えない（Super Bot Fight Mode は #91 の却下のまま）
- Security Overview の提案（Security action items）3件はすべて見送る
  - Super Bot Fight Mode の標準設定（Moderate）: 見送り。#91 に再確認として記録する（クローズのまま）
  - AI Labyrinth（Low）: 見送り。#447 に却下として記録してクローズする
  - security.txt（Low）: 見送り。`docs/notes/cloudflare.md` の見送った技術に書く（issue は作らない）
