# NYC Building Violation Signals: 14-record sample

A small, anonymized, building-level table of **14 open NYC Housing Maintenance Code violations** (roof, fire-protection and pest/vermin conditions) taken from a 2026-10-21 snapshot of the public NYC Open Data HPD violations dataset, joined to public building attributes (units, floors, year built) and enriched with a normalized event type, a service-category label and a one-sentence plain-language inference.

It is intended as a compact, readable example of how raw city violation records can be normalized into a clean tabular schema: useful for teaching joins on open data, prototyping text-to-category labeling, or testing an ETL pipeline. It is **not** a statistical sample of NYC housing conditions.

## What is in the file

- `data/nyc_hpd_building_violation_signals_sample.csv`: 14 rows x 20 columns, UTF-8 CSV with a header row.
- `data/data_dictionary.csv`: per-field description, type, example, source/derivation, nullability and a PII flag (all fields are `NO`).

Composition: 8 roof-condition records, 3 fire-protection records, 3 pest/vermin records. Boroughs: Bronx 6, Brooklyn 3, Manhattan 3, Queens 2. Latest citation dates run from 2026-10-05 to 2026-10-20; 9 rows are labeled `NEW` and 5 `CURRENT` at the snapshot date.

## Fields

| Field | Type | Description | Nullable | Source / derivation |
|---|---|---|---|---|
| `sample_id` | string | Unique identifier of the sample record within this file (not a public-record number). | No | Assigned by Hammerline when the sample is built |
| `market` | string | OI market key for the record's metropolitan area. | No | Hammerline market taxonomy, derived from the record's borough/city |
| `borough_or_city` | string | NYC borough (or city) where the cited building is located. No street address is included. | No | NYC HPD violations dataset (borough field) |
| `state` | string | US state abbreviation. | No | Derived from the borough/city |
| `zip_code` | string | 5-digit ZIP code of the cited building. Building-level, no street address or unit. | No | NYC HPD violations dataset (zip field) |
| `service_category` | string | Commercial service category the record may be relevant to. | No | Hammerline classification of the violation type (roof, fire-protection, pest) |
| `public_record_event_type` | string | Type of public-record event: an open violation of the housing maintenance code or building code. | No | Hammerline normalization of the HPD violation category |
| `cited_condition` | string | Plain-language summary of the cited condition. Apartment-specific wording is removed. | No | Summarized by Hammerline from the HPD violation description |
| `open_citation_count` | integer | Number of open citations of this type at the building at the snapshot date. | No | Count of open HPD violations of that category for the building at snapshot |
| `latest_citation_date` | date | Most recent citation (inspection) date among the open citations, ISO 8601. | No | NYC HPD violations dataset (inspection / issue date) |
| `freshness_at_snapshot` | string | Hammerline freshness label at the snapshot date: NEW = cited within about 3 days; CURRENT = older but still inside OI's current window (aging records are flagged separately and not included here). | No | Derived by Hammerline from latest_citation_date and snapshot_date |
| `building_type` | string | Building description from city property records. | Yes | NYC DCP PLUTO building class, normalized by Hammerline |
| `residential_units` | integer | Number of residential units in the building. | Yes | NYC DCP PLUTO (unitsres) |
| `floors` | integer | Number of floors. | Yes | NYC DCP PLUTO (numfloors) |
| `year_built` | integer | Year the building was built. | Yes | NYC DCP PLUTO (yearbuilt) |
| `source_agency` | string | Agency that publishes the underlying public record, with the agency supplying building attributes. | No | Static per source |
| `source_dataset` | string | Name of the public dataset the violation comes from. | No | NYC Open Data |
| `source_reference_url` | url | Dataset-level reference page on NYC Open Data (not a record-level link). | No | NYC Open Data portal |
| `commercial_relevance` | string | One-sentence inference by Hammerline on why a service provider may care. It is an inference, not a statement that the owner wants or will buy a service. | No | Hammerline research write-up (OUR INFERENCE section) |
| `snapshot_date` | date | Date the record snapshot was taken. Citations may have been cured since. | No | Hammerline snapshot date |

