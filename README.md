# off-japan-names

Japanese product names for barcodes (JAN / EAN) of products sold in Japan, derived from the [Open Food Facts](https://world.openfoodfacts.org) database.

> Contains information from Open Food Facts, which is made available here under the Open Database License (ODbL).

日本で売られている品のバーコード（JAN）と、日本語の商品名の表です。[Open Food Facts](https://world.openfoodfacts.org)（OFF）のデータを元にしています。OFF に日本語の名前が無い品は、AI（Anthropic の Claude）で訳しました。

## License / ライセンス

- This database is made available under the [Open Database License (ODbL) v1.0](https://opendatacommons.org/licenses/odbl/1-0/). Any rights in individual contents of the database are licensed under the [Database Contents License (DbCL) v1.0](https://opendatacommons.org/licenses/dbcl/1-0/). See [LICENSE](LICENSE).
- この表は ODbL 1.0 で公開します。表の中の個々の中身は DbCL 1.0 です（本文は [LICENSE](LICENSE)）。
- If you publicly use this database or a work produced from it, ODbL requires you to keep the notice above and to share any derivative database under the same license.
- この表、またはこの表から作った物を公に使うときは、上の告知を付け、表を手直しした物は同じ ODbL で渡してください（ODbL 4.3・4.4・4.6）。
- Product images are **not** included. / 商品の画像は入っていません。

## Files / ファイル

| File | Contents |
|---|---|
| `off-jp-names.csv` | One row per barcode. UTF-8 (no BOM), LF, RFC 4180 quoting. |
| `off-alias.csv` | Links from names in `off-jp-names.csv` to generic item names (added 2026-10-31). One row per barcode. Same encoding. |
| `LICENSE` | ODbL 1.0 and DbCL 1.0, full texts. |

## Columns / 列

| Column | Meaning |
|---|---|
| `code` | Barcode. 13 digits (EAN-13 / JAN) or 8 digits (EAN-8 / short JAN). Check digit verified. UPC-A (12 digits) is stored as 13 digits with a leading `0`. In-store / restricted-circulation codes (13 digits with prefix `02`, `040`–`049` or `20`–`29`; 8 digits with prefix `2`) are excluded. |
| `name_ja` | Japanese product name. / 日本語の商品名 |
| `name_ja_source` | `off` = the Japanese name as written in Open Food Facts. `ai` = translated by Claude from the OFF name in another language. / `off`＝OFF に書かれた日本語の名前のまま・`ai`＝ほかの言語の名前を Claude で訳した名前 |
| `name_orig` | The OFF name the row is based on (for `off`, the same Japanese name; for `ai`, the name that was translated). |
| `name_orig_lang` | Language code of `name_orig` as tagged in OFF. |
| `brands` | `brands` field of OFF, as is. |
| `quantity` | `quantity` field of OFF, as is (not included in `name_ja`). |
| `off_url` | The product page on Open Food Facts. |

## How it was made / 作り方

1. Source: `food.parquet` of the Hugging Face dataset [`openfoodfacts/product-database`](https://huggingface.co/datasets/openfoodfacts/product-database) (a daily conversion of the OFF JSONL dump), revision `89cfb571db20b1f1b246f27cab71837fc96225b1` (2026-10-27 09:07 UTC). Only the bulk file was used; the OFF API was not used.
2. Selection: products whose `countries_tags` contains `en:japan` (or `en:日本`), or whose code starts with `45` or `49` (GS1 Japan prefixes). Codes are normalized to 8 or 13 digits and kept only if the check digit is valid; in-store codes are dropped. Codes (8 or 13 digits) with GS1 prefixes of the former Soviet Union and the Baltic states (`460`–`470`, `474`–`478`, `481`–`488`) are excluded: most of them are tagged only `en:japan` and look mis-tagged (the 8-digit ones are mostly products of European retailers). The country tags are as written by OFF contributors and may be wrong; some 8-digit codes starting with `45`/`49` are store numbers of European retailers, not Japanese products.

   抜き出しの条件：国の札に日本が付いた品か、code が 45・49（GS1 Japan の頭）で始まる品。code は 8桁か13桁にそろえ、チェックデジットが正しい物だけを残し、店内コード（13桁の 02・040〜049・20〜29、8桁の 2 で始まる番号）は落としました。GS1 の頭が旧ソ連・バルト三国（460〜470・474〜478・481〜488）の品は、8桁・13桁とも、ほとんどが日本の札だけを持ち、札の誤りと見られるので入れていません（8桁の物は多くが欧州の店の品）。国の札は OFF の利用者が書いた物で、誤りがありえます。
3. Japanese name: a `product_name` entry tagged `ja` (or the mistaken `jp`) that contains kana or kanji; else the main-language entry if the product's main language is Japanese; else any entry containing kana. A `ja` entry written only in Latin letters (e.g. `Wonda Coffee`) is treated as not Japanese and translated. Kanji-only names not tagged Japanese are treated as not Japanese (they are often Chinese).
4. Translation (`ai` rows): for products without a Japanese name, the OFF names (all languages), generic name, brands and category tags were given to Claude (Anthropic), asking for a name close to how it would be labelled in a Japanese supermarket, brand names in katakana, no quantity, and an empty answer when unsure (no invented names). Answers that did not contain Japanese characters or were longer than 40 characters were discarded. Products with no name in any language are not included.
5. **Translations are machine-generated and may be wrong.** Please report errors (see Contact).
6. Corrections (2026-10-30): rows whose OFF name was found not to be a product name, or to name a different product, were corrected by hand after checking retailer pages and the OFF photo. A corrected row has `name_ja_source = ai`; `name_orig` is the main-language OFF name.

訳（`ai` の行）は機械の訳です。誤りがありえます。見つけたらお知らせください。

## `off-alias.csv`: links to generic items / 品目へのつなぎ（2026-10-31〜）

Links from product names in `off-jp-names.csv` to the generic item names of the item table used by the app “スマート冷蔵庫” (Digicle), to choose its estimated shelf life, storage place and nutrition. The item table itself (shelf life, storage, nutrition values, pictures) is **not** part of this database and is not included.

`off-jp-names.csv` の商品名を、アプリ「スマート冷蔵庫」（Digicle）の品目の表の行の名前（一般名）につないだ表です。アプリはこのつなぎで、期限の目安・置き場所・栄養を選びます。品目の表そのもの（期限・置き場所・栄養の値・絵）はこの表に入っていません。

| Column | Meaning |
|---|---|
| `code` | Barcode, same as `off-jp-names.csv`. |
| `name_ja` | The `name_ja` of the same row in `off-jp-names.csv`. |
| `item` | The generic item name the product is linked to. / つなぐ先の品目の名前 |
| `link_type` | `K1` = the same item; only the maker name or symbols differ. `K2` = the same kind of item, differing in flavor, size or container. `K3` = a brand name linked to its generic item. `K4` = no generic item matched, so a new generic item name was added to the item table and the product was linked to it (added 2026-11-01). / `K1`＝社名・記号だけ違う・`K2`＝味・大きさ・容器の違い・`K3`＝銘柄 → 一般名・`K4`＝合う品目が無く、品目の表に新しい一般名の行を足してつないだ（2026-11-01〜） |

How it was made: the links were proposed by AI (Claude) from the OFF name, brands, quantity and categories; checked by rules (storage place and type of shelf life against the OFF categories and the name); and, for names whose estimated shelf life changes by 3 times or more, confirmed against the OFF photo and maker or retailer pages (this check was also done with AI assistance). Names that could not be decided are not linked. **Links may be wrong**; please report errors (see Contact).

作り方：OFF の名前・ブランド・量・分類から AI（Claude）がつなぐ先を出し、置き場所と期限の種類を OFF の分類と名前で照らし、期限の目安が3倍以上変わる名前は OFF の写真とメーカー・小売のページで確かめました（この確かめも AI を使って行いました）。決められなかった名前はつないでいません。誤りがありえます。見つけたらお知らせください。

For `K4` (2026-11-01), the new item names are generic names written by us, not OFF names or brands, and the values of the new item rows (shelf life, storage, nutrition) were decided from maker and retailer pages (mostly Japanese) and the Standard Tables of Food Composition in Japan, not from Open Food Facts; their pictures are reused from similar existing items. A product is linked only when its storage place matches the new item and its estimated shelf life does not change by 3 times or more (or was confirmed as above).

`K4`（2026-11-01）の新しい品目の名前は、OFF の名前や銘柄ではなく、私たちが書いた一般名です。新しい行の値（期限・置き場所・栄養）は、メーカー・小売のページ（主に日本）と日本食品標準成分表で決め、Open Food Facts には拠っていません。絵は似た今の品目の絵を使っています。つなぐのは、置き場所が新しい品目と同じで、期限の目安が3倍以上変わらない名前（変わる名前は上と同じく確かめた名前）だけです。

| | Rows |
|---|---:|
| `link_type = K1` | 379 |
| `link_type = K2` | 1278 |
| `link_type = K3` | 1204 |
| `link_type = K4` | 306 |
| **Rows in `off-alias.csv`** (2842 names) | **3167** |

The links were added in batches: batch 1 on 2026-10-31 (848 rows), batches 2–4 on 2026-11-01 (all the remaining links from the sorting), batch 5 on 2026-11-01 (285 rows, mostly `K4`), batch 6 on 2026-11-01 (26 rows for 25 names, `K3` 1, `K4` 25: names whose links had been left for a decision), and batch 7 on 2026-11-02 (1 row for 1 name, `K4` 1: a name still left for a decision after batch 6). On 2026-11-02, after a review, 11 rows were removed and 7 rows were linked to a different item (see “Changes to links” below). Batch 8 on 2026-11-02 added 15 rows for 15 names (`K4` 15: the last names left for a decision, linked to 2 generic item names newly added to the item table). On 2026-11-03, 3 rows for 3 names were removed because the app now finds these names in the item table directly (see “Changes to links” below). On 2026-11-04, 1 row for 1 name was removed for the same kind of reason, and 1 row was linked to a different item (see “Changes to links” below). On 2026-11-05, 1 row was linked to a different item (see “Changes to links” below). On 2026-11-10, 15 rows for 13 names were removed, mostly because the app now finds these names in the item table directly, and 16 rows for 10 names were linked to a different item (see “Changes to links” below). On 2026-11-16, 2 rows for 2 names were removed because the app now answers these names directly from its item table (see “Changes to links” below). On 2026-11-18, 66 rows for 62 names were linked to a different item that fits the product better, mostly flavored or differently shaped products that had been linked to the plain item (see “Changes to links” below). / つなぎは何回かに分けて足しました（2026-10-31 に1回目の 848 行・2026-11-01 に2〜4回目＝仕分けたつなぎの残り全部・2026-11-01 に5回目の 285 行＝主に `K4`・2026-11-01 に6回目の 26 行（名前 25・`K3` 1・`K4` 25）＝つなぐかどうかを決めかねていた名前・2026-11-02 に7回目の 1 行（名前 1・`K4` 1）＝6回目のあとも決めかねていた名前）。2026-11-02 に、見直しで 11 行を外し、7 行のつなぐ先を替えました（下の「つなぎの直し」）。2026-11-02 に8回目の 15 行（名前 15・`K4` 15）＝最後まで決めかねていた名前を、品目の表に新しく足した 2 つの品目につなぎました。2026-11-03 に、3 行（名前 3）を外しました＝アプリが品目の表でこの名前を直接引けるようになったためです（下の「つなぎの直し」）。2026-11-04 に、同じわけで 1 行（名前 1）を外し、1 行のつなぐ先を替えました（下の「つなぎの直し」）。2026-11-05 に、1 行のつなぐ先を替えました（下の「つなぎの直し」）。2026-11-10 に、15 行（名前 13）を外し＝主にアプリが品目の表でこの名前を直接引けるようになったためです、16 行（名前 10）のつなぐ先を替えました（下の「つなぎの直し」）。2026-11-16 に、2 行（名前 2）を外しました＝アプリが品目の表でこの名前に直接答えるようになったためです（下の「つなぎの直し」）。2026-11-18 に、66 行（名前 62）のつなぐ先を、品により合う品目に替えました＝主に、素の品目につないでいた味つき・形違いの品です（下の「つなぎの直し」）。

## Changes to links / つなぎの直し（2026-11-18）

On 2026-11-18, 66 rows in `off-alias.csv` were linked to a different item; no row was added or removed. This brings the table in line with the app as published on 2026-11-18.

- Most of these products are flavored, sweetened or differently shaped versions of a plain item (canned fish in a sauce, flavored yogurt, short pasta, flavored sesame, and so on). The app also uses an item to tell which ingredients of its built-in recipes you have; these products should not count as the plain ingredient, so they are now linked to an item that fits them better. Several of these items are new (see the last paragraph).
- For 53 of the 66 rows, the estimated shelf life and storage do not change. They change for these items: 常温 味付けいなり (3 rows: 60 days → 90 days, refrigerated → room temperature); チーズデザート 個包装 (1 row: 5 days → 180 days); ちんすこう (2 rows: 180 days → 14 days); ごま塩 (5 rows: 150 days → 180 days); ブラウニーミックス (2 rows: 365 days → 180 days). Nutrition values follow the item each product is now linked to.
- 3 rows for energy bars change from `K4` to `K3`, because the item table now has a generic item エナジーバー for them. 3 rows for seasoned fried tofu change from `K1` (2 rows) or `K3` to `K3` for the new room-temperature item.

2026-11-18 に、`off-alias.csv` の 66 行のつなぐ先を替えました（足した行・外した行はありません）。2026-11-18 に公開したアプリに、この表を合わせました。

- 多くは、素の品目につないでいた、味つき・加糖・形違いの品です（ソース煮の魚の缶詰・味つきのヨーグルト・短いパスタ・味つきのごま など）。アプリは品目で、内蔵のレシピの材料が家にあるかも見ます。この品は素の材料に数えないほうがよいので、品により合う品目につなぎ直しました。いくつかは新しく足した品目です（最後の段落）。
- 66 行のうち 53 行は、期限の目安と置き場所が変わりません。変わるのは次の品目です：常温 味付けいなり（3 行：冷蔵 60日 → 常温 90日）・チーズデザート 個包装（1 行：5日 → 180日）・ちんすこう（2 行：180日 → 14日）・ごま塩（5 行：150日 → 180日）・ブラウニーミックス（2 行：365日 → 180日）。栄養の値は、つなぎ直した先の品目の値になります。
- エナジーバーの 3 行は `K4` から `K3` になります＝品目の表に一般名の エナジーバー があるためです。味付けの油揚げの 3 行は、`K1`（2 行）か `K3` から、新しい常温の品目への `K3` になります。

| `code` | `name_ja` | Before / 前 | After / 後 | Why / 理由 |
|---|---|---|---|---|
| `3019080040070` | プティナヴィール サバフィレ 粒マスタードソース | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `3019081237790` | プティナヴィール サバフィレ グリル トマト&プロヴァンスハーブ | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `3019081238360` | プティナヴィール サバフィレ トマト&ハーブ | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `3165950210651` | ソーピケ サバフィレ カタラン風 | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `3165958535015` | ソーピケ サバフィレ エスカベーシュ | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `3263670237719` | ファール・デクミュール サバフィレ ディジョンマスタード オーガニック | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `4902950116427` | フィーアディアマンテン さばフィレ 野菜入りトマトソース | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `4902950116823` | フィーアディアマンテン さばフィレ 野菜入りトマトソース | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `8412439285262` | 有機サバフィレ マスタードソース漬け | さば缶 (`K3`) | 味付けさば缶 (`K3`) | canned mackerel in a sauce or with seasoning; linked to the new item 味付けさば缶, not counted as plain canned mackerel in recipes / ソースや味を付けたさばの缶詰・新しい品目 味付けさば缶 へ（レシピでは素のさば缶に数えない） |
| `3165950215977` | ソーピケ ツナ マスタードソース | ツナ缶 (`K3`) | 味付けツナ缶 (`K3`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `3165950217797` | ソーピケ ツナのリエット トマト | ツナ缶 (`K3`) | 味付けツナ缶 (`K3`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `4806530070027` | ゴールドシーズ キハダマグロチャンク オリーブオイル漬け チリ入り | ツナ缶 (`K3`) | 味付けツナ缶 (`K3`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `4806530070072` | ゴールドシーズ ツナチャンク レモン&ペッパー | ツナ缶 (`K3`) | 味付けツナ缶 (`K3`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `4902950115093` | フィーアディアマンテン ツナ スパイシー | ツナ缶 (`K2`) | 味付けツナ缶 (`K2`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `4902950116571` | フィーアディアマンテン ツナ 甘酢 | ツナ缶 (`K2`) | 味付けツナ缶 (`K2`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `4902950116809` | フィーアディアマンテン ツナ 辛口 | ツナ缶 (`K2`) | 味付けツナ缶 (`K2`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `4902950116816` | フィーアディアマンテン ツナ オリーブオイル チリ | ツナ油漬 (`K2`) | 味付けツナ缶 (`K2`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `4902950117226` | フィーアディアマンテン ツナ チリ | ツナ缶 (`K2`) | 味付けツナ缶 (`K2`) | canned tuna in a sauce or with seasoning; linked to the new item 味付けツナ缶, not counted as plain canned tuna in recipes / ソースや味を付けたツナの缶詰・新しい品目 味付けツナ缶 へ（レシピでは素のツナ缶に数えない） |
| `3263670014457` | ファール・デクミュール イワシ トマトソース煮 オーガニック 油なし | サーディン (`K3`) | いわし味付 (`K3`) | sardines in tomato sauce; linked to いわし味付 (seasoned canned sardines), not to サーディン (sardines in oil) / トマトソース煮のいわし・サーディン（油漬け）ではなく いわし味付 へ |
| `45017452` | ダノン ダノニーノ 携帯用 | ヨーグルト (`K3`) | 加糖ヨーグルト (`K3`) | flavored or sweetened yogurt; linked to the new item 加糖ヨーグルト, not counted as plain yogurt in recipes / 味つき・加糖のヨーグルト・新しい品目 加糖ヨーグルト へ（レシピでは素のヨーグルトに数えない） |
| `45416880` | エンヴィア ラズベリー入り発酵乳デザート | ヨーグルト (`K3`) | 加糖ヨーグルト (`K3`) | flavored or sweetened yogurt; linked to the new item 加糖ヨーグルト, not counted as plain yogurt in recipes / 味つき・加糖のヨーグルト・新しい品目 加糖ヨーグルト へ（レシピでは素のヨーグルトに数えない） |
| `4891118045337` | ネスレ ブルーベリーヨーグルト | ヨーグルト (`K2`) | 加糖ヨーグルト (`K2`) | flavored or sweetened yogurt; linked to the new item 加糖ヨーグルト, not counted as plain yogurt in recipes / 味つき・加糖のヨーグルト・新しい品目 加糖ヨーグルト へ（レシピでは素のヨーグルトに数えない） |
| `4902720117401` | アロエヨーグルト | ヨーグルト (`K2`) | 加糖ヨーグルト (`K2`) | flavored or sweetened yogurt; linked to the new item 加糖ヨーグルト, not counted as plain yogurt in recipes / 味つき・加糖のヨーグルト・新しい品目 加糖ヨーグルト へ（レシピでは素のヨーグルトに数えない） |
| `49100761` | フルーツヨーグルト | ヨーグルト (`K2`) | 加糖ヨーグルト (`K2`) | flavored or sweetened yogurt; linked to the new item 加糖ヨーグルト, not counted as plain yogurt in recipes / 味つき・加糖のヨーグルト・新しい品目 加糖ヨーグルト へ（レシピでは素のヨーグルトに数えない） |
| `49577570` | バニラヨーグルト | ヨーグルト (`K2`) | 加糖ヨーグルト (`K2`) | flavored or sweetened yogurt; linked to the new item 加糖ヨーグルト, not counted as plain yogurt in recipes / 味つき・加糖のヨーグルト・新しい品目 加糖ヨーグルト へ（レシピでは素のヨーグルトに数えない） |
| `4992790029780` | 有機バニラヨーグルト | ヨーグルト (`K2`) | 加糖ヨーグルト (`K2`) | flavored or sweetened yogurt; linked to the new item 加糖ヨーグルト, not counted as plain yogurt in recipes / 味つき・加糖のヨーグルト・新しい品目 加糖ヨーグルト へ（レシピでは素のヨーグルトに数えない） |
| `45605666` | エロスキ ケフィア | ヨーグルト (`K3`) | ケフィア (`K3`) | kefir; the item table has a row for ケフィア / ケフィア・品目の表に ケフィア の行がある |
| `4502278081362` | カルフール スプリットピーのフジッリ | パスタ 500g (`K3`) | フジッリ (`K3`) | fusilli or farfalle; the item table has a row for this pasta shape, counted as short pasta (not spaghetti) in recipes / フジッリ・ファルファッレ・品目の表にこの形の行がある（レシピではスパゲッティでなく短いパスタに数える） |
| `49130614` | バリラ ファルファッレ | パスタ 500g (`K3`) | ファルファッレ (`K3`) | fusilli or farfalle; the item table has a row for this pasta shape, counted as short pasta (not spaghetti) in recipes / フジッリ・ファルファッレ・品目の表にこの形の行がある（レシピではスパゲッティでなく短いパスタに数える） |
| `8009004130050` | アルチェネロ 有機フジッリ | パスタ 500g (`K3`) | フジッリ (`K3`) | fusilli or farfalle; the item table has a row for this pasta shape, counted as short pasta (not spaghetti) in recipes / フジッリ・ファルファッレ・品目の表にこの形の行がある（レシピではスパゲッティでなく短いパスタに数える） |
| `3252970006163` | アルピナ クロゼ | パスタ 500g (`K3`) | ペンネ 500g (`K3`) | short or shaped pasta, not spaghetti; linked to ペンネ 500g, not counted as spaghetti in recipes / 短いパスタ・形のパスタ（スパゲッティではない）・ペンネ 500g へ（レシピではスパゲッティに数えない） |
| `3252970009478` | アルピナ トゥルネット サヴォワ風パスタ | パスタ 500g (`K3`) | ペンネ 500g (`K3`) | short or shaped pasta, not spaghetti; linked to ペンネ 500g, not counted as spaghetti in recipes / 短いパスタ・形のパスタ（スパゲッティではない）・ペンネ 500g へ（レシピではスパゲッティに数えない） |
| `3252970700115` | アルピナ・サヴォワ フリゼット サヴォワ風パスタ | パスタ 500g (`K3`) | ペンネ 500g (`K3`) | short or shaped pasta, not spaghetti; linked to ペンネ 500g, not counted as spaghetti in recipes / 短いパスタ・形のパスタ（スパゲッティではない）・ペンネ 500g へ（レシピではスパゲッティに数えない） |
| `4894514026771` | パパアルフレド アルファベットパスタ | パスタ 500g (`K2`) | ペンネ 500g (`K2`) | short or shaped pasta, not spaghetti; linked to ペンネ 500g, not counted as spaghetti in recipes / 短いパスタ・形のパスタ（スパゲッティではない）・ペンネ 500g へ（レシピではスパゲッティに数えない） |
| `4902170251731` | ハローキティ パスタ | パスタ 500g (`K3`) | ペンネ 500g (`K3`) | short or shaped pasta, not spaghetti; linked to ペンネ 500g, not counted as spaghetti in recipes / 短いパスタ・形のパスタ（スパゲッティではない）・ペンネ 500g へ（レシピではスパゲッティに数えない） |
| `4992825398454` | ヘルシーグルメ パレオ ごま粉スパイラルパスタ | パスタ 500g (`K3`) | ペンネ 500g (`K3`) | short or shaped pasta, not spaghetti; linked to ペンネ 500g, not counted as spaghetti in recipes / 短いパスタ・形のパスタ（スパゲッティではない）・ペンネ 500g へ（レシピではスパゲッティに数えない） |
| `7610849679232` | コープ ナチュラプラン 有機スペルト小麦コンキリエ | パスタ 500g (`K3`) | ペンネ 500g (`K3`) | short or shaped pasta, not spaghetti; linked to ペンネ 500g, not counted as spaghetti in recipes / 短いパスタ・形のパスタ（スパゲッティではない）・ペンネ 500g へ（レシピではスパゲッティに数えない） |
| `4712972150244` | 全粒穀物エナジーバー | シリアルバー (`K4`) | エナジーバー (`K3`) | an energy bar; the item table has a row for エナジーバー (now a brand name linked to its generic item, `K3`) / エナジーバー・品目の表に エナジーバー の行がある（銘柄 → 一般名＝`K3`） |
| `4897010443844` | ビーカインド エナジーバー | シリアルバー (`K4`) | エナジーバー (`K3`) | an energy bar; the item table has a row for エナジーバー (now a brand name linked to its generic item, `K3`) / エナジーバー・品目の表に エナジーバー の行がある（銘柄 → 一般名＝`K3`） |
| `5900749618991` | BA！ エナジーバー バナナ | シリアルバー (`K4`) | エナジーバー (`K3`) | an energy bar; the item table has a row for エナジーバー (now a brand name linked to its generic item, `K3`) / エナジーバー・品目の表に エナジーバー の行がある（銘柄 → 一般名＝`K3`） |
| `45365171` | コカ・コーラ ゼロ | コーラ (`K3`) | コーラ ゼロ (`K3`) | sugar-free cola; linked to the new item コーラ ゼロ, no longer counted with the sugar of regular cola / 糖類ゼロのコーラ・新しい品目 コーラ ゼロ へ（加糖のコーラの栄養で数えない） |
| `4890008109357` | コカ・コーラ ゼロ | コーラ (`K3`) | コーラ ゼロ (`K3`) | sugar-free cola; linked to the new item コーラ ゼロ, no longer counted with the sugar of regular cola / 糖類ゼロのコーラ・新しい品目 コーラ ゼロ へ（加糖のコーラの栄養で数えない） |
| `4890008109364` | コカ・コーラ ゼロ | コーラ (`K3`) | コーラ ゼロ (`K3`) | sugar-free cola; linked to the new item コーラ ゼロ, no longer counted with the sugar of regular cola / 糖類ゼロのコーラ・新しい品目 コーラ ゼロ へ（加糖のコーラの栄養で数えない） |
| `49004717` | コカ・コーラ ゼロ | コーラ (`K3`) | コーラ ゼロ (`K3`) | sugar-free cola; linked to the new item コーラ ゼロ, no longer counted with the sugar of regular cola / 糖類ゼロのコーラ・新しい品目 コーラ ゼロ へ（加糖のコーラの栄養で数えない） |
| `4902663010074` | ひかり味噌 いなり揚げ | いなり揚げ (`K1`) | 常温 味付けいなり (`K3`) | seasoned fried tofu for inari sushi sold at room temperature; linked to the new item 常温 味付けいなり (room temperature, 90 days) instead of the refrigerated item (60 days) / 常温で売る、いなり寿司用の味付け油揚げ・新しい品目 常温 味付けいなり（常温 90日）へ・冷蔵の品目（60日）から |
| `4902758300929` | おいなりさん 味付け油揚げ | 味付けいなり (`K3`) | 常温 味付けいなり (`K3`) | seasoned fried tofu for inari sushi sold at room temperature; linked to the new item 常温 味付けいなり (room temperature, 90 days) instead of the refrigerated item (60 days) / 常温で売る、いなり寿司用の味付け油揚げ・新しい品目 常温 味付けいなり（常温 90日）へ・冷蔵の品目（60日）から |
| `4903127131274` | ヤモト 味付いなり | 味付けいなり (`K1`) | 常温 味付けいなり (`K3`) | seasoned fried tofu for inari sushi sold at room temperature; linked to the new item 常温 味付けいなり (room temperature, 90 days) instead of the refrigerated item (60 days) / 常温で売る、いなり寿司用の味付け油揚げ・新しい品目 常温 味付けいなり（常温 90日）へ・冷蔵の品目（60日）から |
| `4903308012804` | チーズデザート ラムレーズン | 6Pチーズ (`K2`) | チーズデザート 個包装 (`K2`) | QBB チーズデザート, individually wrapped flavored processed cheese; linked to the new item チーズデザート 個包装, not counted as cheese in recipes / QBB チーズデザート（個包装の味つきプロセスチーズ）・新しい品目 チーズデザート 個包装 へ（レシピではチーズに数えない） |
| `4903308012811` | チーズデザート リッチナッツ | 6Pチーズ (`K2`) | チーズデザート 個包装 (`K2`) | QBB チーズデザート, individually wrapped flavored processed cheese; linked to the new item チーズデザート 個包装, not counted as cheese in recipes / QBB チーズデザート（個包装の味つきプロセスチーズ）・新しい品目 チーズデザート 個包装 へ（レシピではチーズに数えない） |
| `4903308012941` | チーズデザート リッチベリー | 6Pチーズ (`K2`) | チーズデザート 個包装 (`K2`) | QBB チーズデザート, individually wrapped flavored processed cheese; linked to the new item チーズデザート 個包装, not counted as cheese in recipes / QBB チーズデザート（個包装の味つきプロセスチーズ）・新しい品目 チーズデザート 個包装 へ（レシピではチーズに数えない） |
| `4903308035605` | チーズデザートブラッドオレンジ | 6Pチーズ (`K2`) | チーズデザート 個包装 (`K2`) | QBB チーズデザート, individually wrapped flavored processed cheese; linked to the new item チーズデザート 個包装, not counted as cheese in recipes / QBB チーズデザート（個包装の味つきプロセスチーズ）・新しい品目 チーズデザート 個包装 へ（レシピではチーズに数えない） |
| `4903308014136` | チーズデザート 栗 | チーズデザート (`K2`) | チーズデザート 個包装 (`K2`) | the same QBB チーズデザート series (individually wrapped processed cheese, refrigerated, 180 days on the maker's label); it had been linked to the cup dessert item (5 days) / 同じ QBB チーズデザート のシリーズ（個包装のプロセスチーズ・メーカーの表示は要冷蔵 180日）・カップのデザートの品目（5日）につないでいた |
| `4540118002726` | 雪塩ちんすこう | クッキー (`K3`) | ちんすこう (`K3`) | salted chinsuko (Okinawan baked sweet); linked to ちんすこう (14 days, the shortest on the makers' pages) instead of クッキー (180 days) / 沖縄の焼き菓子 ちんすこう・ちんすこう（14日＝メーカーの表示のいちばん短い側）へ・クッキー（180日）から |
| `4582350791150` | 塩ちんすこう | クッキー (`K3`) | ちんすこう (`K3`) | salted chinsuko (Okinawan baked sweet); linked to ちんすこう (14 days, the shortest on the makers' pages) instead of クッキー (180 days) / 沖縄の焼き菓子 ちんすこう・ちんすこう（14日＝メーカーの表示のいちばん短い側）へ・クッキー（180日）から |
| `4901201225901` | 緑茶 クランベリー | 緑茶 (`K2`) | フレーバー緑茶 (`K2`) | a green tea drink with fruit flavor; linked to the new item フレーバー緑茶, not counted as plain green tea in recipes / 果物の香りの緑茶の飲み物・新しい品目 フレーバー緑茶 へ（レシピでは素の緑茶に数えない） |
| `6942656601269` | 達利園 青梅緑茶 | 緑茶 (`K3`) | フレーバー緑茶 (`K3`) | a green tea drink with fruit flavor; linked to the new item フレーバー緑茶, not counted as plain green tea in recipes / 果物の香りの緑茶の飲み物・新しい品目 フレーバー緑茶 へ（レシピでは素の緑茶に数えない） |
| `4790016160602` | レ サヴール ド セイロン ミント入り緑茶 | 緑茶 (`K3`) | 茶葉 (`K3`) | green tea leaves with mint, not a bottled drink; linked to 茶葉 (tea leaves) / ミント入りの緑茶の茶葉（ペットの飲み物ではない）・茶葉 へ |
| `4978576284932` | 味付けするめ | あたりめ (`K2`) | さきいか (`K2`) | seasoned dried squid; linked to さきいか (seasoned shredded squid) instead of あたりめ (plain dried squid), not counted as plain dried squid in recipes / 味付けのするめ・あたりめ（素のするめ）ではなく さきいか へ（レシピでは素のするめに数えない） |
| `4967105333364` | ゆず味ごま | ごま (`K2`) | ごま塩 (`K2`) | flavored sesame seeds; linked to ごま塩 (seasoned sesame used like furikake, 180 days) instead of plain sesame (150 days), not counted as plain sesame in recipes / 味つきのごま・素のごま（150日）ではなく ごま塩（ふりかけのように使う味つきのごま・180日）へ（レシピでは素のごまに数えない） |
| `4967105333388` | キムチ味ごま | ごま (`K2`) | ごま塩 (`K2`) | flavored sesame seeds; linked to ごま塩 (seasoned sesame used like furikake, 180 days) instead of plain sesame (150 days), not counted as plain sesame in recipes / 味つきのごま・素のごま（150日）ではなく ごま塩（ふりかけのように使う味つきのごま・180日）へ（レシピでは素のごまに数えない） |
| `4967105333401` | 梅味ごま | ごま (`K2`) | ごま塩 (`K2`) | flavored sesame seeds; linked to ごま塩 (seasoned sesame used like furikake, 180 days) instead of plain sesame (150 days), not counted as plain sesame in recipes / 味つきのごま・素のごま（150日）ではなく ごま塩（ふりかけのように使う味つきのごま・180日）へ（レシピでは素のごまに数えない） |
| `4967105333418` | しょうゆ味ごま | ごま (`K2`) | ごま塩 (`K2`) | flavored sesame seeds; linked to ごま塩 (seasoned sesame used like furikake, 180 days) instead of plain sesame (150 days), not counted as plain sesame in recipes / 味つきのごま・素のごま（150日）ではなく ごま塩（ふりかけのように使う味つきのごま・180日）へ（レシピでは素のごまに数えない） |
| `4967105333432` | わさび味ごま | ごま (`K2`) | ごま塩 (`K2`) | flavored sesame seeds; linked to ごま塩 (seasoned sesame used like furikake, 180 days) instead of plain sesame (150 days), not counted as plain sesame in recipes / 味つきのごま・素のごま（150日）ではなく ごま塩（ふりかけのように使う味つきのごま・180日）へ（レシピでは素のごまに数えない） |
| `4932503055484` | 北海道産小麦のスポンジケーキミックス | ホットケーキミックス (`K3`) | ブラウニーミックス (`K3`) | a sponge cake or muffin mix, not pancake mix; linked to ブラウニーミックス (another cake mix, 180 days) instead of ホットケーキミックス (365 days), not counted as pancake mix in recipes / スポンジケーキ・マフィンのミックス（ホットケーキミックスではない）・ブラウニーミックス（ケーキのミックス・180日）へ・ホットケーキミックス（365日）から（レシピではホットケーキミックスに数えない） |
| `4936366433335` | クイックマフィンミックス チョコチップ | ホットケーキミックス (`K3`) | ブラウニーミックス (`K3`) | a sponge cake or muffin mix, not pancake mix; linked to ブラウニーミックス (another cake mix, 180 days) instead of ホットケーキミックス (365 days), not counted as pancake mix in recipes / スポンジケーキ・マフィンのミックス（ホットケーキミックスではない）・ブラウニーミックス（ケーキのミックス・180日）へ・ホットケーキミックス（365日）から（レシピではホットケーキミックスに数えない） |
| `4902521008557` | オレイダ グリーンピース | 冷凍野菜 (`K3`) | 冷凍グリンピース (`K3`) | frozen green peas; linked to 冷凍グリンピース instead of the general 冷凍野菜, counted as green peas (not mixed vegetables) in recipes / 冷凍のグリンピース・広い 冷凍野菜 ではなく 冷凍グリンピース へ（レシピではミックスベジタブルでなくグリーンピースに数える） |

味付けさば缶, 味付けツナ缶, 加糖ヨーグルト, コーラ ゼロ, 常温 味付けいなり, チーズデザート 個包装, フレーバー緑茶 are generic item names written by us, not OFF names; they were added to the item table in the app published on 2026-11-18. Their estimated shelf life and storage are those of the items they were split from, except 常温 味付けいなり (room temperature, 90 days, from makers' and retailers' pages in Japan) and チーズデザート 個包装 (refrigerated, 180 days, from the maker's label). Their nutrition values are those of the items they were split from, except 加糖ヨーグルト (the Standard Tables of Food Composition in Japan: yogurt, skimmed milk, sweetened) and コーラ ゼロ (the maker's label). None of these values come from Open Food Facts.

味付けさば缶・味付けツナ缶・加糖ヨーグルト・コーラ ゼロ・常温 味付けいなり・チーズデザート 個包装・フレーバー緑茶 は、OFF の名前ではなく、私たちが書いた一般名です。2026-11-18 に公開したアプリの品目の表に足しました。期限の目安と置き場所は、分ける前の品目と同じです。ただし 常温 味付けいなり（常温 90日＝日本のメーカー・小売のページ）と チーズデザート 個包装（要冷蔵 180日＝メーカーの表示）は別に決めました。栄養の値も分ける前の品目と同じです。ただし 加糖ヨーグルト（日本食品標準成分表のヨーグルト 脱脂加糖）と コーラ ゼロ（メーカーの表示）は別です。どの値も Open Food Facts には拠っていません。

## Changes to links / つなぎの直し（2026-11-16）

On 2026-11-16, 2 rows were removed from `off-alias.csv`; no row was added or linked to a different item. This brings the table in line with the app as published on 2026-11-16.

- Removed: キーマカレー (keema curry) and グリーンカレー (green curry) had been linked to レトルトカレー (curry in a retort pouch). On 2026-11-16, the app added these two plain dish names to its item table as ready-made dishes from the deli counter (refrigerated, 3 days), because in Japanese supermarkets a plain dish name most often means a deli dish. The item table now answers these names directly, so the links were removed. For these two products the estimated shelf life becomes shorter (365 days at room temperature → 3 days refrigerated).

2026-11-16 に、`off-alias.csv` の 2 行を外しました（足した行・つなぐ先を替えた行はありません）。2026-11-16 に公開したアプリに、この表を合わせました。

- 外した行：キーマカレー と グリーンカレー は、レトルトカレー につないでいました。2026-11-16 に、アプリの品目の表に、この2つの素の料理名を惣菜の品目（冷蔵 3日）として足しました＝スーパーでは、素の料理名は惣菜のことが多いためです。品目の表がこの名前に直接答えるので、つなぎを外しました。この2つの品の期限の目安は短くなります（常温 365日 → 冷蔵 3日）。

| `code` | `name_ja` | Before / 前 | After / 後 | Why / 理由 |
|---|---|---|---|---|
| `4902688242795` | グリーンカレー | レトルトカレー (`K3`) | — (removed / 外した) | the item table now has グリーンカレー as a deli dish / 品目の表に惣菜の グリーンカレー を足した |
| `4902688242818` | キーマカレー | レトルトカレー (`K3`) | — (removed / 外した) | the item table now has キーマカレー as a deli dish / 品目の表に惣菜の キーマカレー を足した |

## Changes to links / つなぎの直し（2026-11-10）

On 2026-11-10, 15 rows were removed from `off-alias.csv` and 16 rows were linked to a different item; no row was added. This brings the table in line with the app as published on 2026-11-09 and 2026-11-10; the app changes of 2026-11-07 and 2026-11-08 had not been reflected here yet.

- Removed: the app now ignores a word at the start of a name such as 有機 (organic), 特選 (premium selection) or 外国産 (imported) and looks up the rest of the name in the item table. These names now reach the same item as their link without this table (有機プーアル茶 reaches プーアル茶, a new item with the same estimated shelf life and storage as 茶葉), so the links were removed. The estimated shelf life and storage for these products do not change.
- Linked to a different item: 白味噌 (white miso: a sweet, light-colored rice miso) and カンパーニュ (pain de campagne) were added to the item table on 2026-11-10. White miso products, which had been linked to 味噌 (miso), are now linked to 白味噌; their estimated shelf life becomes shorter (unopened at room temperature: 270 days → 180 days), and their nutrition values become those of sweet rice miso. パン・ド・カンパーニュ, which had been linked to パン (bread, 4 days), is now linked to カンパーニュ (2 days). ひかり味噌 信州白味噌 stays linked to 味噌, because 信州 (Shinshu) miso is the light-colored salty type that 味噌 stands for in the item table.

2026-11-10 に、`off-alias.csv` の 15 行を外し、16 行のつなぐ先を替えました（足した行はありません）。2026-11-09 と 2026-11-10 に公開したアプリに、この表を合わせました。2026-11-07 と 2026-11-08 のアプリの直しを、ここにまだ写していませんでした。

- 外した行：アプリが、名前の頭の 有機・特選・外国産 などの語を外して、残りの名前で品目の表を引くようになりました。この名前は、この表が無くても、つないでいた品目と同じ品目に当たります（有機プーアル茶 は、期限の目安と置き場所が 茶葉 と同じ新しい品目 プーアル茶 に当たります）。そこでつなぎを外しました。この品の期限の目安と置き場所は変わりません。
- つなぐ先を替えた行：2026-11-10 に、品目の表に 白味噌（甘みそ）と カンパーニュ を足しました。味噌 につないでいた白みその品は 白味噌 につなぎ直し、期限の目安が短くなります（開ける前・常温 270日 → 180日）。栄養の値は甘みその値になります。パン（4日）につないでいた パン・ド・カンパーニュ は カンパーニュ（2日）につなぎ直しました。ひかり味噌 信州白味噌 は 味噌 のままです＝品目の表の 味噌 は、信州みそ型の淡色の辛口みそを表すためです。

| `code` | `name_ja` | Before / 前 | After / 後 | Why / 理由 |
|---|---|---|---|---|
| `4531711001667` | 有機プーアル茶 | 茶葉 (`K4`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds プーアル茶, an item added on 2026-11-08 with the same estimated shelf life and storage as 茶葉 / 頭の 有機 を外して、11/8 に足した品目 プーアル茶 を直接引く（期限の目安と置き場所は 茶葉 と同じ） |
| `4543361964292` | 有機緑茶 | 緑茶 (`K3`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 緑茶 directly / 頭の 有機 を外して 緑茶 を直接引く |
| `4901027661464` | 有機緑茶 | 緑茶 (`K3`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 緑茶 directly / 頭の 有機 を外して 緑茶 を直接引く |
| `45648465` | 有機たまご | 卵 (`K2`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 卵 directly / 頭の 有機 を外して 卵 を直接引く |
| `4902713128421` | 有機みそ | 味噌 (`K2`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 味噌 directly / 頭の 有機 を外して 味噌 を直接引く |
| `4902856434052` | 特選 白だし | 白だし (`K2`) | — (removed / 外した) | the app ignores 特選 (premium selection) at the start and finds 白だし directly / 頭の 特選 を外して 白だし を直接引く |
| `4903016041561` | 有機きなこ | きなこ (`K2`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds きなこ directly / 頭の 有機 を外して きなこ を直接引く |
| `4909891100485` | 外国産 有機納豆 | 納豆 (`K2`) | — (removed / 外した) | the app ignores 外国産 (imported) and 有機 (organic) at the start and finds 納豆 directly / 頭の 外国産 と 有機 を外して 納豆 を直接引く |
| `4912345678911` | 有機にんじん | にんじん (`K2`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds にんじん directly / 頭の 有機 を外して にんじん を直接引く |
| `4958325003304` | 有機抹茶 | 抹茶 (`K2`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 抹茶 directly / 頭の 有機 を外して 抹茶 を直接引く |
| `4978784206979` | 有機抹茶 | 抹茶 (`K2`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 抹茶 directly / 頭の 有機 を外して 抹茶 を直接引く |
| `4977858011365` | 有機減塩みそ | 味噌 (`K2`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 減塩みそ (low-salt miso), now another name of 味噌, directly / 頭の 有機 を外して、味噌 の別名に足した 減塩みそ を直接引く |
| `4978784600234` | 有機玄米茶 | 玄米茶 (`K3`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 玄米茶 directly / 頭の 有機 を外して 玄米茶 を直接引く |
| `4985810101108` | 有機煎茶 | 煎茶 (`K3`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds 煎茶 directly / 頭の 有機 を外して 煎茶 を直接引く |
| `8009004809963` | 有機トマトソース | トマトソース (`K2`) | — (removed / 外した) | the app ignores 有機 (organic) at the start and finds トマトソース directly / 頭の 有機 を外して トマトソース を直接引く |
| `4901614003622` | 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4902703011702` | 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4904035402005` | 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4904783000683` | 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4978045600683` | 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4978094999219` | 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4902663012733` | ひかり味噌 有機白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4902663015567` | ひかり味噌 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4902663014676` | ひかり味噌 西京みそ | 味噌 (`K2`) | 白味噌 (`K2`) | 西京みそ is a white miso; 白味噌 was added to the item table on 2026-11-10 / 西京みそ は白みその一つ・11/10 に足した 白味噌 へ |
| `4902401145044` | ハナマルキ 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4901614501753` | プレミアム 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4902713128902` | マルコメ 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4958325000389` | ムソー 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4979369007622` | ヤマゲン 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4979369123520` | ヤマゲン 白みそ | 味噌 (`K2`) | 白味噌 (`K2`) | a white miso; 白味噌 (sweet, light-colored rice miso) was added to the item table on 2026-11-10 / 白みそ・11/10 に品目の表に足した 白味噌（甘みそ）へ |
| `4955638436577` | パン・ド・カンパーニュ | パン (`K3`) | カンパーニュ (`K3`) | pain de campagne; カンパーニュ was added to the item table on 2026-11-10 / パン・ド・カンパーニュ・11/10 に品目の表に足した カンパーニュ へ |

白味噌 and カンパーニュ are generic item names written by us, not OFF names. Their nutrition values come from the Standard Tables of Food Composition in Japan (白味噌: rice miso, sweet type; カンパーニュ: French bread, as there is no row for pain de campagne), and their estimated shelf life from makers' and retailers' pages in Japan, not from Open Food Facts.

白味噌 と カンパーニュ は、OFF の名前ではなく、私たちが書いた一般名です。栄養の値は日本食品標準成分表（白味噌＝米みそ 甘みそ・カンパーニュ＝カンパーニュの行が無いため フランスパン）、期限の目安は日本のメーカー・小売のページで決め、Open Food Facts には拠っていません。

## Changes to links / つなぎの直し（2026-11-05）

On 2026-11-05, 1 row in `off-alias.csv` was linked to a different item; no row was added or removed. 鉄分のむヨーグルト プルーンFe is a drinking yogurt with added iron and folic acid, but it was linked to ordinary drinking yogurt, which has far less iron. It is now linked to a new item for this product. The estimated shelf life and storage do not change; only the nutrition values change.

2026-11-05 に、`off-alias.csv` の 1 行のつなぐ先を替えました（足した行・外した行はありません）。鉄分のむヨーグルト プルーンFe は鉄と葉酸を足した飲むヨーグルトなのに、鉄がずっと少ない普通の飲むヨーグルトにつないでいました。この品のための新しい品目につなぎ替えました。期限の目安と置き場所は変わらず、変わるのは栄養の値だけです。

| `code` | `name_ja` | Before / 前 | After / 後 | Why / 理由 |
|---|---|---|---|---|
| `4908011621633` | 鉄分のむヨーグルト プルーンFe | 飲むヨーグルト (`K2`) | プルーンFe 1日分の鉄分 のむヨーグルト (`K2`) | a drinking yogurt with added iron and folic acid; ordinary drinking yogurt has far less iron / 鉄と葉酸を足した飲むヨーグルト・普通の飲むヨーグルトは鉄がずっと少ない |

プルーンFe 1日分の鉄分 のむヨーグルト was added to the item table in this review. It is named after the product as shown on the maker's page (Megmilk Snow Brand), not after the OFF name. Its values are the maker's nutrition facts per bottle (190 g) on that page; for nutrients the maker does not show, the values of drinking yogurt (sweetened) from the Standard Tables of Food Composition in Japan are used. No value was taken from Open Food Facts. That this barcode is this product (the regular 190 g bottle, not a reduced-sugar one) was checked with the brand and quantity registered in Open Food Facts.

プルーンFe 1日分の鉄分 のむヨーグルト は、この見直しで品目の表に足した品目です。名前は OFF の名前ではなく、メーカー（雪印メグミルク）のページの商品名です。値はそのページの 1 本（190g）あたりの栄養成分表示で、メーカーが示していない成分は、日本食品標準成分表の ヨーグルト ドリンクタイプ 加糖 の値を使いました。Open Food Facts の値は使っていません。このバーコードの品がこの商品（通常の 190g の品・糖質を減らした品ではない）であることは、Open Food Facts の登録の銘柄と量で確かめました。

## Changes to links / つなぎの直し（2026-11-04）

On 2026-11-04, 1 row was removed from `off-alias.csv` and 1 row was linked to a different item; no row was added. The app now ignores a word at the end or the start of a name saying that no sugar is used or that sugar is reduced (for example 砂糖不使用 or 砂糖控えめ) and looks up the rest of the name in the item table. アーモンド効果 砂糖不使用 now reaches the same item as its link without this table, so the link was removed. エールマン ハイプロテイン プレーン is a high-protein plain yogurt, but it was linked to plain yogurt, which has much less protein; it is now linked to a new generic item for high-protein yogurt. The estimated shelf life and storage for these two products do not change.

2026-11-04 に、`off-alias.csv` の 1 行を外し、1 行のつなぐ先を替えました（足した行はありません）。アプリが、名前の最後か頭にある、砂糖を使っていない・控えた旨の語（砂糖不使用・砂糖控えめ など）を外して、残りの名前で品目の表を引くようになりました。アーモンド効果 砂糖不使用 は、この表が無くても、つないでいた品目と同じ品目に当たります。そこでつなぎを外しました。エールマン ハイプロテイン プレーン は高たんぱくのプレーンヨーグルトなのに、たんぱく質がずっと少ない普通のプレーンヨーグルトにつないでいました。高たんぱくのヨーグルトの新しい一般名の品目につなぎ替えました。この2つの品の期限の目安と置き場所は変わりません。

| `code` | `name_ja` | Before / 前 | After / 後 | Why / 理由 |
|---|---|---|---|---|
| `4971666488630` | アーモンド効果 砂糖不使用 | アーモンド効果 (`K2`) | — (removed / 外した) | the app ignores 砂糖不使用 (no sugar used) at the end and finds アーモンド効果 directly / 最後の 砂糖不使用 を外して アーモンド効果 を直接引く |
| `4902971381804` | エールマン ハイプロテイン プレーン | プレーンヨーグルト (`K3`) | 高たんぱくヨーグルト (`K3`) | a high-protein plain yogurt; plain yogurt has much less protein / 高たんぱくのプレーンヨーグルト・普通のプレーンヨーグルトはたんぱく質がずっと少ない |

高たんぱくヨーグルト was added to the item table in this review. Its values were estimated with AI assistance from the Standard Tables of Food Composition in Japan (non-fat plain yogurt) and, for calcium, a maker's value for a similar yogurt. Only the protein value (9.3 g per 100 g) was taken from the nutrition facts of this product as registered in Open Food Facts, because no maker's value was found; it is within the range of the makers' values for similar yogurts sold in Japan.

高たんぱくヨーグルト は、この見直しで品目の表に足した品目です。値は、日本食品標準成分表（ヨーグルト 無脂肪無糖）と、カルシウムは同じ形のヨーグルトのメーカーの値を元に、AI を使って推定しました。メーカーの値が見つからなかったため、たんぱく質（100g あたり 9.3g）だけは、この品の Open Food Facts の登録の栄養成分表示の値を使いました（日本で売られている同じ形のヨーグルトのメーカーの値の範囲の中です）。

## Changes to links / つなぎの直し（2026-11-03）

On 2026-11-03, 3 rows were removed from `off-alias.csv`; no row was added or linked to a different item. The app now ignores a place of origin written in brackets or at the start of a name (for example （イタリア産） or フランス産) and looks up the rest of the name in the item table. These names now reach the same item as their link without this table, so the links were removed. The estimated shelf life and storage for these products do not change.

2026-11-03 に、`off-alias.csv` の 3 行を外しました（足した行・つなぐ先を替えた行はありません）。アプリが、括弧の中や名前の頭の産地（（イタリア産）・フランス産 など）を外して、残りの名前で品目の表を引くようになりました。この名前は、この表が無くても、つないでいた品目と同じ品目に当たります。そこでつなぎを外しました。この品の期限の目安と置き場所は変わりません。

| `code` | `name_ja` | Before / 前 | After / 後 | Why / 理由 |
|---|---|---|---|---|
| `4560148204211` | フィリピン産ドライマンゴー | ドライマンゴー (`K2`) | — (removed / 外した) | the app ignores フィリピン産 (from the Philippines) at the start and finds ドライマンゴー directly / 頭の フィリピン産を外して ドライマンゴー を直接引く |
| `4568123563544` | ぶどう（イタリア産） | ぶどう (`K2`) | — (removed / 外した) | the app ignores （イタリア産） (from Italy) in brackets and finds ぶどう directly / 括弧の（イタリア産）を外して ぶどう を直接引く |
| `4902033803725` | フランス産ブリーチーズ | ブリーチーズ (`K2`) | — (removed / 外した) | the app ignores フランス産 (from France) at the start and finds ブリーチーズ directly / 頭の フランス産を外して ブリーチーズ を直接引く |

## Changes to links / つなぎの直し（2026-11-02）

On 2026-11-02, after a review of the item table, 11 rows were removed from `off-alias.csv` and 7 rows (4 names) were linked to a different item; no row was added. The app applies a link to every product with the same name, not only to the barcode in this table. So a link was removed when the name alone does not tell the form of the product (for example, dried or chilled noodles, or fresh apples or juice) and the link chose a much longer estimated shelf life than the common form in Japanese supermarkets. A link was changed when the maker's or a retailer's page showed that the product is a different kind of item.

2026-11-02 に、品目の表の見直しで、`off-alias.csv` の 11 行を外し、7 行（名前 4）のつなぐ先を替えました（足した行はありません）。アプリは、この表のバーコードの品だけでなく、同じ名前の品すべてにつなぎを使います。そこで、名前だけでは品の形（乾麺かチルドか・生のりんごかジュースか など）が決まらず、つなぐと日本のスーパーで多い形より期限の目安がずっと長くなる名前は、つなぎを外しました。つなぐ先を替えたのは、メーカーや小売のページで別の種類の品と分かった行です。

| `code` | `name_ja` | Before / 前 | After / 後 | Why / 理由 |
|---|---|---|---|---|
| `4529329000042` | 桜うどん | うどん 乾麺 (`K3`) | — (removed / 外した) | the name does not tell dried from chilled or boiled noodles / 乾麺かチルド・ゆでか名前で決まらない |
| `4901166008519` | 讃岐うどん | うどん 乾麺 (`K3`) | — (removed / 外した) | the name does not tell dried from chilled or boiled noodles / 乾麺かチルド・ゆでか名前で決まらない |
| `4901726014875` | 田舎うどん | うどん 乾麺 (`K3`) | — (removed / 外した) | the name does not tell dried from chilled or boiled noodles / 乾麺かチルド・ゆでか名前で決まらない |
| `4902110393279` | 信州そば | 乾麺 そば (`K3`) | — (removed / 外した) | the name does not tell dried from chilled or boiled noodles / 乾麺かチルド・ゆでか名前で決まらない |
| `4902663009535` | 天ぷらうどん | インスタント麺 (`K3`) | — (removed / 外した) | the name does not tell a ready meal or chilled noodles from a cup noodle / 惣菜・チルドの麺かカップ麺か名前で決まらない |
| `4904977033251` | しいたけ豆 | 豆の缶詰 (`K4`) | — (removed / 外した) | Japanese 〜豆 products are usually simmered beans, not canned beans / 国内の「〜豆」は多くが煮豆（缶詰ではない） |
| `4908209500290` | 青森りんご | りんごジュース (`K3`) | — (removed / 外した) | on receipts, usually fresh apples, not juice / レシートでは多くが生のりんご（ジュースではない） |
| `4942355154556` | きのこパスタ | パスタ 500g (`K2`) | — (removed / 外した) | in supermarkets, a ready or frozen dish, not dried pasta / スーパーでは惣菜か冷凍の料理（乾燥パスタではない） |
| `4942355602170` | えびピラフ | 冷凍ピラフ (`K3`) | — (removed / 外した) | without 冷凍 (frozen) in the name, usually a ready meal / 名前に「冷凍」が無ければ多くは惣菜 |
| `4970083030026` | 蔵王高原そば | 乾麺 そば (`K3`) | — (removed / 外した) | the name does not tell dried from chilled or boiled noodles / 乾麺かチルド・ゆでか名前で決まらない |
| `4972328630459` | 全粒そば | 乾麺 そば (`K3`) | — (removed / 外した) | the name does not tell dried from chilled or boiled noodles / 乾麺かチルド・ゆでか名前で決まらない |
| `4901005122475` | グリコ ペロティ | 飴 (`K3`) | 棒付きチョコ (`K3`) | a chocolate on a stick (maker's page), not a candy / 棒付きのチョコレート（メーカーのページ）・飴ではない |
| `4901111201668` | スティック 紅茶オレ 華やかロイヤルミルクティー | ブレンディ スティック (`K2`) | 粉末ミルクティー (`K3`) | a milk tea powder (maker's page), not a coffee stick / ミルクティーの粉（メーカーのページ）・コーヒーのスティックではない |
| `4909411076658` | キリン ミルクティー | 午後の紅茶 (`K2`) | 午後の紅茶 ミルクティー (`K2`) | milk tea with milk and sugar; the item before was plain tea / 乳と砂糖の入ったミルクティー・前の品目は紅茶だけ |
| `4909411076870` | キリン ミルクティー | 午後の紅茶 (`K2`) | 午後の紅茶 ミルクティー (`K2`) | milk tea with milk and sugar; the item before was plain tea / 乳と砂糖の入ったミルクティー・前の品目は紅茶だけ |
| `4909411084950` | キリン ミルクティー | 午後の紅茶 (`K2`) | 午後の紅茶 ミルクティー (`K2`) | milk tea with milk and sugar; the item before was plain tea / 乳と砂糖の入ったミルクティー・前の品目は紅茶だけ |
| `4909411086299` | キリン ミルクティー | 午後の紅茶 (`K2`) | 午後の紅茶 ミルクティー (`K2`) | milk tea with milk and sugar; the item before was plain tea / 乳と砂糖の入ったミルクティー・前の品目は紅茶だけ |
| `6901721497459` | マクスウェルハウス ミルクコーヒー | インスタントコーヒー (`K3`) | スティックコーヒー (`K3`) | a 3-in-1 stick (coffee, creamer and sugar), not black instant coffee / コーヒー・クリーマー・砂糖のスティック・ブラックの粉ではない |

棒付きチョコ and 午後の紅茶 ミルクティー were added to the item table in this review. As with `K4`, their values were decided from maker pages and the Standard Tables of Food Composition in Japan, not from Open Food Facts.

棒付きチョコ と 午後の紅茶 ミルクティー は、この見直しで品目の表に足した品目です。`K4` と同じく、値はメーカーのページと日本食品標準成分表で決め、Open Food Facts には拠っていません。

## Corrections / 直した行（2026-10-30）

| `code` | OFF name | Correction |
|---|---|---|
| `4901085003800` | 読み込んでいます… (a page-loading message, not a product name) | `name_ja` = お～いお茶 緑茶 (checked on two retailer pages) |
| `4902201412292` | Nestle, instant coffee | `name_ja` = ネスレ ふわラテ 宇治抹茶 (the OFF photo and ingredients) |
| `49840322` | 読み込んでいます… (no other name, brand or category) | Removed |

OFF の名前が品の名前でない行・別の品の名前の行を、小売のページと OFF の写真で確かめて、手で直しました。直した行の `name_ja_source` は `ai`、`name_orig` は OFF の主の言語の名前です。

## Counts / 件数（2026-10-27・2026-10-30 に直した行を反映）

| | Rows |
|---|---:|
| Products selected (valid code) / 抜き出した品 | 6,113 |
| Not included: restricted-circulation codes `040`–`049` / 店内コード（040〜049） | 14 |
| Not included: former Soviet Union / Baltic prefixes / 旧ソ連・バルト三国の頭 | 775 |
| `name_ja_source = off` (Japanese name in OFF) | 533 |
| `name_ja_source = ai` (translated, including 2 corrected rows) | 3,907 |
| Not included: translation left empty or rejected / 訳が空・検査で外れた | 329 |
| Not included: no name in any language / 名前がまったく無い | 554 |
| Not included: removed by correction / 直して外した | 1 |
| **Rows in `off-jp-names.csv`** | **4,440** |

## Updating / 更新の仕方

The table is a snapshot. To update it, repeat the steps above on a newer revision of `food.parquet`: keep the rows whose OFF Japanese name is unchanged, take new Japanese names from OFF as `off`, and translate only new products that still have no Japanese name. Each update is published as a new commit with the revision in the commit message.

この表はある日の写しです。更新するときは、新しい版の `food.parquet` で上の手順をやり直し、OFF に日本語の名前が増えた品は `off` に替え、まだ日本語の名前が無い新しい品だけを訳します。

## Contact / 連絡先

support@digicle.net
