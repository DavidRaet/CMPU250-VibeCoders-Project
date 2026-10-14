# Baseball Data

This folder contains the New York Mets batting data used in the project. The CSV files provide the source data for the exploratory analysis; cleaned or derived data can also be stored here when needed.

## Dataset coverage and conventions

- `NYM_batting.csv`: 2,728 player-season records and 31 columns, covering 1962–2023.
- `NYM_pitching.csv`: 1,296 player-season records and 37 columns, covering 1962–2023.
- Each row records a player's Mets statistics for one season. Counts depend on playing opportunities; they do not measure performance independently of playing time.
- Column names below match the CSV headers exactly. Counts are numeric totals; rates are numeric values, generally stored as decimals rather than percentages (for example, `.250` batting average). Our EDA proposes replacing missing positions with `Unknown`.

## Shared variables

These columns appear in both CSV files.

| Variable | Type / unit | Description |
| --- | --- | --- |
| `Rank` | Integer | Source row/order number within a season; its ranking criterion is undocumented. |
| `Year` | Integer, calendar year | Season in which the statistics were recorded. |
| `Position` | Category | Listed defensive position or pitching role; some records are blank. Codes are explained below. |
| `Name` | Text | Player name. Used to group records in the current notebook; names are not guaranteed unique player IDs. |
| `Age` | Numeric, years | Player age listed for that season; the source's age reference date is not documented here. |
| `Strikeouts` | Count | Batting: times the batter struck out. Pitching: opposing batters struck out by the pitcher. |
| `Dominant_Hand` | Category | Source hand label, `Left` or `Right`; representing the batter's preferred hand to play in. |
| `Switch_Hitter` | Category | Whether the player is labeled a switch hitter (`Yes`/`No`). |

Batting position codes: `C` catcher; `1B`, `2B`, `3B` first, second, and third base; `SS` shortstop; `LF`, `CF`, `RF` left, center, and right field; `OF` outfield; `IF` infield; `CI` corner infield; `MI` middle infield; `UT` utility; `DH` designated hitter; `P` pitcher. Pitching roles: `SP` starting pitcher, `RP` relief pitcher, and `CL` closer. 

## Batting variables

Together with the eight shared variables, these describe all 31 batting columns.

| Variable | Unit | Description / construction |
| --- | --- | --- |
| `Games` | Count | Games in which the player appeared. |
| `Plate_Appearances` | Count | Completed batting opportunities, including at-bats, walks, hit-by-pitches, sacrifices, and other qualifying outcomes. Used as a playing-opportunity measure. |
| `At_Bats` | Count | Official at-bats; excludes walks, hit-by-pitches, sacrifice bunts, sacrifice flies, and certain interference outcomes. |
| `Runs` | Count | Runs scored by the player. |
| `Hits` | Count | Credited hits of any type: singles, doubles, triples, and home runs. |
| `Doubles` | Count | Two-base hits. |
| `Triples` | Count | Three-base hits. |
| `Home_Runs` | Count | Home runs hit. |
| `Runs_Batted_In` | Count | Runs credited to the batter's action under scoring rules; depends partly on runners on base. |
| `Stolen_Bases` | Count | Successful stolen bases. |
| `Caught_Stealing` | Count | Times caught attempting to steal a base. |
| `Base_On_Balls` | Count | Walks received, including intentional walks. |
| `Batting_Average` | Hits per at-bat | `Hits / At_Bats` (BA or AVG); all hit types receive equal credit. |
| `On_Base_Percentage` | Rate | `(Hits + Base_On_Balls + Times_Hit_By_Pitch) / (At_Bats + Base_On_Balls + Times_Hit_By_Pitch + Sacrifice_Flies)` (OBP). The denominator does not represent all plate appearances. |
| `Slugging_Percentage` | Bases per at-bat | `Total_Bases / At_Bats` (SLG); weighs extra-base hits more heavily. |
| `On_Base_Plus_Slugging_Percentage` | Numeric rate sum | OBP + SLG (OPS); displayed component rounding may cause small differences. |
| `On_Base_Plus_Slugging_Percentage_Plus` | Adjusted index | OPS+; adjusts offensive performance for league and park context. 100 is league average; higher is better. |
| `Total_Bases` | Count of bases | Bases earned through hits: singles + 2 × doubles + 3 × triples + 4 × home runs. Excludes walks and stolen bases. |
| `Double_Plays_Grounded_Into` | Count | Ground balls by the batter that resulted in a double play. |
| `Times_Hit_By_Pitch` | Count | Times the batter was awarded first base after being hit by a pitch (HBP). |
| `Sacrifice_Hits` | Count | Credited sacrifice bunts (SH); distinct from sacrifice flies. |
| `Sacrifice_Flies` | Count | Credited sacrifice flies (SF). |
| `Intentional_Bases_on_Balls` | Count | Intentional walks received, which is also a subset of `Base_On_Balls`. |


