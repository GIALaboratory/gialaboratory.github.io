---
title: What's New
---

# GIA Report Results API Changelog

## 2020-08-26

#### Added

- A more informative error message is returned when a client queries for a non-Sandbox report using a Sandbox API key.
- A new object has been added to [DiamondData](https://gialaboratory.github.io/report-results/reference/diamonddata.doc.html) called [Culet](https://gialaboratory.github.io/report-results/reference/culet.doc.html), containing one field called  `culet_code`. `culet_code` is a machine-readable abbreviation for `culet` in [DiamondProportions](https://gialaboratory.github.io/report-results/reference/diamondproportions.doc.html). `culet_code` is an enumeration defined in [CuletCode](https://gialaboratory.github.io/report-results/reference/culetcode.doc.html).
- Two new fields have been added to [Girdle](https://gialaboratory.github.io/report-results/reference/girdle.doc.html): `girdle_condition_code` and `girdle_size_code`.  Both are machine-readable abbreviations for the  [Girdle](https://gialaboratory.github.io/report-results/reference/girdle.doc.html) fields `girdle_condition` and `girdle_size` respectively.  Both are enumerations: `girdle_condition_code` is defined in [GirdleConditionCode](https://gialaboratory.github.io/report-results/reference/girdleconditioncode.doc.html) and `girdle_size_code` is defined in [GirdleSizeCode](https://gialaboratory.github.io/report-results/reference/girdle.doc.html).


#### Changed

- [isReportUpdated](https://gialaboratory.github.io/report-results/reference/reportupdated.doc.html) renamed argument `report_date`  to `date`. The purpose of this field in the `isReportUpdated` remains the same; to provide the date from which to begin looking for changes to a report.  

#### Fixed

- The fields `shape_code` and `shape_group_code` in the [DiamondShape](https://gialaboratory.github.io/report-results/reference/diamondshape.doc.html) object are now enumerations, [ShapeCode](https://) and [ShapeGroupCode](https://) respectively.


## 2020-06-08

#### Added

- [isReportUpdated](https://gialaboratory.github.io/report-results/reference/reportupdated.doc.html) allows you to determine whether a report has been updated after a given date and time. See [Checking for Stale Reports](https://gialaboratory.github.io/report-results/docs/#checking-for-stale-reports) for details. Calling `isReportUpdated` does not incur a quota lookup.
- [Quota](https://gialaboratory.github.io/report-results/reference/quota.doc.html) now returns an array of [Quota Buckets](https://gialaboratory.github.io/report-results/reference/quotabucket.doc.html). This gives information for each of your lookups purchased, the number of lookups remaining, and the expiration date of the purchase. For more information, please see [Quota Monitoring](https://gialaboratory.github.io/report-results/docs/#quota-monitoring) in the API Documentation.
- Tips for migrating from the legacy Report Check API have been added to the [API Documentation](https://gialaboratory.github.io/report-results/docs/#migrating-from-the-legacy-report-check-api) page. Short codes for [culet size](https://gialaboratory.github.io/report-results/docs/#culet-size), [girdle thickness](https://gialaboratory.github.io/report-results/docs/#girdle-thickness), and [girdle condition](https://gialaboratory.github.io/report-results/docs/#girdle-condition) are listed.

#### Changed

- The [Quickstart Guide](https://gialaboratory.github.io/report-results/quickstart/) is updated to use [Insomnia](https://insomnia.rest/).

## 2020-02-04

#### Added
- Volume Pricing is now available in addition to our existing "Pay As You Go" offering.  Volume pricing allows clients to lookup reports up to a maximum limit for a set monthly price.
- Diamond Type Letters are now downloadable in PDF form, when requested as part of the grading service.

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
