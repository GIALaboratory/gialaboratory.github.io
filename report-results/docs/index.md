---
title: Documentation
---

# GIA Report Results API Documentation

## Introduction

The GIA Report Results API enables applications to integrate with GIA's database in order to verify grading information and retrieve assets associated with a report. 

This guide provides the background and details for integrators to evaluate our APIs, and to get started writing code against live endpoints in our sandbox or production environments.

## Concepts

While the API's premise is simple: submit a report number and obtain the report's results, there are details to be aware of. Be sure to read this document carefully.

### GraphQL

The GIA Report Results API is built using [GraphQL](https://graphql.org/). GraphQL is a query language supported by all major programming languages. The benefit of GraphQL is that you can request only the data you need, and that data is returned in a single response.

This means you must specify the data you want in the form of a query passed to the API.

The good news is that most tools provide documentation navigators to help you explore the API. To learn more, try our [Quickstart Guide](/report-results/quickstart).

### Report Results

The GIA Report Results API returns results for nearly all of GIA's reports and services through a single endpoint called `getReport`.

A call to `getReport` returns a `GradingReport` with fields common to all reports. These include report number, report date, and report type.

However, different report types naturally have different fields in their results. For example, a diamond report has different results than a sapphire report. 

To handle this, the `results` field contains a [union type](https://graphql.org/learn/schema/#union-types) called `ReportResults` that will return one of the following concrete types: 

* `DiamondGradingReportResults` for grading services on natural diamonds, such as Diamond Dossier, Diamond Grading, and Colored Diamond Grading reports.
* `PearlIdentReportResults` for Pearl Identification and Pearl Identification and Classification reports.
* `LabGrownDiamondGradingReportResults` for Lab-Grown (formerly Synthetic) diamond reports.
* `IdentificationReportResults` for reports on colored stones such as sapphire, ruby, tourmaline, and others.
* `MeleeServiceResults` for results on the Melee Analysis service.
* `NarrativeReportResults` for results that do not fit in the other formats.

Since the results field may be populated by any of these concrete types, you must use a [conditional fragment](https://graphql.org/learn/queries/#inline-fragments) (such as `... on DiamondGradingReportResults`) to return any fields at all.

__Pro Tip__: Use the [__typename](https://graphql.org/learn/queries/#meta-fields) meta field to determine how to handle the data on your client.

```
{
  getReport(report_number: "2141438171") {
    report_date
    report_number
    report_type
    results {
      ... on DiamondGradingReportResults {
        __typename
        shape_and_cutting_style
        carat_weight
        color_grade
        clarity_grade
      }
    }
  }
}
```

returns

```
{
  "data": {
    "getReport": {
      "report_date": "September 01, 2019",
      "report_number": "2141438171",
      "report_type": "Diamond Dossier",
      "results": {
        "__typename": "DiamondGradingReportResults",
        "shape_and_cutting_style": "Emerald Cut",
        "carat_weight": "0.51 carat",
        "color_grade": "E",
        "clarity_grade": "VS2"
      }
    }
  }
}
```

### Data Types

Most results fields are [String](https://graphql.org/learn/schema/#scalar-types) types to accomodate the text as it appears on a grading report. For example, `Internally Flawless` clarity grades will be spelled out rather than abbreviated `IF`.

In most cases, the abbreviated versions are also available. These [Enums](https://graphql.org/learn/schema/#enumeration-types) are validated to be one of the allowed values. Look for fields that end with `_code`. Examples are `report_type_code`, `clarity_grade_code`, `color_grade_code`, among others.

Fields that are concatenated in the results are also available as individual fields. For example, measurements are stated as "minimum diameter - maximum diameter x depth" for round diamonds and "length x width x depth" for fancy shapes. The individual fields are expressed in the `RoundMeasurements` and `FancyMeasurements` types.

### Asset Links

Links to assets are contained in the `links` field. The availability of assets is dependent on the type of report you are querying. If an asset is not available the field will return `null`.

Assets available are:

* __PDF Facsimile__ A PDF representation of the grading report. Available for most, but not all, report types.
* __Proportions Diagram__ The diagram representing the proportions of the diamond. Available on diamond reports such as the Diamond Dossier and Diamond Grading Report.
* __Plotting Diagram__  The diagram representing clarity characteristics. Available for full grading reports such as the Diamond Grading Report.
* __Image__ Any photograph of the image that is printed on the report. Available for most colored stones and pearl reports and some colored diamond reports.
* __Rough Image and Video__ Image and video taken during rough analysis. Available for the Diamond Origin and Colored Diamond Origin Reports.
* __Polished Image and Video__ Image and video taken after diamond is matched to the rough and issued an origin report. Available for the Diamond Origin and Colored Diamond Origin reports.

The links returned by the GIA Report Results API are active for 60 minutes from the API call. If you attempt to access the asset after this period, you will receive an error response.

## Platform Overview

GIA has invested heavily in engineering this system for the highest possible performance, security, and availability. This section contains details you need to know in order to reliably and securely connect to the API.

### API Plans and Quota Monitoring

Usage of the GIA Report Results API is enabled by a plan. Each plan has a number of report requests associated with it. When this quota is depleted, you will no longer be able to retrieve report results. It is important that you track your quota and take action when necessary.

### API Keys

API keys are used to authenticate and authorize requests to the API. A plan may have multiple keys and you may revoke any key without affecting other keys on the plan.

__Important:__ For your protection, you must securely store these keys and they must not be shared! You are responsible for taking all precautions to safeguard access to the keys.

### Sandbox Environment

Once you apply for API access, you will be issued a sandbox plan and an associated key. This key can be used to access a limited set of reports that represent the variety of results you can expect from the API. 

The sandbox plan will be initially loaded with a number of report lookups. This will allow you to view your quota usage as you develop your application. If you require additional quota on your sandbox plan, we will be happy to assist. There is no charge to add quota to your sandbox API plan.

### Moving to Production

Once your API application is approved by GIA, you will be able to add a production API plan. API keys associated with this plan may access the full set of reports available in GIA's database.

GIA's Report Results API plans are simple and straightforward: you only pay for what you use. When you're ready to query the full set of reports, you select the number of lookups you require and that quota is immediately available for your use. This can be as many or as few lookups as you want. There is no long term contract or commitment.

### Quota Monitoring

You may check your remaining quota at any time by using `getQuota`. Checking your quota does not affect your remaining quota.

```
{
  getQuota{
    remaining
  }
}
```

You can also get your remaining quota with each query to `getReport`.

```
{
  getReport(report_number: "2141438171") {
    report_date
    report_number
    report_type
    quota{
      remaining
    }
  }
}
```

returns

```
{
  "data": {
    "getReport": {
      "report_date": "September 01, 2019",
      "report_number": "2141438171",
      "report_type": "Diamond Dossier",
      "quota": {
        "remaining": 967
      }
    }
  }
}
```

### Rate Limits

Rate limiting is applied based on the authorization key. If the API returns status code `429`, it means that you have sent too many requests. When this happens, check the `Retry-After` header, where you will see a number displayed. This is the number of seconds that you need to wait before you try your request again.

### System Status

The GIA Report Results API is engineered for high availability. You may view our current system status and historical uptime at [status.gia.edu](https://status.gia.edu).

__Important:__ You must subscribe to notifications at [status.gia.edu](https://status.gia.edu). This is the sole method we will use to update you on planned maintenance or unplanned incidents.

## Error Conditions

### Report not found

Requesting a report that is unavailable, the API will return `HTTP 200 OK` call and an errors object. The errors object will include a message that the item is unavailable and an error code.

```
{
  "data": {
    "getReport": null
  },
  "errors": [
    {
      "path": [
        "getReport"
      ],
      "data": null,
      "errorType": null,
      "errorInfo": null,
      "locations": [
        {
          "line": 2,
          "column": 3,
          "sourceName": null
        }
      ],
      "message": "This report does not exist or is not available through Report Check. For further information, please contact us. Code: c7b893ee-63ba-4e03-be84-83ef13da222f"
    }
  ]
}
```

| Possible Cause | Solution |
| --- | ----------- |
| You are using a sandbox key to access a non-sandbox report number. | Obtain a production key. |
| The requested report number does not exist. | Check your entries and try again. |
| The report exists, but has not been returned to the client and is not yet available. | Retry your query after item has been returned to the client. |
| The report exists, but is unavailable for other reasons. | Contact GIA for further information. |

### Item is Undergoing Service

Items that are undergoing servicing by GIA are unavailable through this API. 

```
{
  "data": {
    "report1": null,
  },
  "errors": [
    {
      "path": [
        "report1"
      ],
      "data": null,
      "errorType": null,
      "errorInfo": null,
      "locations": [
        {
          "line": 3,
          "column": 3,
          "sourceName": null
        }
      ],
      "message": "The item with this report number is undergoing service by GIA. Once service is completed and the item is returned, report results will be displayed. Please try your search later."
    }
  ]
}
```

### Invalid key

An invalid key will return a `HTTP 403 Forbidden` and this body content:

```
{
  "message": "Authorization denied."
}
```

| Possible Cause | Solution |
| --- | ----------- |
| Your key is inactive. | Log in to the developer portal and confirm your key. |

### Quota limit exceeded

Submitting a query that exceeds your quota will return `HTTP 200 OK` and an error response in the body of the message.

```
{
  "data": {
    "getReport": null
  },
  "errors": [
    {
      "path": [
        "getReport"
      ],
      "data": null,
      "errorType": null,
      "errorInfo": null,
      "locations": [
        {
          "line": 2,
          "column": 3,
          "sourceName": null
        }
      ],
      "message": "QuotaUsageLimit"
    }
  ]
}
```

| Possible Cause | Solution |
| --- | ----------- |
| Your quota limit has been consumed or has expired. | Add additional lookups through the developer portal. |

### Newer report issued

From time to time, GIA re-examines items and issues new reports on those items. In those cases a query to the original report will be redirected to the newer report. Details will be returned in the `info_message` field.

### Asset Link Expired

## Providing Feedback

Your feedback will be extremely helpful for future improvements to the GIA Report Results API. Please let us know of any suggestions, ideas, or bugs that you encounter. You can find us at [GIA.edu/contactus](https://www.gia.edu/contactus).

If you encounter an unexpected error condition in the GIA Report Results API, please include the error code returned in the response.
