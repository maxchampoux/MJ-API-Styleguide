# MJ-API-Styleguide
Styleguide on how to implement APIs

# Introduction
The Mailjet platform is a collection of reusable services that encapsulate well-defined business capabilities. Developers are encouraged to access these capabilities through Application Programming Interfaces (APIs) that enable consistent design patterns and principles. This facilitates a great developer experience and the ability to quickly compose complex business processes by combining multiple, complementary capabilities as building blocks.

Mailjet APIs follow the [RESTful][https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm] architectural style as much as possible. To support our objectives, we will develop a set of rules, standards, and conventions that apply to the design of RESTful APIs. These will be used to help design and maintain hundreds of APIs that have evolved over several years to meet the needs of a wide variety of use cases.

We are working on these guidelines to ensure good API design practices in future projects led within Mailjet's organization. We have pulled extensively from the broader community and believe that it is important to give back. The documentation is as generic as possible to make it easier to incorporate into the guidelines you use in your projects. If you have any updates, suggestions, or additions that you would like to contribute, please feel free to submit a PR or create an issue.

### Contributors

Emmanuel Boisgontier, George Goranov, Ivaylo Ivanov, Maxime Champoux.

### To do List

* Basic Authentication
* API Versioning (in URI, when to version?)
* Deprecation

### Planning contributions

Every Friday, we will organize a API Design committee in order ot validate contributions made earlier.
To keep a certain pace of contribution, we will impose at least 1 contribution per week made by a member of the team.

| Contributors | Date Committee | Topic |
|---|---|---|
| Maxime | 1 Dec. 2017 | Asynchronous export process. |
| Ivaylo | 8 Dec. 2017 | To be define |
| Emmanuel | 15 Dec. 2017 | To be define |
| George| 22 Dec. 2017 | To be define |

# Reference
https://github.com/paypal/api-standards/blob/master/api-style-guide.md

# Table Of Contents

* [Asynchronous Process](#asynchronous-process)
    * [Export](#export)

<h1 id="guidelines">Guidelines</h1>   

<h2 id="asynchronous-process">Asynchronous Process</h1>   

<h3 id="export">Export</h2>

<h4 id="why-export">Why asynchronous?</h4>

Timeouts and memory constraints limit the size of the datasets that can be retrieved synchronously by the API. Bigger queries have to run in the background and the result set to be stored in a file accessible via the website. 
The filters implemented today for the different resources are inspired by the needs of the website and are not always optimized for big result sets. The objective of asynchronous export is to reduce the number and the range of filters and to optimize the returned data as a function of the filters. The use of the Export action allows us to return properties different from the resource itself and modulate them according to the filter used.  

<h4 id="implementation-export">Implementation export</h4>

API calls that support async. execution implement the Export action that causes it to execute the query via a batch job.
Only Post Method is support to create the appropriate batch job.

```
Method: POST
Path: /{resource}/export
```

In addition to parameters specific to each query, an export asynchronous process contains global parameters that are common to all exports, listed below:

**Global Parameters:**

| Field | In | Type | Required | Description |
|-------|------|------|----------|-------------|
| ExportFormat | Body | String | Required | Output file format. Default value is `csv`. Can also be `json` or `list`. |
| FieldSeparator | Body | String (1 char) | Optional | Hex value used to separate the fields. Default value `,` (comma). |
| FieldNames | Body | Array [String (1 - 128 char)] | Optional | Strings to be used as column headers. Name of fields to be substituted in the query. They have the format $fieldN$ in the query. 
The array has the following format: {"field1":"label1","field2":"label2"}. The key represents the field name (in the query or extra) and the value represents the label to use in the final exported file. If a field is not listed in that collection, the label to use is the field name.
The default value is an empty collection {}. |
| Headers | Body | Array [String (1-28)] | Optional | 
Strings to be used as column headers. The array has the following format: {"field1":"label1","field2":"label2"}. The key represents the field name (in the query or extra) and the value represents the label to use in the final exported file. If a field is not listed in that collection, the label to use is the field name.
The default value is an empty collection {} |
| FieldValues | Body | Array [String (1-28)] | Optional | The replacement values for the fields defined in FieldNames. The array size of FieldValues has to match FieldNames. |
| Charset | Body | String | Optional | Changes the encoding of the output file. Supported values `utf8` and `utf8bom`; As `utf8` is the default. |
| TimezoneOffset | Body | String | Optional | Timezone offset for formatting dates according to the locale. Allowed values are between -12 and +12, default 0. |
| TimeFormat | Body | String | Optional | Format of the date time of the result columns. Default value: “hh:nn”
Allowed values can be found here: DateTime Type => Link. |
| DateTimeFormat | Body | String | Optional | Format of the date time of the result columns. Default value: “” (empty string). Allowed values defined by DateTime Type => Link. |
| Locale | Body | String | Optional | Export locale for the csv format. Only `en_US`is supported. |
| ListId | Body | UUID | Optional | Reference de la liste cible à modifier. ID of the list to import into the current list. |
| Action | Body | String | Optional | Action to apply on imported contacts. Values: `addforce`, `addnoforce`, `unsub`, `duplicate-override`, `duplicate-no-override` |

**Returns:**
| Field | Type | Description |
|-------|------|-------------|
| JobId | UUID | Reference of the Job ID to follow. |










