
# About this project

This project maps and explores the educational mobility of Chinese students in the United States during the Second World War. It draws on data extracted from the *Directory of Chinese University Graduates & Students in America* [旅美中國同人録], compiled by the China Institute in America and published in 1944 by the Committee on Wartime Planning for Chinese Students in the United States. By reconstructing students’ educational trajectories and linking the institutions they attended in China, the United States, and other countries, the project uses interactive networks and spatial visualizations to examine patterns of transnational mobility, institutional connections, and academic training among Chinese students during the wartime period.

## From Birth to Education 



## Educational Mobility and Networks

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
