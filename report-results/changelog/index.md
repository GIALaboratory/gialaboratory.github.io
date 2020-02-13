---
title: What's New
---

# GIA Report Results API Changelog

## 2020-02-04

New features (volume pricing, diamond letters) added, reports' fields updated issues fixed.

#### Added
- A new pricing option called "Volume Pricing" has been delivered, in addition to our existing "Pay As You Go" offering.  Volume pricing allows clients to lookup reports up to a maximum limit for a set monthly price.  We have developed additional bulk pricing plans, listed at gia.edu/report-results-api, along with more information regarding this new payment option.
- Support for diamond letters: four new links are now available to download PDFs for reports with diamond letter service.

#### Changed
- `color_distribution` added to all synthetic/lab-grown reports
- `proportions` added to all synthetic/lab-grown reports
- `luster`, `matching`, `drilling` and `quantity` added to pearl reports

#### Fixed
- Melee reports now return all fields
- `clarity_characteristics` now appear on grading reports only


## 2020-01-24

#### Changed
- All historical report assets are now available via the API.

## 2019-11-04
GIA is excited to announce the general availability of the GIA Report Results API!

#### Added
- `getReport` query field
- `getQuota` query field

_Note regarding availability of assets_: At launch, a limited number of historical reports may not contain assets such as the pdf, plotting and/or proportions diagrams while data migration operations continue. Recently issued reports will contain all expected assets. A note will be posted here once the migration of historical assets is completed.
