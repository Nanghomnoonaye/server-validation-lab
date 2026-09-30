# Answer Key — Server Test Practice Data

Try the exercises first. Every number here was calculated from the data files, with the data snapshot date 2026-09-25.

## 1. Clean the issue log

- **Project name not in upper case:** 5 rows (for example `orion-2u` → `ORION-2U`).
- **Test_Category in upper case with a trailing space:** 2 rows (for example `THERMAL ` → `Thermal`).
- **Blank Owner_Dept:** 3 rows. Fill each one from its root cause:
  - ISS-0001 (No Fault Found, Network) → Test Eng (No Fault Found is always Test Eng)
  - ISS-0020 (Assembly/Process, Power-on/POST) → ME (assembly/process issues belong to ME)
  - ISS-0183 (Firmware/BIOS, Thermal) → RD-FW (the note says BMC 2.3.1, and the BMC firmware team is RD-FW)

Why it matters: without cleaning, a pivot table shows `orion-2u` and `ORION-2U` as two projects, and every count is wrong.

## 2. Test pass rate vs exit target

| Project | Stage | Runs | Passed | Pass rate | Target | Meets? |
|---|---|---:|---:|---:|---:|---|
| LYRA-4U | DVT | 126 | 117 | 92.9% | 92.0% | Yes |
| LYRA-4U | EVT | 549 | 471 | 85.8% | 85.0% | Yes |
| ORION-2U | DVT | 514 | 468 | 91.1% | 92.0% | **No** |
| ORION-2U | EVT | 454 | 428 | 94.3% | 85.0% | Yes |
| ORION-2U | PVT | 240 | 232 | 96.7% | 97.0% | **No** |
| VEGA-1U | DVT | 519 | 481 | 92.7% | 92.0% | Yes |
| VEGA-1U | EVT | 483 | 438 | 90.7% | 85.0% | Yes |

**Missed:** ORION-2U DVT and ORION-2U PVT. ORION-2U DVT was still allowed to exit (see its PM_Note in the schedule sheet). ORION-2U PVT misses only slightly, but PVT is the last gate before mass production.

## 3. Open issues

| Project | Critical | Major | Minor | Total |
|---|---:|---:|---:|---:|
| LYRA-4U | 33 | 3 | 0 | 36 |
| ORION-2U | 2 | 4 | 1 | 7 |
| VEGA-1U | 1 | 6 | 2 | 9 |
| **All** | 36 | 13 | 3 | 52 |

There are 36 open Critical issues, but they are only a few real problems:
- **LYRA-4U: 33 issues share one root cause**, GPU heatsink airflow that isn't enough at 30 °C ambient and above. That is one design problem logged over and over.
- ORION-2U: 2 are recent and still `Under Analysis` (ISS-0240, ISS-0242).
- VEGA-1U: 1 is `No Fault Found` but still open (ISS-0231). The PM should push to either close it or reproduce it.

Lesson: count problems by root cause, not by number of tickets.

## 4. The bad station

| Station | Runs | Fails | Fail rate |
|---|---:|---:|---:|
| ST-01 | 219 | 15 | 6.8% |
| ST-02 | 254 | 18 | 7.1% |
| ST-03 | 248 | 17 | 6.9% |
| ST-04 | 250 | 14 | 5.6% |
| ST-05 | 209 | 10 | 4.8% |
| ST-06 | 239 | 15 | 6.3% |
| **ST-07** | 256 | 38 | **14.8%** |
| ST-08 | 251 | 19 | 7.6% |
| ST-09 | 258 | 14 | 5.4% |
| ST-10 | 267 | 15 | 5.6% |

**ST-07** fails about twice as often as the others. Split by date:
- Before 7/20: 1 fails in 99 runs (1.0%)
- 7/20–8/28: 30 fails in 95 runs (31.6%)
- After 8/28: 7 fails in 62 runs (11.3%)

Of ST-07's issues, 19 are `No Fault Found` and 9 are `Test Setup/Fixture`. 17 of the No Fault Found issues were opened before 2026-08-12, when the worn cable was found. They were really the same fixture problem that nobody had recognized yet.

The problem was not in the servers. A worn test cable caused false failures from about 7/20 until it was replaced on 8/28. After that, the rate falls back toward normal (the sample after 8/28 is small). That wasted engineering time and made the pass rate look worse than it was. PM action: track fail rate by station every week, so a bad fixture is caught in days, not weeks.

## 5. DIMM lot

| DIMM lot | Memtest runs | Fails | Fail rate |
|---|---:|---:|---:|
| A2605 | 52 | 4 | 7.7% |
| A2607 | 50 | 3 | 6.0% |
| B2604 | 52 | 4 | 7.7% |
| **B2607** | 30 | 13 | **43.3%** |
| D2606 | 27 | 1 | 3.7% |

Lot **B2607** fails 43.3% of the time, while the other lots stay under 8%. It is used mainly in **VEGA-1U** (19 VEGA units carry it, more than any other lot). Symptom: correctable ECC errors over threshold. This is a component (supplier) problem, owned by SQE. The lot was quarantined on 2026-09-08 and an 8D report was requested from the supplier. It is the main reason VEGA-1U DVT can't exit.

