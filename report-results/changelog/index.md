---
title: What's New
---

# GIA Report Results API Changelog

## 2022-12-15

#### Added

- The Digital Report Access Card is now accessible via [`links`](https://gialaboratory.github.io/report-results/reference/links.doc.html) and [`assets`](https://gialaboratory.github.io/report-results/reference/reportasset.doc.html) fields.
- The Digital Report Access Card is currently available for below report types:
    - Diamond Grading Report (DG)
    - Diamond Dossier Report (DD)
    - Laboratory-Grown Diamond Grading Report (LGDG)
    - Laboratory-Grown Diamond Dossier Report (LGDOSS)
    
 For more information, please view the [How to Use the Digital Report Access Card](https://support.gia.edu/s/digital-diamond-dossier?language=en_US#report-access-card) video.

## 2022-11-15

#### Added

- AGS Ideal ® Report PDF and ASET image are now accessible via [`links`](https://gialaboratory.github.io/report-results/reference/links.doc.html) and [`assets`](https://gialaboratory.github.io/report-results/reference/reportasset.doc.html) fields of [`gradingReport`](https://gialaboratory.github.io/report-results/reference/gradingreport.doc.html).
- AGS Ideal ® Report results are also available as individual fields. Please refer to [`SupplementalReportResults`](https://gialaboratory.github.io/report-results/reference/supplementalreportresults.doc.html) for more information. 

## 2022-07-05

#### Added

- The query [`getReport`](https://gialaboratory.github.io/report-results/reference/query.doc.html) now exposes [`industrydisclosures`](https://gialaboratory.github.io/report-results/reference/industrydisclosures.doc.html) to provide diamond's disclosed source of origin.

## 2021-12-22

#### Added

- The query [`getReport`](https://gialaboratory.github.io/report-results/reference/query.doc.html) now exposes an array of [`assets`](https://gialaboratory.github.io/report-results/reference/reportasset.doc.html)  linked to a report.

### Changed

- Meanings for enum values is available in the [Reference Documentation](https://gialaboratory.github.io/report-results/reference/).

## 2020-12-01

#### Added

- Colored diamond color grades are now available as individual fields. See [CDColorGrade](https://gialaboratory.github.io/report-results/reference/cdcolorgrade.doc.html) for more information.
- You can now receive updates on any report for 18 months from the time of your original lookup. See [Checking for Stale Reports](https://gialaboratory.github.io/report-results/docs/#checking-for-stale-reports) for  implementation details.
- Various performance improvements and bug fixes.

#### Deprecated

- The `color` field of [DiamondData](https://gialaboratory.github.io/report-results/reference/diamonddata.doc.html) has been deprecated. Use `color_grades` instead. 

## 2020-08-26

#### Added

- The enum `culet_code` provides [abbreviations](https://gialaboratory.github.io/report-results/reference/culetcode.doc.html) for culet condition

- The enums`girdle_condition_code` and `girdle_size_code` provide [abbreviations](https://gialaboratory.github.io/report-results/reference/girdle.doc.html) for girdle condition and size.

#### Changed

- A more informative error message is returned when a client queries for a non-Sandbox report using a Sandbox API key.
- The fields `shape_code` and `shape_group_code` have been changed from strings to enums. See [ShapeCode](https://gialaboratory.github.io/report-results/reference/shapecode.doc.html) and [ShapeGroupCode](https://gialaboratory.github.io/report-results/reference/shapegroupcode.doc.html).
- The argument `report_date` on [isReportUpdated](https://gialaboratory.github.io/report-results/reference/reportupdated.doc.html) has been renamed `date`.


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
