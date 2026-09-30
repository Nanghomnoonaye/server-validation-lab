# Server Validation Lab

A practice lab for server product managers. Leadership says "testing has a lot of problems". Your job is to find out why from the data, then make the gate decisions.

All programs, numbers and problems are **fictional practice data**.

## What's in the lab

- **Where the programs stand:** the EVT → DVT → PVT → MP schedule for three server programs (planned vs actual), pass rate against each exit target, and open issues by severity.
- **Four investigations:** each case starts from a clue and a chart. You pick the root cause (design, firmware, component, test setup, or assembly) and then the PM's next move.
- **Gate review:** Go, conditional Go, or Hold for three real-looking decisions.
- **Do it yourself:** download the data and rebuild the analysis in Excel, Power BI, SQL or Python.

## Data (`data/`)

| File | Contents |
|---|---|
| `Server_Test_Practice.xlsx` | All tables, plus 10 exercises from data cleaning to a Chinese weekly status report |
| `server_test.db` | SQLite database with the same four tables |
| `test_runs.csv` | 2,885 test runs (Pass / Fail, station, firmware, DIMM lot, ambient temperature) |
| `issue_log.csv` | 250 issues with severity, owner, status and root cause; a few messy rows on purpose |
| `thermal_log.csv` | CPU/GPU temperatures and fan duty for thermal tests (join on `Run_ID`) |
| `schedule.csv` | Planned vs actual dates for each gate, with exit criteria |
| `Answer_Key.md` | Worked answers to the 10 exercises. Try them first. |

## Run it

Open `index.html` in a browser, or turn on GitHub Pages for this repo (Settings → Pages → Deploy from branch → `main` / root).

## Related reading

[HW/SW Product Management](https://lolopodcast.github.io/PM-PO-PL-PCC/) covers stage-gates and the PM / PO / PL / PCC roles.
