
## Python

```bash
pip install -r requirements.txt
cd src
python3 01_build_panel.py
python3 02_event_study.py
python3 03_did.py
python3 04_robustness_leave_co_out.py
python3 05_timed_vs_untimed.py
```

## Data sources

Monthly handle: [legalsportsreport.com](https://www.legalsportsreport.com/sports-betting/states/revenue/),
aggregating state gaming-regulator reports. Endorsement dates: PR Newswire, BusinessWire,
company newsrooms, and sports-business trade press, one dated source per event. Full
citations in `FINDINGS.md` and the paper's reference list.

## Scope and limitations

Three treated states, six events, and only 2-3 state clusters for standard errors. See
`FINDINGS.md` for the full discussion, including how much the headline estimate depends
on Colorado.
