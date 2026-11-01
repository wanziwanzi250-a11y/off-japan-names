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
| `link_type = K1` | 381 |
| `link_type = K2` | 1294 |
| `link_type = K3` | 1213 |
| `link_type = K4` | 295 |
| **Rows in `off-alias.csv`** (2856 names) | **3183** |

The links were added in batches: batch 1 on 2026-10-31 (848 rows), batches 2–4 on 2026-11-01 (all the remaining links from the sorting), batch 5 on 2026-11-01 (285 rows, mostly `K4`), and batch 6 on 2026-11-01 (26 rows for 25 names, `K3` 1, `K4` 25: names whose links had been left for a decision). / つなぎは何回かに分けて足しました（2026-10-31 に1回目の 848 行・2026-11-01 に2〜4回目＝仕分けたつなぎの残り全部・2026-11-01 に5回目の 285 行＝主に `K4`・2026-11-01 に6回目の 26 行（名前 25・`K3` 1・`K4` 25）＝つなぐかどうかを決めかねていた名前）。

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
