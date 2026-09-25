
# About this project

This project maps and explores the educational mobility of Chinese students in the United States during the Second World War. It draws on data extracted from the *Directory of Chinese University Graduates & Students in America* [旅美中國同人録], compiled by the China Institute in America and published in 1944 by the Committee on Wartime Planning for Chinese Students in the United States. By reconstructing students’ educational trajectories and linking the institutions they attended in China, the United States, and other countries, the project uses interactive networks and spatial visualizations to examine patterns of transnational mobility, institutional connections, and academic training among Chinese students during the wartime period.

# Educational Mobility and Networks

The following interactive visualizations and supporting data are derived from `educ_geoloc.csv`. 

- [01_bipartite.html](https://carmand03.github.io/chinese-students-us-mobility/output/01_bipartite.html) — Student ↔ university bipartite network.
  - Filters by student name/ID, university, year range, Level, broad Field, and detailed discipline.
  - Level supports multiple selection.
  - Node size is proportional to visible degree.
  - Physics controls: Stop, Slow, Normal, Fast.

- [02_university_projection.html](https://carmand03.github.io/chinese-students-us-mobility/output/02_university_projection.html) — University ↔ university projected network.
  - An edge connects two universities when they share at least one distinct student.
  - Edge weight = number of distinct shared students.
  - Minimum shared-student and minimum visible-degree filters remove isolated/weakly connected nodes.
  - University labels use the raw `University` name; `University_clean` remains the internal key.
  - Node size can represent student count, degree, weighted degree, degree centrality, betweenness, closeness, or PageRank.
  - Edge width can represent shared-student weight or edge betweenness.
  - Louvain communities can optionally be displayed with node colors.
  - Communities can be filtered by community size rather than by community ID.
  - Physics controls: Stop, Slow, Normal, Fast.

- [03_spatial_network.html](https://carmand03.github.io/chinese-students-us-mobility/output/03_spatial_network.html) — Geolocated university projection on an OpenStreetMap basemap.
  - World / United States scope.
  - Minimum shared-student and minimum visible-degree filters.
  - University labels use the raw `University` name.
  - Community-size filtering.
  - Community colors can be switched on or off.
  - University search and map recentering.

- [04_sankey_china_to_us.html](https://carmand03.github.io/chinese-students-us-mobility/output/04_sankey_china_to_us.html) — Student flows from China-based institutions to U.S.-based institutions.
  - Independent source/start and target/end Level filters.
  - Year-range filter.
  - Flow width = number of distinct students.

- [05_sankey_us_to_us.html](https://carmand03.github.io/chinese-students-us-mobility/output/05_sankey_us_to_us.html) — Student flows among U.S. universities.
  - Independent source/start and target/end Level filters.
  - Year-range filter.
  - Flow width = number of distinct students.

- [06_sankey_other_countries.html](https://carmand03.github.io/chinese-students-us-mobility/output/06_sankey_other_countries.html) — Student flows involving institutions outside China and the United States.
  - Includes Other ↔ Other, China ↔ Other, and U.S. ↔ Other transitions.
  - Independent source/start and target/end Level filters.

- [07_sankey_multistage_all_countries.html](https://carmand03.github.io/chinese-students-us-mobility/output/06_sankey_other_countries.html) — Three-stage student pathways across all countries.
  - Uses three consecutive observed education stages.
  - Includes institutions in China, the United States, and other countries.
  - Stage 1, Stage 2, and Stage 3 Level filters are independent.

## Level normalization

The notebook creates a normalized Level variable (`_level_cat`). The intended normalization includes:

- `Doctor`, `Doctorate`, and `Licentiate` → `Doctorate`
- `Diploma` and `Certificate` → `Other degree`
- Missing/blank Level → `Unspecified`

Level selectors permit multiple selections. No selection means all Level categories are included.

## Broad discipline Field

The notebook creates an ad hoc higher-level variable called `Field` from the original `discipline` variable. It is designed for exploratory filtering and is not an official disciplinary classification.

Current Field families are:

- Humanities
- Social Sciences
- Business & Administration
- Engineering
- Physical Sciences
- Biological Sciences
- Earth & Environmental Sciences
- Agricultural Sciences
- Health Sciences
- Education
- Unspecified

The explicit `FIELD_MAP` is stored in the notebook and can be edited if a different substantive classification is preferred. The original detailed discipline values remain available.

## Network methodology

- Student key: `NameID`.
- University key: `University_clean`.
- Visible university label: the most frequent raw `University` value associated with each `University_clean` key.
- Bipartite edges correspond to education records after cleaning.
- University projection: for each student, the set of distinct universities attended is formed. Each student contributes at most one count to any given unordered university pair, preventing duplicate source records from inflating shared-student weights.
- Centrality measures are calculated on the full, unthresholded university projection.
- Betweenness and edge betweenness use topological shortest paths (`weight=None`).
- University coordinates are the median latitude and longitude of available records for each cleaned university.

## Community detection

Communities are detected on the weighted university projection using NetworkX Louvain community detection when available, with shared-student edge weights.

Community membership is calculated on the full projection. Interactive filtering can then restrict the display to communities whose total size falls within a selected size interval.

## Sankey methodology

Two-stage Sankeys are constructed from chronological transitions between consecutive observed education years for each student. When a student has multiple institutions in the same year, possible institution-to-institution transitions between consecutive observed years are represented.

Level filtering is stage-specific:

- Source/start Level applies only to the source observation.
- Target/end Level applies only to the target observation.

The multi-stage Sankey uses three consecutive observed education stages and provides separate Level filters for Stage 1, Stage 2, and Stage 3.

Sankey link values count distinct students for each displayed institution-to-institution flow.

## Supporting CSV files

- `university_node_metrics.csv` — university-level network metrics and community information.
- `university_edge_metrics.csv` — projected university-pair weights and edge metrics.
- `student_transition_events.csv` — chronological two-stage transition records used by the Sankeys.
- `multistage_paths_all_countries.csv` — three-stage pathway records.
- `university_display_mapping.csv` — mapping between `University_clean` internal keys and visible raw `University` labels.

## Reproducibility

The companion notebook is:

`student_university_networks.ipynb`

It contains the complete data-cleaning, classification, graph-construction, community-detection, Sankey, and HTML-export code.

## Internet requirement

The interactive HTML files load JavaScript libraries and/or map tiles from external CDNs:

- vis-network for network visualizations
- Leaflet and OpenStreetMap for the spatial visualization
- Plotly for Sankey diagrams

An internet connection is therefore required for the full interactive visualizations when the HTML files are opened.

# From Birth to Education

This notebook builds interactive visualizations of student
mobility from recorded birthplaces in China to educational institutions
in China, the United States, and other countries. It combines
birthplace/residence data, geolocated education records, supplied
Chinese city coordinates, and historical Chinese province boundaries.

The main notebook is:

`birth_to_education_flows.ipynb`

It produces interactive Sankey diagrams, spatial flow maps,
birthplace-distribution maps, a U.S. state-level education map, and
supporting CSV tables.

## 1. Input data

The notebook expects the following files in `data/`.

### `birth_residence.csv`

Semicolon-delimited student birthplace/residence data. The analysis uses
`NameID` as the student identifier and draws on fields including birth
year, romanized birth province, romanized birth town/city, and student
name.

### `educ_geoloc.csv`

Geolocated education records. The analysis uses `NameID` to link
students and `University_clean` as the standardized
educational-institution identifier. Other fields used include education
year, university name, city, state/province, country,
latitude/longitude, degree level, and discipline.

### `chinese_cities_with_coordinates.csv`

Semicolon-delimited coordinates for Chinese birth towns/cities. The file
also supplies Chinese-script place names used in the combined birthplace
map.

### Historical province shapefile

The historical boundary layer is `Provinces_1912-1931` and requires the
normal shapefile components, including at least:

-   `Provinces_1912-1931.shp`
-   `Provinces_1912-1931.shx`
-   `Provinces_1912-1931.dbf`
-   `Provinces_1912-1931.prj`

The notebook also copies optional sidecar files when available. It
recognizes an uploaded duplicate named `Provinces_1912-1931(1).shp`.

The historical layer is read with `pyshp` and reprojected to WGS84 with
`pyproj`; GeoPandas is not required.

## 2. Installation

The notebook uses standard Python data-analysis packages plus `pyshp`
and `pyproj`.

``` bash
pip install numpy pandas pyshp pyproj
```

It also uses Jupyter/IPython for notebook display. The generated HTML
visualizations load Plotly, Leaflet, and OpenStreetMap resources from
the web, so an internet connection is needed when viewing the
interactive HTML files.

## 3. Running the notebook

Place all source files in `/mnt/data/`, then restart the kernel and run
the notebook from the first cell through the end.

This is important because later sections reuse objects constructed
earlier, including cleaned student records, historical province
geometry, place-name lookups, education-place coordinates, trajectories,
and visualization payloads.

Outputs are written to:

`/mnt/data/birth_education_outputs_enhanced/`

## 4. Data preparation

### Student linkage

`NameID` is converted to a cleaned string identifier in both the
birthplace and education datasets and is used to link records.

Education rows without a student identifier or `University_clean` are
excluded from the education analysis.

### Birth year

Birth year is converted to numeric form. All interactive visualizations
provide:

-   a minimum birth-year selector;
-   a maximum birth-year selector; and
-   an Include/Exclude option for students whose birth year is unknown.

The year controls in these visualizations therefore refer to **year of
birth**, unless a visualization explicitly states otherwise.

### Country grouping

Education countries are normalized into four broad categories:

-   China
-   US
-   Other
-   Unknown

### Degree level

Degree levels are normalized for the U.S. state visualization. In
particular, `Doctor`, `Doctorate`, and `Licentiate` are grouped as
`Doctorate`; `Diploma` and `Certificate` are grouped as `Other degree`;
missing levels become `Unspecified`.

### Educational fields

The notebook maps the source `discipline` values into a broader `Field`
taxonomy used by the U.S. state-level visualization. Unmapped
disciplines fall into the notebook's residual/interdisciplinary
category, while missing disciplines remain unspecified.

## 5. Historical Chinese geography

Historical province polygons come from the 1912--1931 shapefile.

The notebook handles spelling differences between the birthplace
directory and the historical layer, including:

-   `Chahar` → `Chahaer`
-   `Rehe` → `Jehol`

Province geometry is reprojected from the shapefile CRS to WGS84.

For province-origin flow maps, the notebook currently uses the
transformed center of the province polygon's bounding box as an
approximate origin point. This point is intended for visualization and
should not be interpreted as an actual birthplace location.

Birth-town/city origins use the supplied city-coordinate dataset.

## 6. First educational destination

For each student, the notebook identifies the earliest observed
education year within each destination scope:

-   Overall
-   China
-   United States
-   Other countries

If more than one educational institution occurs in the same earliest
observed year, all such institutions are retained.

The first-destination visualizations can represent the educational
destination at three aggregation levels:

1.  **University / school**
2.  **City / town**
3.  **State / province**

At the city and state/province levels, records are aggregated by
educational geography rather than merely relabeled.

For spatial display, city and state/province destination coordinates are
calculated from the median coordinates of geolocated institutions
assigned to that place.

## 7. Multi-stage educational trajectories

The multi-stage analysis uses the first three distinct observed
education years for each student:

-   Stage 1 = first distinct observed education year
-   Stage 2 = second distinct observed education year
-   Stage 3 = third distinct observed education year

When multiple institutions occur in the same selected year, the notebook
retains the possible institution combinations across stages.

The education stages can be viewed as:

-   University / school
-   City / town
-   State / province

This aggregation option is available in both the multi-stage Sankey and
the multi-stage spatial map.

Students with missing birthplace information are retained in the
multi-stage Sankey. Their trajectories begin directly at Education Stage
1 rather than being assigned a fictitious or blank birthplace node.

## 8. Interactive outputs

### `01_birth_to_first_education_sankey.html`

Sankey diagram from birthplace to first observed educational
destination.

Controls include:

-   birthplace level: Province or City/Town;
-   destination scope: Overall, China, United States, or Other;
-   education-stage geography: University/School, City/Town, or
    State/Province;
-   birth-year range;
-   inclusion/exclusion of unknown birth years;
-   Top N flows; and
-   minimum number of students.

Flow width represents distinct students.

### `02_birth_to_first_education_map.html`

Spatial version of birthplace → first-education flows.

It supports the same birthplace, destination-scope, education-geography,
birth-year, Top N, and minimum-student controls.

Historical Chinese province boundaries are displayed as geographic
context.

### `03_multistage_sankey.html`

Sankey diagram of:

`Birthplace → Stage 1 → Stage 2 → Stage 3`

Controls include:

-   birthplace level;
-   education-stage geography;
-   birth-year range;
-   inclusion/exclusion of unknown birth years;
-   Top N links per stage; and
-   minimum students.

Students without a recorded birthplace enter the Sankey directly at
Stage 1.

### `04_multistage_spatial_map.html`

Spatial representation of multi-stage education trajectories.

Users can independently display one or more segment types:

-   Birth → Stage 1
-   Stage 1 → Stage 2
-   Stage 2 → Stage 3

The map also provides:

-   Province or City/Town birthplace level;
-   University/School, City/Town, or State/Province education geography;
-   different colors by trajectory segment or a single-color mode;
-   birth-year filtering;
-   unknown-birth-year inclusion/exclusion;
-   minimum-student threshold; and
-   Top N flows per stage.

### `05_birth_distribution_china_combined.html`

Combined interactive map of recorded birthplaces in China.

The display selector provides:

-   Provinces only
-   Cities only
-   Provinces + cities

Historical provinces are shown with a **blue choropleth**, while
city/town locations use contrasting **orange proportional circles**.

Popups/tooltips include romanized and Chinese names when available.

The map includes the global birth-year controls and browser-side export
buttons for the currently filtered province and city tables.

### `07_us_education_by_state.html`

Interactive U.S. state-level education map.

It can summarize:

-   distinct students;
-   education records; or
-   distinct universities.

Filters include:

-   birth-year range;
-   inclusion/exclusion of unknown birth year;
-   Field; and
-   Level.

The visualization also provides a browser-side CSV export of the
currently filtered state table.

## 9. Statistical and intermediate outputs

The notebook exports supporting CSV files including:

-   `birth_to_first_education_records.csv`
-   `multistage_trajectory_records.csv`
-   `multistage_map_records.csv`
-   `table_births_by_historical_province.csv`
-   `table_births_by_city.csv`
-   `table_us_education_by_state.csv`

The interactive birthplace and U.S. state maps can additionally download
their currently filtered tables from the browser.

The historical province layer is also exported as:

`Provinces_1912-1931.geojson`

## 10. Interpretation cautions

The visualizations describe the **observed records in the source
datasets**. They should not automatically be interpreted as complete
educational biographies or complete migration histories.

In particular:

-   an education stage means an ordered **observed education year**, not
    necessarily a formal degree sequence;
-   missing education records can make an observed stage differ from a
    student's actual chronological educational stage;
-   multiple institutions in the same year can produce multiple retained
    trajectories;
-   birthplace availability varies across students;
-   unknown birth years can be included or excluded interactively;
-   city/state education aggregation depends on the location metadata
    assigned to institutions;
-   city/state map coordinates are representative median institution
    coordinates, not necessarily civic or administrative centroids;
-   province-origin points on flow maps are approximate visualization
    points derived from historical polygon bounding boxes;
-   historical administrative boundaries and names do not necessarily
    correspond to present-day boundaries; and
-   flow widths/counts represent students present in the linked source
    data, not population-level migration rates.

## 11. Reproducibility

For reproducible execution:

1.  Keep the input filenames unchanged or update the path definitions at
    the top of the notebook.
2.  Keep all shapefile components together.
3.  Restart the Python kernel.
4.  Run all notebook cells in order.
5.  Use the generated files in `birth_education_outputs_enhanced/`.

If a later cell raises a `NameError`, first confirm that the notebook
has been run from the beginning. Many visualization cells intentionally
reuse intermediate objects produced during data cleaning and geographic
preparation.

## 12. Main output directory

After a complete run, the primary artifacts are available under:

``` text
birth_education_outputs_enhanced/
├── 01_birth_to_first_education_sankey.html
├── 02_birth_to_first_education_map.html
├── 03_multistage_sankey.html
├── 04_multistage_spatial_map.html
├── 05_birth_distribution_china_combined.html
├── 07_us_education_by_state.html
├── Provinces_1912-1931.geojson
├── birth_to_first_education_records.csv
├── multistage_trajectory_records.csv
├── multistage_map_records.csv
├── table_births_by_historical_province.csv
├── table_births_by_city.csv
└── table_us_education_by_state.csv
```
