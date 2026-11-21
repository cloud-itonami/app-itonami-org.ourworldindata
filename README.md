# org.ourworldindata

`ourworldindata.org` Grapher の、世界の電源別発電量 snapshot を保全する DataLad dataset です。元データの上流出典と利用条件は付属 metadata と source catalog に保存し、統合 datom は `etzhayyim/global-energy-datoms` が作ります。

## Snapshots

* 初回 snapshot (2026-07-10取得): `raw/grapher/electricity-prod-source-stacked.csv`。energy projection が読む現行版であり、上書きしないこと。
* 第2 snapshot (2026-11-21取得): `raw/grapher/2026-11-21/`。旧 slug が `electricity-mix` Grapher へ 301 するため、実効 URL を catalog の `:url` に記録した。CSV header は同名12列だが Gas と Oil の順序が入れ替わっているため、列は名前で読むこと。差分の測定結果は `raw/grapher/2026-11-21/DIFF.edn`。
* 検証: `nbb bin/verify-source-catalog.cljk` は catalog の sha256 を再計算し、未登録の `raw/` 配下ファイルがあれば失敗する。
* 2026-11-21 snapshot への移行は owner 判断が必要なため、この変更は draft のままにする。
