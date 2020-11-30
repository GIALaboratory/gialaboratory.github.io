---
title: Documentation
---

# GIA Report Results API Documentation
{:.no_toc}

The GIA Report Results API enables applications to integrate with GIA's database to verify grading information and retrieve assets associated with a report. 

## Contents 
{:.no_toc}

* TOC
{:toc}

## Concepts

Read this guide for details on how to query a report number and parse the response.

#### GraphQL

The GIA Report Results API uses [GraphQL](https://graphql.org/). GraphQL is a query language supported by all major programming languages.

GraphQL tools provide documentation navigators to help you explore the API. To learn more, try our [Quickstart Guide](/report-results/quickstart).

#### Report Results

The GIA Report Results API returns results for GIA's reports and services through an endpoint called `getReport`.

A call to `getReport` returns a `GradingReport` with fields common to all reports. These include report number, report date, and report type. Different report types have distinct fields in their results. For example, a diamond report has different attributes than a sapphire report. 

The `results` field contains a [union type](https://graphql.org/learn/schema/#union-types) called `ReportResults` that returns one of the following concrete types: 

* `DiamondGradingReportResults` for natural diamond reports, such as Diamond Dossier, Diamond Grading, and Colored Diamond Grading reports.
* `PearlIdentReportResults` for pearl reports.
* `LabGrownDiamondGradingReportResults` for lab-grown diamond reports.
* `IdentificationReportResults` for colored stones reports.
* `MeleeServiceResults` for results on the Melee Analysis service.
* `NarrativeReportResults` for results that do not apply to the other formats.

Use a [conditional fragment](https://graphql.org/learn/queries/#inline-fragments) (such as `... on DiamondGradingReportResults`) to access these fields.

__Pro Tip__: Use the [__typename](https://graphql.org/learn/queries/#meta-fields) meta field to determine how to handle the data on your client.

```graphql
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

```json
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

#### Data Types

Most results fields are [String](https://graphql.org/learn/schema/#scalar-types) types to accommodate the text as it appears on a grading report. For example, `Internally Flawless` clarity grades will be spelled out rather than abbreviated `IF`.

In most cases, abbreviated versions are also available. These [Enums](https://graphql.org/learn/schema/#enumeration-types) return one of the allowed values. Look for fields that end with `_code`. Examples are `report_type_code`, `clarity_grade_code`, and `color_grade_code`.

Concatenated fields are also available individually. For example, measurements for round diamonds are `minimum diameter - maximum diameter x depth`. The numerical fields arise in the `RoundMeasurements` type.

#### Asset Links

The [`links`](https://gialaboratory.github.io/report-results/reference/links.doc.html) field holds links to supplementary assets. 

The availability of assets depends on the report type and other factors. Fields are `null` if an asset is not available.

Links are active for 60 minutes from the API call. The API returns an error if you attempt to access assets after this period.

#### Checking for Stale Reports

Periodically review the status of any reports cached in your systems to avoid presenting outdated information.

Pass a report number and date to `isReportUpdated` to determine whether the report was updated after that date. There is no need for you to query `GetReport` for updated data if the response is `false`.

Calling `isReportUpdated` does not incur a lookup.

```graphql
{
  isReportUpdated(report_number: "6203489265", report_date: "2020-01-01") {
    report_updated
  }
}
```

returns

```json
{
  "data": {
    "isReportUpdated": {
      "report_updated": false,
    }
  }
}
```

## Connecting to the API

### Crafting Your Request

Construct the request body and convert it to JSON before sending it to the endpoint as a `POST` request with the proper headers. 

##### Step 1: Craft a valid query using a GraphQL-aware tool.

Use a tool such as Insomnia to validate your query. Instructions are in the [Quickstart Guide](/report-results/quickstart).

We'll use this example query throughout this section.

```graphql
query ReportQuery($ReportNumber: String!) {
    getReport(report_number: $ReportNumber){
        report_number
        report_date
        report_type
        results {
            __typename
            ... on DiamondGradingReportResults {
                shape_and_cutting_style
                carat_weight
                clarity_grade
                color_grade
            }
        }
        quota {
            remaining
        }
    }
}
```

Note the use of the `ReportNumber` query variable.
```json
{
  "ReportNumber": "2141438171"
}
```

Be sure your query is successful before proceeding to the next step.

##### Step 2: Construct a GraphQL request body using your language's idioms

The body you send to the API must conform to [GraphQL standards](https://graphql.org/learn/serving-over-http/#post-request). 

The best practice is to construct this structure using the features of your language before converting it to JSON.

The API expects this structure:
```json
{
  "query": "YOUR_QUERY_GOES_HERE", 
  "variables": { 
    "YOUR_VARIABLE_NAME_GOES_HERE": "YOUR_VARIABLE_VALUE_GOES_HERE" 
  } 
}
```

You know the elements (query, variable name, and value) from Step 1. In the example above, `ReportNumber` is the variable, and `"2141438171"` is the value.

One way to construct the request body is to use Dictionary elements:

1. Create a dictionary object called `query_variables` of type (`String`, `String`).
2. Insert an element into `query_variables` with `ReportNumber` as the index and  `"2141438171"` as the value.
3. Create a second dictionary object called `body` of type (`String`, `Object`)
4. Insert an element into `body` with `query` as the index and the query you drafted in Step 1 as the value.
5. Insert a second element into `body` with `variables` as the index and `query_variables` as the value

```csharp
// Example in C#

var query = @"
query ReportQuery($ReportNumber: String!) {
    getReport(report_number: $ReportNumber){
        report_number
        report_date
        report_type
        results {
            __typename
            ... on DiamondGradingReportResults {
                shape_and_cutting_style
                carat_weight
                clarity_grade
                color_grade
            }
        }
        quota {
            remaining
        }
    }
}
";

// Set the report number to lookup
var reportNumber = "2141438171";

// Construct the request body to be POSTed to the graphql server
var query_variables = new Dictionary<string, string>
{
    { "ReportNumber", reportNumber}
};
var body = new Dictionary<string, object>
{
    { "query", query },
    { "variables", query_variables }
};

```

##### Step 3: Convert the request body object to JSON

Your programming language has a library for serializing objects to JSON. 

```csharp
// Example in C#: 

string json = JsonSerializer.Serialize(body);
```

> __Tip__: Avoid hand-coding your JSON body. Use the tools your language provides.

##### Step 4: Construct the HTTP request

The API requires two HTTP headers:

* __Authorization__: Set this to the API Key you received at signup
* __Content-Type__: Set this to `application/json`

> __Do not embed API keys directly in code__: Embedded API keys can be accidentally exposed. Store API keys in environment variables or files outside of your application's source tree.

```csharp
// Example in C# 

var client = new WebClient()

// key contains the API key
client.Headers.Add(HttpRequestHeader.Authorization, key);
client.Headers.Add(HttpRequestHeader.ContentType, "application/json");

```

> __Tip__: You do not need a specialized GraphQL client to query the API. You can use the HTTP client provided by your programming language.

##### Step 5: POST the HTTP request

You are now ready to send the request to the API.

* HTTP method must be POST
* The address is the URL given when you signed up for the API
* The body is the JSON you serialized in Step 3

```csharp
// Example in C#

// Send the payload as a JSON to the endpoint
var response = client.UploadString(url, json);
```

### Parsing the Response

#### Overview

The GIA Report Results API returns responses in [JSON](https://www.json.org/). The shape of the JSON response aligns with the GraphQL query you submitted.

#### Checking for Errors

Error checking the GraphQL response requires attention at several points. 

1. Ensure you received an `HTTP 200 OK` response.
2. Check the GraphQL [errors](https://graphql.org/learn/serving-over-http/#response) field for any other problems.  

For more information, see [Error Conditions](#error-conditions).

#### Working with the Response

The tools you use depend on the language you are using. With statically-typed languages such as C# and Java, you will either (a) deserialize the JSON object into a native class or (b) use a library that allows you to work with the JSON document natively.

Option (a) can be tricky given the polymorphic nature of the `ReportResults` object. This approach is not discussed further in this document.

## Platform Overview

GIA has engineered this system for the utmost performance, security, and availability. This section contains details for connecting to the API reliably and securely.

### API Plans and Quota Monitoring

A plan enables lookups in the GIA Report Results API. 

Each plan has report lookups associated with it. You will no longer be able to retrieve report results when this quota runs out. Track your quota and take action when necessary.

There are multiple ways to retrieve your quota:

1. Call `getQuota` to get your remaining allowance. Calling getQuota does not affect your remaining quota.
2. Call `getReport` and request the `quota { remaining }` field as part of the response. 
3. View your quota by signing in to your GIA Report Results API dashboard.

> __Note:__ The system calculates your quota within each report lookup. If you request multiple reports in a single request, the quota remaining may be misleading. The precise quota remaining is the minimum of all `remaining` values in the response.
>
> Calling `getQuota` alone (that is, as a request separate from any calls to `getReport`) will always return the exact number of report lookups remaining.

### API Keys

API keys authenticate and authorize requests to the API. A plan may have multiple API keys. You may revoke any key without affecting other keys on the plan.

> __Important:__ Securely store these keys and do not share them! You are responsible for taking all precautions to safeguard access to the keys.

### Sandbox Environment

GIA will issue you a sandbox plan and associated key as soon as your API application is submitted. This key can access a limited set of reports that represent the variety of results you can expect from the API. 

See [Sandbox Reports](https://gialaboratory.github.io/report-results/sandbox-reports/) for a list of reports available under the sandbox plan.

There is no charge to add lookups to your sandbox API plan. Please contact us for assistance.

### Moving to Production

You will be able to add a production API plain once GIA approves your application. API keys associated with this plan may access the full set of reports available in GIA's database.

### Quota Monitoring

Check your remaining quota by using `getQuota`. Checking your quota does not affect your remaining quota.

```graphql
{
  getQuota{
    remaining
  }
}
```

You can also get your remaining quota with each query to `getReport`.

```graphql
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

```json
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

#### Quota Buckets

Each purchase of lookups in your API plan will create a new "quota bucket". You may query all quota buckets for the initial allocation, the lookups remaining, and the expiration date of the bucket.

```graphql
{
  getQuota {
    remaining
    buckets {
      id
      initial
      remaining
      expiration_date
      status
    }
  }
}
```

returns

```json
{
  "data": {
    "getQuota": {
      "remaining": 1284,
      "buckets": [
        {
          "id": "2e35ceaf-7957-4b7b-94b8-8253df37d08e",
          "initial": 5000,
          "remaining": 1284,
          "expiration_date": "2021-02-04T15:19:00Z",
          "status": "ACTIVE"
        }
      ]
    }
  }
}
```

### Rate Limits

Rate limiting is applied based on the authorization key. You have sent too many requests if the API returns status code `429`. Check the `Retry-After` header for the number of seconds to wait before resending your request.

### System Status

The GIA Report Results API is engineered for high availability. You may view our current system status and historical uptime at [status.gia.edu](https://status.gia.edu).

__Important:__ Subscribe to notifications at [status.gia.edu](https://status.gia.edu). These notifications are the method we will use to update you on planned maintenance or unplanned incidents.

### Migrating from the Legacy Report Check API

Some fields are coded differently in this API. In most cases, we have provided both the abbreviated and full text fields.

Example:

| Field | Legacy Report Check API | Report Results API |
|-------|-------------------------|--------------------|
| Culet | VSM | Very Small |
| Girdle | STK to THK, F | Slightly Thick to Thick, Faceted |

### Error Conditions

#### Report not found

The API returns an `HTTP 200 OK` response if the report is unavailable. The `errors` object includes a message and an error code.

```json
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
      "errorType": "REPORT UNAVAILABLE",
      "errorInfo": "REPORT NOT FOUND",
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
| The requested report number does not exist. | Check your entries and try again. |
| The report exists, but has not been returned to the client and is not yet available. | Retry your query after the item is returned to the client. |
| The report exists but is unavailable for other reasons. | Contact GIA for further information. |

#### Report Not Found - Forbidden

If you attempt to query a non-sandbox report with a sandbox key, you will receive an error message.

```json
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
      "errorType": "REPORT NOT FOUND",
      "errorInfo": "FORBIDDEN",
      "locations": [
        {
          "line": 2,
          "column": 3,
          "sourceName": null
        }
      ],
      "message": "This API key does not have permission to access this report. If this is a sandbox API key, please obtain a production API key and retry your request."
    }
  ]
}
```

| Possible Cause | Solution |
| --- | ----------- |
| You are using a sandbox key to access a non-sandbox report number. | Obtain a production key. |

#### Item is Undergoing Service

Items that are currently being serviced by GIA are unavailable through this API. 

```json
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
      "errorInfo": REPORT IN HOUSE
      "errorType":  REPORT UNAVAILABLE
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

| Possible Cause | Solution |
| --- | ----------- |
| The item is being serviced by GIA. | Wait until the item has been completed and returned to the client. |

#### Invalid key

An invalid key returns an `HTTP 403 Forbidden` response and this body content:

```json
{
  "message": "Authorization denied."
}
```

| Possible Cause | Solution |
| --- | ----------- |
| Your key is inactive. | Log in to the developer portal and confirm your key. |

#### Quota limit exceeded

Queries that exceed the plan's quota return an `HTTP 200 OK` response. Details appear in the `errors` object.

```json
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
      "errorInfo": PLAN QUOTA MET
      "errorType":  QUOTA REACHED
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

#### Report Number Returned Does Not Match Report Number Requested

From time to time, GIA re-examines items and issues updated reports. In those cases, queries for the original report redirect to the current report. A note appears in the `info_message` field.

| Possible Cause | Solution |
| --- | ----------- |
| A newer report has been issued to replace the original report number. | The current report number is the most accurate data GIA has for this item. |

#### Asset Link Expired

Asset links expire 60 minutes from the time you query the API. Requesting an asset using an expired URL returns `HTTP 403 Forbidden` and a "Request has Expired" message.

```html
<Error>
  <Code>AccessDenied</Code>
  <Message>Request has expired</Message>
  <Expires>2019-09-28T15:47:27Z</Expires>
  <ServerTime>2019-09-28T16:10:51Z</ServerTime>
  <RequestId>F73DF5DE0563DEE8</RequestId>
  <HostId>
    ADy4EQkt1sui/87CpxtQe5CMmBq8fejXckZiaB5Ui4bjfA21ky7Lp4wGBlw47A87haeruBbu90o=
  </HostId>
</Error>
```

| Possible Cause | Solution |
| --- | ----------- |
| The asset link has expired. | Query `getReport` to obtain a new asset link. |

#### MalformedHttpRequestException

```json
{
  "errors" : [ {
    "message" : "Invalid JSON payload in POST request.",
    "errorType" : "MalformedHttpRequestException"
  } ]
}
```

| Possible Cause | Solution |
| --- | ----------- |
| JSON is invalid. | Ensure the JSON you submit is well-formed. |
| Improper nesting of escape characters. | Avoid using string concatenation and check that you are escaping properly for your language. |

## Providing Feedback

Your feedback drives improvements to the GIA Report Results API. Please let us know of any suggestions, ideas, or bugs that you encounter. You can find us at [GIA.edu/contactus](https://www.gia.edu/contactus).

If you encounter an unexpected error condition in the GIA Report Results API, please include the error code returned in the response.
