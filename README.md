# Replicating Card (1995): A Positive Return to Schooling, with Uncertainty About Its Size

## The paper and its headline claim

- **Paper:** Card, D. (1995). “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.” In L. N. Christofides, E. K. Grant and R. Swidinsky (eds.), Aspects of Labour Market Behaviour: Essays in Honour of John Vanderkamp, pp. 201–222. University of Toronto Press.
- **Identification strategy:** Instrumental variables using two-stage least squares, with childhood proximity to a four-year college as the instrument for years of schooling.
- **Headline claim:** An additional year of schooling raises hourly wages by an estimated 13.2 log points (SE 5.5 log points), reported in Table 3, Panel A, column (5).
- **My replication:** 0.1315 (SE 0.0550) compared with the paper’s 0.132 (SE 0.055), a gap of −0.0005, or approximately 0.38% below the paper’s reported estimate, explained by rounding.

## Reproduce the headline table

Python 3.11 or newer. From the repository root:

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python reproduce.py
```

`python reproduce.py` is the one command. Once `MY_PAPER` is set in `download_data.py`,
it downloads the data if `data/` does not have it, runs `notebooks/01_positioning_and_replication.ipynb` and then
`notebooks/02_robustness.ipynb` top to bottom in a fresh kernel, and prints the headline
table from `output/headline_table.csv`. Executed copies of the notebooks, with their
outputs, go to `output/executed/`.

- **Expected runtime:** TODO: minutes on your machine, measured from a clean clone (the empty template runs in under a minute).

## Data: source and licence

## Data: source and licence

- **Source:** NLS Young Men data used by Card, available from Scott Cunningham’s Mixtape repository:
  https://raw.githubusercontent.com/scunning1975/mixtape/master/card.dta
- **Included or downloaded:** Downloaded automatically using `download_data.py`. This follows the template’s default approach and lets the project retrieve the data without a manual upload.

The template downloads by default: `.gitignore` excludes `data/*.dta`, and
`download_data.py` fetches the files on the first run. Two facts to check before you
decide:

- The five files from `scunning1975/mixtape` (castle, card, lee, hansen, abortion) are in a
  repository under an MIT-style licence that permits copies if its notice is kept. The
  underlying data were collected by the original authors.
- The Dehejia-Wahba files on `users.nber.org/~rdehejia` state no licence. Download them;
  do not commit them.

If you decide to include the data, delete the `data/*.dta` line from `.gitignore`, commit
the files with the licence notice, and say so above.

## Repository map

| Path | What it is |
|---|---|
| `reproduce.py` | The one command: runs the notebooks in name order, prints the headline table |
| `download_data.py` | Data URLs for the six papers; `python download_data.py --paper card` fetches one into `data/` |
| `notebooks/01_positioning_and_replication.ipynb` | Sections 1 and 2: positioning notes, the replication, the theirs-vs-mine table, the discrepancy log. **No AI.** |
| `notebooks/02_robustness.ipynb` | Section 3: four robustness exercises. AI allowed, logged in `ai-appendix.md` |
| `output/headline_table.csv` | The theirs-vs-mine table, written by notebook 01 |
| `output/` | Every table and figure the report uses |
| `report/` | The report PDF |
| `ai-appendix.md` | P.R.I.M.E. entries and full transcripts for Sections 3 to 6, and the no-AI statement for Sections 1 and 2 |
| `AUDIT.md` | How to run the peer audit you file on a classmate's repository |
| `requirements.txt` | Pinned package versions |

## Use of AI

Sections 1 and 2 were completed without AI.

TODO: list the sections where you used AI (from 3, 4, 5 and 6), for what, and point to the
entries in [`ai-appendix.md`](ai-appendix.md). If you used none, say so.
