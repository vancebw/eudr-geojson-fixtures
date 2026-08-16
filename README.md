# EUDR GeoJSON Test Fixtures

Open test fixtures for validating **EUDR geolocation files** — the plot data that operators and traders must collect under Regulation (EU) 2023/1115 ([EUDR](https://eur-lex.europa.eu/eli/reg/2023/1115/oj), Article 9).

Use these files to test EUDR plot validators, KML/CSV converters, onboarding flows, or unit tests without hunting for real (privacy-sensitive) supplier data. Every coordinate is **synthetic** — the plots do not exist.

## The rules these fixtures exercise

- Every production plot needs **latitude and longitude in decimal degrees with at least six decimal places**.
- Plots **up to 4 hectares** may use a **single point**; plots **above 4 hectares** require a **polygon** outlining the boundary. Cattle establishments use a point.
- **One geometry per plot**, with a stable identifier (here: `plot_id`); duplicate or overlapping boundaries are review findings.
- GeoJSON (RFC 7946) stores coordinates in **longitude-before-latitude** order on WGS84 — the most common real-world error is a silent axis swap.

## Fixture index

| File | Scenario | Expected finding |
| --- | --- | --- |
| `fixtures/valid/point-under-4ha.geojson` | Coffee plot ≤ 4 ha as a single six-decimal point | Passes point-plot checks |
| `fixtures/valid/polygon-over-4ha.geojson` | Cocoa plot ≈ 25 ha polygon, closed ring | Passes polygon checks |
| `fixtures/valid/multipolygon-farm.geojson` | Two-part farm as a MultiPolygon | Passes with MultiPolygon support |
| `fixtures/invalid/reversed-lat-lon.geojson` | Latitude/longitude swapped (both values in range, wrong country) | Coordinate order conflict / country mismatch |
| `fixtures/invalid/latitude-out-of-range.geojson` | Latitude `200.123456` exceeds ±90 | Coordinate out of range |
| `fixtures/invalid/self-intersecting-polygon.geojson` | Bow-tie ring | Self-intersection |
| `fixtures/invalid/unclosed-ring.geojson` | Ring first point ≠ last point | Ring not closed |
| `fixtures/invalid/duplicate-ring.geojson` | Interior ring identical to outer ring | Invalid ring / zero area |
| `fixtures/invalid/missing-plot-id.geojson` | Feature without a `plot_id` property | Missing stable plot identifier |
| `fixtures/invalid/feature-without-geometry.geojson` | `"geometry": null` | Feature without geometry |
| `fixtures/invalid/malformed.kml` | Truncated KML document | Unreadable KML |
| `fixtures/invalid/swapped-coordinate-columns.csv` | `latitude` column contains longitude values (lat > 90) | Axis order ambiguity / out-of-range latitude |

The "expected finding" names follow the check taxonomy used by the [Filovara EUDR GeoJSON Validator](https://filovara.com/eudr/geojson-validator), a free browser tool that runs these structural checks locally (no upload) and can optionally screen a validated polygon with FAO Open Foris WHISP.

## Try the fixtures

1. Open the [free EUDR GeoJSON validator](https://filovara.com/eudr/geojson-validator).
2. Paste a fixture's contents (valid or invalid).
3. Compare the reported findings against the table above.

For the background rules in plain language, see [EUDR geolocation requirements](https://filovara.com/eudr/geolocation-requirements).

## Contributing

Additional failure modes are welcome — especially real-world variants you have seen in supplier files (CRS declarations, KMZ edge cases, shapefile sidecars). Open an issue or pull request. Keep all data synthetic.

## License

[MIT](LICENSE) — use these fixtures in any tool, test suite, or tutorial. Attribution appreciated but not required.

Maintained by [Filovara](https://filovara.com) — EUDR due diligence software for SMEs.
