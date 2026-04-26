# P-Collector Card DB

Public hot-update feed for the **[P-Collector](https://apps.apple.com/app/) iOS / Android app**.

This repository hosts the JSON catalog and manifest that the app downloads at
runtime to refresh its local card database **without** going through the App
Store / Play Store update channel.

## Files

| Path | Description |
| --- | --- |
| `card_db/card_db_manifest.json` | Tiny manifest (version, content hash, sha256) — checked first by the client |
| `card_db/card_db_en.json` | English Pokémon TCG card metadata (built from [TCGdex/cards-database](https://github.com/tcgdex/cards-database)) |
| `card_db/card_db_jp.json` | Japanese card metadata (manual curation + TCGplayer/PokemonPriceTracker image URLs) |

## CDN URLs (consumed by the app)

```
https://cdn.jsdelivr.net/gh/posmap-japan/p-collector-card-db@main/card_db/card_db_manifest.json
https://cdn.jsdelivr.net/gh/posmap-japan/p-collector-card-db@main/card_db/card_db_en.json
https://cdn.jsdelivr.net/gh/posmap-japan/p-collector-card-db@main/card_db/card_db_jp.json
```

The app fetches the manifest first, compares `version` / `content_hash` against
its local copy, and downloads the larger JSON files only when an update is
available.

## How updates are produced

The catalog is regenerated daily by the
[`update-card-db` workflow](https://github.com/posmap-japan/P-Collector/actions/workflows/update-card-db.yml)
in the private **P-Collector** application repository. The workflow:

1. Pulls the latest [tcgdex/cards-database](https://github.com/tcgdex/cards-database) release.
2. Rebuilds `card_db_en.json` via `scripts/build_en_cards_from_tcgdex_repo.py`.
3. Runs `scripts/ci_safety_check.py` (rejects malformed / drastically-shrunken catalogs).
4. Pushes the resulting JSON + manifest to **this repo** via SSH deploy key.
5. Purges the jsDelivr cache so users start receiving the new manifest immediately.

## Data sources & licenses

- **English card data**: derived from [TCGdex](https://tcgdex.dev/) under their
  CC-BY-NC license. Card images are hot-linked from the TCGdex CDN with the
  attribution requested by the project. Not affiliated with The Pokémon Company.
- **Japanese card data**: manually curated metadata + image URLs sourced from
  TCGplayer's CDN and PokemonPriceTracker indexing. We do **not** scrape
  `pokemon-card.com` and do not redistribute any first-party imagery.
- **Pokémon, Pokémon TCG, and all related marks** are trademarks of
  Nintendo / Creatures Inc. / GAME FREAK inc. This project is fan-made and not
  endorsed by The Pokémon Company.

## License

Scripts and the JSON schema are released under the **MIT License** (see
`LICENSE`). The card data itself is governed by the upstream license of each
source listed above.

## Issues

This repository is intentionally generated — please file feature requests and
bug reports against the application repository, not here.