## Project-created and proposed variables

Implementation status below reflects `eda/Vibe_Coders_Project.ipynb`. A mix of cleaned and derived metrics from the notebook and EDA document that weren't implemented yet are included below.

| Variable / label | Status | Description / construction |
| --- | --- | --- |
| `player_seasons` | Implemented notebook summary | `batting['Name'].value_counts()`: number of batting rows per player name. Interpreted as recorded Mets seasons, assuming one row per player-season and unambiguous names. Does not measure a player's entire MLB career, consecutive seasons, or elapsed career length. |
| `corr_matrix` | Implemented notebook summary | Pairwise Pearson correlations among `Age`, `Games`, `Plate_Appearances`, `Batting_Average`, `On_Base_Percentage`, `Slugging_Percentage`, OPS+, and `Home_Runs`. Values range from −1 to 1. |
| `Singles` (also called `Single` in the notes) | Proposed | One-base hits: `Hits − Doubles − Triples − Home_Runs`. Corrects the notes' formula, which incorrectly adds triples and home runs. |
| `wOBA` | Proposed | Weighted on-base average: `[wBB × (BB − IBB) + wHBP × HBP + w1B × Singles + w2B × Doubles + w3B × Triples + wHR × Home_Runs] / (AB + BB − IBB + SF + HBP)`. Here BB = `Base_On_Balls`, IBB = `Intentional_Bases_on_Balls`, HBP = `Times_Hit_By_Pitch`, AB = `At_Bats`, and SF = `Sacrifice_Flies`. Weights must be obtained for each season; they are not included in these CSVs. Higher values indicate greater weighted batting production per opportunity. |
| `OPS`, `OPS_Plus`, `Hit_By_Pitch`, `Sac_Flies` in the notes' batting summary | Proposed aliases | Refer respectively to batting `On_Base_Plus_Slugging_Percentage`, `On_Base_Plus_Slugging_Percentage_Plus`, `Times_Hit_By_Pitch`, and `Sacrifice_Flies`. They are not extra source columns. |
| Career longevity / subsequent-year games | Proposed outcomes | Our document suggests measuring long-term success using observed seasons or games in a following season. No exact outcome definition or calculation is implemented. Mets-only coverage cannot establish total MLB career length; the final recorded season does not prove retirement. |
| Rate metric distortions / outlier status | Proposed outcomes | Labels in the research-question table, with no implemented numeric definition or outlier threshold. |

We also propose retaining batting rows with `Plate_Appearances >= 50`, filling missing positions with `Unknown`, and dropping unnecessary columns, which are our planned transformations. `performance_g1` is a notebook list selecting existing columns. wRC+, Catch Probability, xBA, and BaseRuns are background metrics discussed in our EDA.

## Definition references

- [MLB standard statistics glossary](https://www.mlb.com/glossary/standard-stats): batting and pitching count/rate terminology.
- [Baseball-Reference glossary](https://www.baseball-reference.com/glossary/): conventional statistics and adjusted indices.
- [FanGraphs wOBA definition and formula](https://library.fangraphs.com/offense/woba/): season-dependent weights and denominator for the proposed metric.