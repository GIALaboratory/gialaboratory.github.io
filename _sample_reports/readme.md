# Sandbox Reports

This folder contains the sample reports listed in [Sample Reports](https://gialaboratory.github.io/report-results/sandbox-reports/).

### Instructions

To add a new report:

1. Create a new file named `REPORTTYPECODE_REPORTNUMBER.md`. Example: `DD_1234.md`.
2. Add front-matter to the code using the below template
3. List assets available in bullet form
4. Add any additional notes pertaining to the report
5. Before committing, test that the report is available using a sandbox API key

#### Template

```
---
report_number: 1234
report_type: Diamond Dossier
results_type: DiamondGradingReportResults
---

* PDF
* Proportions diagram

```