## 6. BMC firmware and fans

| BMC FW | Thermal runs | Fails | Fail rate | Avg fan duty | Avg CPU temp |
|---|---:|---:|---:|---:|---:|
| 2.2.8 | 124 | 16 | 12.9% | 74.2% | 71.4 °C |
| 2.3.1 | 172 | 46 | 26.7% | 66.2% | 74.1 °C |
| 2.3.4 | 138 | 13 | 9.4% | 70.8% | 70.9 °C |

BMC **2.3.1** (used 7/15–8/20) doubled the thermal failure rate. `Fan speed not responding` appears 31 times on 2.3.1, compared with 6 on 2.2.8 and 1 on 2.3.4. The fan control table didn't load after an AC power cycle, so the fans stayed slow (about 30% duty) and the CPUs overheated. The fix, **2.3.4**, worked: the failure rate dropped back down. It affected all three projects because they share the same BMC firmware.

## 7. LYRA-4U GPU thermal

| Ambient | Thermal runs | Fails | Fail rate | Avg GPU temp | Max GPU temp | GPU throttle events |
|---|---:|---:|---:|---:|---:|---:|
| 25 °C | 38 | 2 | 5.3% | 71.3 °C | 85.7 °C | 0 |
| 30 °C | 36 | 12 | 33.3% | 82.0 °C | 95.6 °C | 12 |
| 35 °C | 43 | 24 | 55.8% | 87.9 °C | 103.6 °C | 29 |

The hotter the room, the worse it gets, and failures continue after the BMC fix. So this is a **design** problem (heatsink/airflow), not firmware. Servers usually have to work at 35 °C ambient, so LYRA-4U can't pass DVT until the new air shroud is proven. This is the biggest risk across the three projects.

## 8. Time to close and aging issues

| Root cause | Closed issues | Avg days | Median days |
|---|---:|---:|---:|
| Component | 31 | 16.7 | 9.0 |
| Firmware/BIOS | 66 | 15.3 | 11.0 |
| Assembly/Process | 27 | 11.6 | 10.0 |
| Design | 28 | 10.4 | 8.5 |
| No Fault Found | 37 | 7.6 | 4.0 |
| Test Setup/Fixture | 9 | 3.2 | 3.0 |

Component and firmware issues take longest to close because they depend on a supplier or on the next firmware release. **26 open issues are older than 30 days:** Thermal 23, SQE 3. These are the LYRA thermal design issue and the DIMM lot, the same two problems that block the schedule.

## 9. Schedule slip

| Project | Stage | Planned end | Actual end | Slip (days) |
|---|---|---|---|---:|
| ORION-2U | EVT | 2026-07-10 | 2026-07-10 | 0 |
| ORION-2U | DVT | 2026-08-20 | 2026-08-31 | 11 |
| ORION-2U | PVT | 2026-10-09 | running | – |
| VEGA-1U | EVT | 2026-07-31 | 2026-07-31 | 0 |
| VEGA-1U | DVT | 2026-09-15 | running | 10 overdue |
| VEGA-1U | PVT | 2026-10-30 | not started | – |
| LYRA-4U | EVT | 2026-08-25 | 2026-09-10 | 16 |
| LYRA-4U | DVT | 2026-10-15 | running | – |
| LYRA-4U | PVT | 2026-11-30 | not started | – |

- **ORION-2U DVT, 11 days late and below its pass-rate target:** thermal failures from BMC 2.3.1, plus false failures from the ST-07 fixture. PVT started 11 days late as a result.
- **LYRA-4U EVT, 16 days late:** the GPU thermal design issue. EVT was exited on a waiver, and the issue is now in DVT.
- **VEGA-1U DVT, 10 days past plan and still running:** the DIMM lot B2607 problem. PVT (planned 9/16) can't start yet.

## 10. Sample weekly status (週報)

Your wording can differ. Here is one example:

> **本週測試狀態（截至 9/25）**
> 
> - **LYRA-4U：紅燈。** GPU 在 30°C 以上環境溫度散熱不足，造成降頻，目前有 33 筆 Critical 問題，根本原因相同。負責單位：散熱組。新導風罩驗證中，預計 10/9 前提供結果。
> - **VEGA-1U：黃燈。** DVT 延遲 10 天。DIMM 批號 B2607 ECC 錯誤率偏高（約 43%），已隔離該批號並要求供應商提供 8D 報告。負責單位：SQE。預計 10/3 更換料件後重測。
> - **ORION-2U：黃燈。** PVT 通過率 96.7%，略低於 97% 目標。2 筆 Critical 問題分析中。負責單位：RD。預計 9/30 前確認根本原因。
> - **共通事項：** BMC 2.3.4 已解決風扇控制問題；ST-07 測試線材已更換，建議每週追蹤各測試站不良率。

The dates in this example are made up. Use real commitments from owners at work.
