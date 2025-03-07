---
icon: "cloud"
title: "API"
description: "API descriptions"
---

# VAT return Validation and Submission API

## Changelog

| Dato       | Hva ble endret?                                           |
| :--------- | :-------------------------------------------------------- |
| 2021.06.17 | Updated documentation for [feedback](#retrieve-feedback). |
| 2021.07.05 | Corrected the datatype for when uploading attachments.    |
| 2021.07.05 | Changed the URL for validation to the correct value       |

## Introduction

VAT returns to be sent to Skatteetaten from an end-user
system (SBS) should use these APIs:

1.  Skatteetaten VAT return Validation API
2.  Skatteetaten Altinn3 VAT-Return-Submission API

as described below.

## Overall process for Submission and validating VAT return

Submission of VAT returns are done with the Skatteetaten
Altinn3 App Instance API. The Instance API is a generic Altinn Api and its detailed description can be found here <a href="https://docs.altinn.studio/teknologi/altinnstudio/altinn-api/app-api/instances/" target="_blank">Instance API</a>. In-depth knowledge of this API is not required as this documentation
covers the needed sequence for submitting VAT returns.

It is recommended to use the <a href="https://skd.apps.tt02.altinn.no/skd/mva-melding-innsending-etm2/swagger/index.html" target="_blank">swagger documentation</a> along with this API description.

In addition, there are running examples of VAT return filing that use Jupyter Notebook and Python here: <a href="https://skatteetaten.github.io/mva-meldingen/english/test/" target="_blank">Test</a>

The submission process is performed with a sequence of calls to the Instance API and is described in detail below the sequence diagram, and it is as follows:

1. Authentication
   - Change ID-Porten token to Altinn token
2. Validation
3. Data filling towards Altinn3-App
   - Create instance towards Altinn3-App
   - Upload vat-return submission towards Altinn3-App
   - Upload vat-return towards Altinn3-App
   - Upload attachements towards Altinn3-App
4. Complete data filling towards Altinn3-App
5. Complete submission towards Altinn3-App
6. Retrieve feedback towards Altinn3-App

The Instance VAT Filing API is available at this URL:

```
instanceApiUrl = "https://skd.apps.tt02.altinn.no/skd/mva-melding-innfiling-etm2/instances"
```

In the following sequence diagram, the application URL will be hidden, so if `POST: /intances/` is written it is
implicitly `POST: instanceApiUrl`

![](Vat-Return-Submission-Sequence-Diagram.png)

## Authentication

### Change ID-Porten token to the Altinn token

To change ID-Porten token, make the following calls:

```JSON
GET `https://platform.tt02.altinn.no/authentication/api/v1/exchange/id-porten`
    HEADERS:
        "Authorization": "Bearer " + "{IDPortenToken}"
           "content-type": "application/json"
```

and the response content will be a brand new altinnToken used as the
Bearer token in the subsequent requests. The token currently has a duration of 8 hours. Later in 2021, Altinn3 will offer refresh tokens so that one login can last for up to 3 months.

## Validate tax return

The service validates the content of a tax return and returns a response
with any errors, deviations and warnings. The service will do the
following:

1.  Check the message format.
2.  Control the content and composition of the elements in the VAT
    return.

Skatteetaten assumes that the validation service is called in
advance of submitting the VAT return. This ensures that the VAT return
has the correct format, content and increases the probability that
the VAT return will be approved upon submission.

**URL** : `POST https://<env>/api/mva/mva-melding/valider`

Where `<env>`is an environment-specific address
e.g.`mp-test.sits.no`

**Body** :

- According to XSD:<a href="https://github.com/Skatteetaten/mva-meldingen/tree/master/docs/english/informasjonsmodell/xsd/no.skatteetaten.fastsetting.avgift.mva.skattemeldingformerverdiavgift.v1.0.xsd" target="_blank">Skattemeldingformerverdiavgift.v1.0</a>

**Example** : Submitting XML in invalid format

POST <a href="https://mp-test.sits.no/api/mva/mva-melding/valider" target="_blank">https://mp-test.sits.no/api/mva/mva-melding/valider </a>

Header: `Content-Type: application/xml`

With content (http body) that does not pass <a href="https://github.com/Skatteetaten/mva-meldingen/tree/master/docs/english/informasjonsmodell/xsd/no.skatteetaten.fastsetting.avgift.mva.skattemeldingformerverdiavgift.v1.0.xsd" target="_blank">XSD</a> validation:

```xml
<?xml version='1.0' encoding='UTF-8'?>
    <mvaMeldingDto xmlns="no:skatteetaten:fastsetting:avgift:mva:skattemeldingformerverdiavgift:v1.0">

    </mvaMeldingDto>
```

### Response

status: 200
Content (body)

```xml
<valideringsresultat>
        <status>UGYLDIG_SKATTEMELDING</status>
        <valideringsfeil>
            <stiTilFeil>//innfiling</stiTilFeil>
            <valideringsDetaljer>
                <feilmelding>Mva meldingen må være på gyldig format og passere XML skjema valideringen</feilmelding>
                <alvorlighetsgrad>UGYLDIG_SKATTEMELDING</alvorlighetsgrad>
                <avvikKode>MvaMeldingsinnhold_Xml_SkjemaValideringsfeil</avvikKode>
                <informasjon>cvc-complex-type.2.4.b: The content of element 'mvaMeldingDto' is not complete. One of
                    '{"no:skatteetaten:fastsetting:avgift:mva:skattemeldingformerverdiavgift:v1.0":innfiling}' is expected.
                </informasjon>
            </valideringsDetaljer>
        </valideringsfeil>
    </valideringsresultat>
```

## Create Instance

An instance is an object in altinn that follows the process and the data model defined by the application. Skatteetaten has a VAT-Return-Submission application which has a process with currently three
steps for submitting. The steps are uploading data, confirm and feedback.

In addition to being an object, an instance has a data object defined by a data model in the app.

Once an instance is created, it will be possible to update the data
object of the instance and add other data objects to the app's data
model. This is done in the next step.

To create an instance, you perform a POST request at the instanceApiUrl with an
`instanceOwner` object where you enter the organization
number for the organization that a VAT return is to be sent on behalf of:

```JSON
POST {instanceApiUrl}
    HEADERS:
        "Authorization": "Bearer " + "{altinnToken}"
        "content-type": "application/json"
    CONTENT/BODY:
        {
            "instanceOwner": {
                "organisationNumber": "{organizationNumber}"
                }
        }
```

This request will create the instance and return the following response

### Response

```JSON
Response HTTPCode: 201 (OK)
    Content:
    {
        "id": "{partyId}/{instanceGuid}",
        "instanceOwner": {
            "partyId": "{partyId}",
            "organisationNumber": "{organizationNumber}"
        },
        "appId": "skd/{ApplicationName}",
        "org": "skd",
        "selfLinks": {
            "apps": "{instanceUrl}", // the instanceUrl for the app
            "platform": "{platformUrl}" // the altinn3 plattform url for the instance
        },
        "data": [
            {
                "id": "{dataGuid}", // {dataGuid} can be used in the next step
                "instanceGuid": "{instanceGuid}",
                "dataType": "no.skatteetaten.fastsetting.avgift.mva.mvameldinginnsending.v0.1",
                "contentType": "application/xml",
                "blobStoragePath": "skd/{ApplicationName}/{instanceGuid}/data/{dataGuid}",
                "selfLinks": {
                    "apps": "{instanceDataAppUrl}", // {instanceDataAppUrl} can be used in the next step
                    "platform": "{instanceDataPlatformUrl}"
                },
                "size": 273,
                "locked": false,
                "refs": [],
                "isRead": true,
                "created": "2021-03-01T08:15:25.1139057Z",
                "createdBy": "86257",
                "lastChanged": "2021-03-01T08:15:25.1139057Z",
                "lastChangedBy": "86257"
            }
        ]
        // the rest of the object is snipped for documentation purposes
    }
```

The rest of the requests in the sequence for the submission use
`instanceUrl`. This can be found from the response at the
creation of the instance. See the example of the response above.

`instanceUrl` could either be picked from
`selflinks.apps` or derived from
`instanceApiUrl/{partyId}/{instanceGuid}`, where
`{partyId}` and `{instanceGuid}` can be
found in the `id` field in the returned instance.

Example instanceUrl:
`https://skd.apps.tt02.altinn.no/skd/mva-melding-innfiling-etm2/instances/3949387/abba061g-3abb-4bab-bab8-c9abbaf1ed50/data/28abba46-dea8-4ab7-ba90-433abba906df`

### Error messages

_Response 400 - Bad Request:_ <br>
Example Value

```JSON
{
  "type": "string",
  "title": "string",
  "status": 0,
  "detail": "string",
  "instance": "string"
}
```

_Response 403 - Forbidden:_ <br>
Example Value:

```JSON
{"type":"https://tools.ietf.org/html/rfc7231#section-6.5.3","title":"Forbidden","status":403,"traceId":"00-44eab35cb9ca2049b24de316f380a774-a724e045b09dfc44-00"}
```

This error message could occur if you try to create an instance where the logged-in user does not have the necessary rights to the organisation number defined in the request header.
This can also occur if the user does not have the correct roles necessary for creating an instance.

_Response 404 - Not Found:_ <br>
Example Value:

```JSON
"Cannot lookup party: Failed to lookup party by organisationNumber: 123456789. The exception was: 404 - Not Found - "
```

This error message can occur when you set an invalid organisation number in the request header.

## Upload VAT return submission

MvaMeldingInnsending is a data type for metadata for the VAT return submission.
The object to populate is created during the instantiation and can
be found in the instance object's `data` list and has
`"dataType": "no.skatteetaten.fastsetting.avgift.mva.mvameldinginnsending.v0.1"`.
Since this object already exists when uploading VAT return submission,
PUT is used to update the data element.

The model for VAT return submission can be found here:
<a href="https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/xsd/no.skatteetaten.fastsetting.avgift.mvamvameldinginnsending.v1.0.xsd" target="_blank">no.skatteetaten.fastsetting.avgift.mva.mvameldinginnsending.v1.0.xsd</a>

Url to MvaMeldingInnsending has this structure:

```
vatReturnSubmissionUrl = {instanceApiUrl}/{partyId}/{instanceGuid}/data/{dataGuid}
```

where `{dataGuid}` is the ID of the data object of the
instance.

There are 2 ways to derive the `vatReturnSubmissionUrl` and
both use the instance's data list element that has the data type
`no.skatteetaten.fastsetting.avgift.mva.mvameldinginnsending.v0.1`.
When the instance is created, there is only one element in the list.

From the data element you can either:

- merge `{dataGuid}` that exists as value in
  `"id"` in the structure above,
  - alternatively use `{instanceApiUrl}/data/{dataGuid}`
- or use the `selfLinks.apps` value
  `{instanceDataAppUrl}`, as shown in the instance
  response in the previous step.
  - `vatReturnSubmissionUrl = {instanceDataAppUrl}`

You upload VAT return submission by using the data api for the instance:

```
PUT {vatReturnSubmissionUrl}
    HEADERS:
        "Authorization": "Bearer " + "{altinnToken}"
        "content-type": "text/xml"
```

```xml
Content:
    <?xml version="1.0" encoding="UTF-8"?>
    <mvameldinginnsending>
        ...
    </mvameldinginnsending>
```

Example of xml file for VAT return submission can be found under <a href="https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/eksempler/melding/" target="_blank">Test</a>.

### Error Messages

_Response 403 - Forbidden:_ <br>
If the logged-in user attempt to upload a file to the instance, but the person does not have the correct roles, you will get the response code 403 in return.

## Upload VAT return

The model is found here:
<a href="../informasjonsmodell/xsd/no.skatteetaten.fastsetting.avgift.mva.skattemeldingformerverdiavgift.v1.0.xsd" target="_blank">no.skatteetaten.fastsetting.avgift.mva.skattemeldingformerverdiavgift.v1.0.xsd</a>

The URL for uploading the VAT return has this structure:

### Error Messages

_Response 403 - Forbidden:_ <br>
If the logged-in user attempt to upload a file to the instance, but the person does not have the correct roles, you will get the response code 403 in return.

```
{instanceUrl}/data?datatype=mvamelding
```

The VAT return is uploaded with the following request to the instance data api:

```JSON
POST {instanceUrl}/data?datatype=mvamelding
    HEADERS:
        "Authorization": "Bearer " + "{altinnToken}"
        "content-type": "text/xml"
        "Content-Disposition": "attachment; filename=mvaMelding.xml"
```

```xml
Content:
    <?xml version="1.0" encoding="UTF-8"?>
    <mvaMeldingDto xmlns="no:skatteetaten:fastsetting:avgift:mva:skattemeldingformerverdiavgift:v1.0">
        ...
    </mvaMeldingDto>
```

This call will upload the xml document to the instance.

## Upload Attachments

It is possible to upload from 0 to 57 attachments, with an individual
size of 25MB.

Url for uploading attachments has this structure:

```
{instanceUrl}/data?datatype=binaerVedlegg
```

The following content types are allowed for attachments:

- text/xml
- application/pdf
- application/vnd.oasis.opendocument.formula
- application/vnd.openxmlformats-officedocument.wordprocessingml.document
- image/jpeg
- image/jpg
- image/png

Attachments are uploaded with the following request to the instance data api:

```JSON
POST {instanceUrl}/data?datatype=binaerVedlegg
    HEADERS:
        "Authorization": "Bearer " + "{altinnToken}"
        "content-type": "application/pdf"
        "Content-Disposition": "attachment; filename=merknaderTilMvaMeldingen.pdf"
    Content:
    {pdf-attachment in binary format}
```

This request will upload the pdf document to the instance.

Remember that `content-type` should be appropriate for
the attachment to be uploaded and that the file name in the
`Content-Disposition` header should be appropriate
and unique. This is the file name Skatteetaten will refer to
for the attachment.

### Error Messages

_Response 403 - Forbidden:_ <br>
If the logged-in user attempt to upload a file to the instance, but the person does not have the correct roles, you will get the response code 403 in return.

## Complete Data Filling

This step uses the process api for the instance and the instance will go to the next step for VAT return filing in the application process.

To complete the data filling, the following call is made to the process api for the instance:

```JSON
PUT {instanceUrl}/process/next
    HEADERS:
        "Authorization": "Bearer " + "{altinnToken}"
        "content-type": "application/json"
```

The submission will now be in the confirmation step.

### Error Messages

_Response 403 - Forbidden:_ <br>
If the logged-in user attempt to update to the next task in the instance process, but does not have the correct roles, you will get the response code 403 in return.

_Response 409 - Conflict:_ <br>
Example Value

```JSON
{
  "type": "string",
  "title": "string",
  "status": 0,
  "detail": "string",
  "instance": "string"
}
```

```
"Valideringsfeil: Organisasjonsnummeret i instansen er forskjellig fra organisasjonsnummeret i MvaMeldingInnsending (\"konvolutt\")"
```

You will get this error message if the organisation number used when creating the instance is different from the organisation number defined in vat-return submission.

```
"Valideringsfeil: Organisasjonsnummeret i MvaMeldingInnsending (\"konvolutt\") er forskjellig fra organisasjonsnummeret i {filnavn}"
```

You will get this error message if the organisation number defined in vat. return submission is different from the organisation number defined in vat-return.

```
"Valideringsfeil: Liste med vedlegg definert i MvaMeldingInnsending (\"konvolutt\") er forskjellig fra listen med vedlegg som er lastet opp i instansen."
```

If the list of attachments defined in at-return submission is different from the attachments uploaded to the instance, you will get this error message.

```
"Valideringsfeil: Meldingskategorien i MvaMeldingInnsending (\"konvolutt\") er forsjellig fra Meldingskategorien i {filnavn}"
```

This error message will occur if the value of the field message category in vat-return submission is different from the message category in the vat-return.

**Validation Service**

```
"Valideringsfeil: Ugyldig mva-melding"
```

The app will also call the validation service during the completion of the data filling step. If the vat-return is invalid,
it will return a 409 error message, and the validation result in xml as content.

Example value:

```XML
<valideringsresultat>
    <status>UGYLDIG_SKATTEMELDING</status>
    <valideringsfeil>
        <stiTilFeil>//innsending</stiTilFeil>
        <valideringsDetaljer>
            <feilmelding>Mva meldingen må være på gyldig format og passere XML skjema valideringen</feilmelding>
            <alvorlighetsgrad>UGYLDIG_SKATTEMELDING</alvorlighetsgrad>
            <avvikKode>MvaMeldingsinnhold_Xml_SkjemaValideringsfeil</avvikKode>
            <informasjon>cvc-complex-type.2.4.b: The content of element 'mvaMeldingDto' is not complete. One of
                '{"no:skatteetaten:fastsetting:avgift:mva:skattemeldingformerverdiavgift:v1.0":innsending}' is expected.
            </informasjon>
        </valideringsDetaljer>
    </valideringsfeil>
</valideringsresultat>
```

You can find an explanation for all the validation rules [here](/english/forretningsregler/).

## Complete vat-return submission

This step uses the process-api for the instance, and it will end the confirmation step for the submission.
It will also update the instance to the next step in the application, the feedback step.

To complete the submission, the following call is used towards the process-api on the instance:

```JSON
PUT {instansUrl}/process/next
HEADERS:
    "Authorization": "Bearer " + "{altinnToken}"
    "content-type": "application/json"
```

The submission will now be in the feedback step.

### Error Messages

_Response 403 - Forbidden:_ <br>
If the logged-in user attempt to update to the next task in the instance process, but does not have the correct roles, you will get the response code 403 in return.

## Retrieve feedback

The Tax Administration has created 2 api-endpoints to simplify the development of this step:

- An endpoint, which returns a status on whether the Tax Administration has provided feedback.
- A synchronous endpoint, which returns the instance when the Tax Administration has processed the vat-return, and provided feedback.

The following sequence diagram shows how to identify if the Tax Administration has provided feedback to a given instance,
and how to retrieve it.
![](Get-Feedback.png)

You can get the status of the feedback by performing a request towards the feedback-api for the instance.

```JSON
GET {instansUrl}/{partyId}/{instanceGuid}/feedback/status
HEADERS:
    "Authorization": "Bearer " + "{altinnToken}"
    "accept": "application/json"
```

If the call is successful it will return a status code 200, and a json object:

```JSON
{
  "isFeedbackProvided":	boolean
}
```

Where `isFeedbackProvided` returns as `true` if feedback has been given, otherwise it will return `false`

To retrieve the instance where the feedback has been provided, use a call towards the synchronous API-endpoint:

```JSON
GET {instansUrl}/{partyId}/{instanceGuid}/feedback
HEADERS:
    "Authorization": "Bearer " + "{altinnToken}"
    "accept": "application/json"
```

This endpoint will return the instance when the Tax Administration has given feedback,
and it will contain data elements for all the feedback files from the Tax Administration.

**Note: Latter mentioned endpoint should only be used in the following situations:**

- End user is waiting on feedback.
- After the status-endpoint has returned `isFeedbackProvided : true`

### Error Messages

_Response 400 - Bad Request:_ <br>
Example Value

```JSON
{
  "type": "string",
  "title": "string",
  "status": 0,
  "detail": "string",
  "instance": "string"
}
```

_Response 403 - Forbidden:_ <br>
This error message will occur if the logged-in user attempt to retrieve the instance, but the person does not have the correct roles.

_Response 404 - Not Found:_ <br>
Example Value

```JSON
{
  "type": "string",
  "title": "string",
  "status": 0,
  "detail": "string",
  "instance": "string"
}
```

### Feedback files

Once the Tax Administration has given feedback, the files for the feedback can be downloaded from the instance.

Example of feedback files given for a submission on the 17.06.2021 <a href = "https://github.com/Skatteetaten/mva-meldingen/tree/master/docs/documentation/test/eksempler/feedback/exampleSuccessfulFeedback17062021" target = "_ blank ">are located here </a>. These files were downloaded from an instance where the Tax Authorities had given feedback.

The files that can be downloaded will have `dataType`:

- betalingsinformasjon
- valideringsresultat
- kvittering

and download URLs are found in the instance object `data` elements returned from the feedback or instance api as shown below (irrelevant json removed).

Given a `data` element, the file can be retrieved using:

```JSON
GET {selfLinks.apps}
HEADERS:
    "Authorization": "Bearer" + "{altinnToken}"
```

where `selfLinks.apps` is retrieved from the list of data-elements on the instance as shown here:

```JSON
  "data": [
    {
      "id": "82c96a52-ad0b-428f-8005-7f214daf367e",
      "instanceGuid": "55604b08-1690-4a8d-bf6b-95c11dc40c58",
      "dataType": "valideringsresultat",
      "filename": "valideringsresultat.xml",
      "contentType": "text/xml",
      "selfLinks": {
        "apps": "https://skd.apps.tt02.altinn.no/skd/mva-melding-innsending-sit/instances/50267437/55604b08-1690-4a8d-bf6b-95c11dc40c58/data/82c96a52-ad0b-428f-8005-7f214daf367e",
        "platform": "https://platform.tt02.altinn.no/storage/api/v1/instances/50267437/55604b08-1690-4a8d-bf6b-95c11dc40c58/data/82c96a52-ad0b-428f-8005-7f214daf367e"
      }
    },
    {
      "id": "726a315f-7e5e-4514-8ef1-5eda624407d4",
      "instanceGuid": "55604b08-1690-4a8d-bf6b-95c11dc40c58",
      "dataType": "betalingsinformasjon",
      "filename": "betalingsinformasjon.xml",
      "contentType": "text/xml",
      "selfLinks": {
        "apps": "https://skd.apps.tt02.altinn.no/skd/mva-melding-innsending-sit/instances/50267437/55604b08-1690-4a8d-bf6b-95c11dc40c58/data/726a315f-7e5e-4514-8ef1-5eda624407d4",
        "platform": "https://platform.tt02.altinn.no/storage/api/v1/instances/50267437/55604b08-1690-4a8d-bf6b-95c11dc40c58/data/726a315f-7e5e-4514-8ef1-5eda624407d4"
      }
    },
    {
      "id": "cbce850a-a887-4598-aea4-710ea9ffdc7d",
      "instanceGuid": "55604b08-1690-4a8d-bf6b-95c11dc40c58",
      "dataType": "kvittering",
      "filename": "kvittering.pdf",
      "contentType": "application/pdf",
      "selfLinks": {
        "apps": "https://skd.apps.tt02.altinn.no/skd/mva-melding-innsending-sit/instances/50267437/55604b08-1690-4a8d-bf6b-95c11dc40c58/data/cbce850a-a887-4598-aea4-710ea9ffdc7d",
        "platform": "https://platform.tt02.altinn.no/storage/api/v1/instances/50267437/55604b08-1690-4a8d-bf6b-95c11dc40c58/data/cbce850a-a887-4598-aea4-710ea9ffdc7d"
      }
    }
  ]
```