Fields from the city records: `borough_or_city`, `zip_code`, `latest_citation_date`, `building_type` (normalized from DCP building class), `residential_units`, `floors`, `year_built`, `source_*`. Fields added by the publisher: `sample_id`, `market`, `state`, `service_category`, `public_record_event_type`, `cited_condition`, `open_citation_count` (a count over the source rows), `freshness_at_snapshot`, `commercial_relevance` (an inference, not a fact), `snapshot_date`.

## Anonymization and privacy

No personal data. The file excludes owner, officer and managing-agent names, street addresses, house numbers, apartment or unit text, phone numbers and email addresses. Location is limited to borough and ZIP code. Building attributes (units, floors, year built) are public property facts about structures, not about people. Even so, please do not try to re-identify buildings' occupants or owners, and do not contact residents.

## Intended uses

- Teaching and practice with tabular open data (types, dates, nullable fields, provenance columns).
- Prototyping classification of violation text into service categories, or freshness labeling from dates.
- Schema reference for building-level violation data.

## Out-of-scope uses and limitations

- 14 rows were chosen to illustrate the schema across three categories. They are not drawn to be representative, and no statistic computed from them should be generalized.
- A snapshot, not live data: violations may have been corrected since 2026-10-21. It is not an official record; check the City's dataset for current status.
- `service_category` and `commercial_relevance` are the publisher's inferences. A violation does not mean the owner wants, needs or will buy any service, and rows are not requests for service or guaranteed leads or jobs.
- Most NYC records concern residential apartment buildings. Several building attributes can be null.
- Do not use to harass or discriminate against residents or owners, or to re-identify individuals.

## How to load

```python
import pandas as pd
df = pd.read_csv("data/nyc_hpd_building_violation_signals_sample.csv")
print(df.groupby("service_category").size())
```

## Source, license and attribution

- **Underlying data:** NYC Open Data, "HPD Housing Maintenance Code Violations" (dataset id `wvxf-dwi5`), https://data.cityofnewyork.us/Housing-Development/Housing-Maintenance-Code-Violations/wvxf-dwi5 ; building attributes from NYC DCP PLUTO. Under NYC's Open Data Law (Admin. Code 23-502(d)), public datasets are available without registration, license requirement or restrictions on their use.
- **Attribution:** Contains public data from the City of New York (NYC Open Data), published by the NYC Department of Housing Preservation and Development (HPD), with building attributes from the NYC Department of City Planning (DCP) PLUTO dataset. The classification fields and inference text were added by Hammerline Growth. The City of New York does not endorse this dataset.
- **License for this sample (the publisher's added fields and the compilation): Creative Commons Attribution 4.0 International (CC BY 4.0).** Please credit "Hammerline Growth" and the City of New York as above. The license covers this 14-record sample only. It does not extend to the underlying city data (which remains open under the City's terms) or to anything else the publisher offers.

**Scope of this license.** CC BY 4.0 applies ONLY to the explicitly published 14-row public sample package in this repository (the 14-row CSV, its data dictionary, this README and the citation file). It does NOT license the full Opportunity Intelligence (OI) database, paid OI products, proprietary derived datasets, or any future dataset that is not explicitly released under that license. The underlying NYC Open Data remains subject to the City of New York's terms.


## Update information

Static snapshot dated 2026-10-21 (published 2026). This sample is not refreshed on a schedule. A newer sample may be posted later under a new version tag.

## Provenance and publisher disclosure

Prepared by Hammerline Growth, which also produces a larger research product built from public inspection and violation records. This sample is provided free under CC BY 4.0 regardless of any use of that product. Method notes: https://hammerlinegrowth.com/methodology

## Repository contents

```
data/nyc_hpd_building_violation_signals_sample.csv
data/data_dictionary.csv
LICENSE        (CC BY 4.0 full text)
CITATION.cff
```

## How to cite

See `CITATION.cff` (GitHub shows a "Cite this repository" button).

## Contributing

This is a static sample. Issues about data errors or unclear field definitions are welcome.
