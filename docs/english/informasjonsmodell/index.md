---
icon: "cloud"
title: "Information models, XSD and encoding"
description: "XSD for VAT return (mva-melding)
- XSD for submission of metadata information 
- encoding in XML-format"
---

# XSD for the VAT return

### Change log

| Date       | What changed?                                                                                                                                                                                                  |
| :--------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2021.06.21 | Updated code list [mvaSpesifikasjon](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/kodelister/mvaSpesifikasjon.xml), correcting "tap på krav" to "tapPåKrav" |
| 2021.08.31 | Updated with description of the fields in the VAT return submission                                                                                                                                            |
| 2021.09.13 | New table describing the usage of SAF-T codes                                                                                                                                                                  |
| 2021.09.21 | Information model for VAT return updated. SAF-T codes not to be reported (0,7, 20,21 and 22) are removed.                                                                                                      |
| 2021.10.12 | Updated code list [merknadTilsvarendeMvaKode](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/kodelister/merknadTilsvarendeMvaKode.xml), including removal of duplicates |

## Version 1.0 of the XSD for the VAT return

Version 1.0 of this XSD is found here [no.skatteetaten.fastsetting.avgift.mva.skattemeldingformerverdiavgift.v1.0](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/xsd/no.skatteetaten.fastsetting.avgift.mva.skattemeldingformerverdiavgift.v1.0.xsd)

Graphical representation of the XSD and encoding for the [VAT return](SkattemeldingForMerverdiavgift2020.png):

![SkattemeldingForMerverdiavgift2020](SkattemeldingForMerverdiavgift2020.png)

The SAF-T specification also contains the codes 0 (No VAT treatment), 7 (No VAT treatment - no turnover according to the VAT legislation), 20 (No VAT treatment), 21 (Basis on import of goods, regular rate) and 22 (Basis on import of goods, reduced rate, middle). These should not be reported in the VAT return

Exapmles of the VAT return in excel:

[Test cases VAT return](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/Testtilfeller%20mva-melding.xlsx?raw=true)

Corresponding example files for VAT return in XML format can be downloaded from the test section: https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/eksempler/melding/

## Description of the fields in the VAT return

### MvaMelding

<table align=center>
  <tr><th style="width:25%" align=left>Field</th><th align=left>Description</th></tr>
  <tr><td>meldingskatergori</td><td>Description: the subtype of the VAT return <br>
	Purpose: to ensure that the user can fulfill their VAT reporting obligations
	</td>
  </tr>
    <tr><td>merknad</td><td>Description: additional information about the content of the VAT return<br>
	Purpose: to ensure that the taxpayer have the possibility to explain their application of law when necessary
	</td>
  </tr>
</table>

### Skattepliktig

<table align=center>
  <tr><th style="width:25%" align=left>Field</th><th align=left>Description</th></tr>
  <tr><td>organisasjonsnummer</td><td>Description: unique identifier for the taxable organisation  <br>
  Purpose: to take care of the rights and obligations of the taxpayer
	</td>
  </tr>	
</table>

### Innsending

<table align=center>
  <tr><th style="width:25%" align=left>Field</th><th align=left>Description</th></tr>
  <tr><td>regnskapssystemrefereanse</td><td>Description: The taxpayers unique reference for the submission <br>
  Purpose: To ensure that the taxpayer and the tax office refer to the same message. 
	</td>
  </tr>
    <tr><td>system</td><td>Description: Name and possible version of accounting system <br>
Purpose: To be able to follow up systematic errors with the system supplier instead of following up each individual taxpayer. 
	</td>
  </tr>
</table>

### Betalingsinformasjon

Oppgis bare dersom det er tilgodebeløp.

<table align=center>
  <tr><th style="width:25%" align=left>Field</th><th align=left>Description</th></tr>
  <tr><td>KID</td><td>Description: The recipient's identification of a payment <br>
Purpose: To be able to pay to the bank accounts that require KID 
</table>

### Skattegrunnlag og beregnet skatt

<table align=center>
  <tr><th style="width:25%" align=left>Field</th><th align=left>Description</th></tr>
  <tr><td>skattleggingsperiode</td><td>Description: the taxation period for which the VAT return applies <br>
Purpose: to ensure consistency between bookkeeping and VAT return period 
	</td>
  </tr>
    <tr><td>fastsattMerverdiavgift</td><td>Description: sum to pay / sum to be refunded <br>
Purpose: to ensure that the correct amount is being paid
	</td>
  </tr>
</table>

### MvaSpesifikasjonslinje

<table align=center>
  <tr><th style="width:25%" align=left>Field</th><th align=left>Description</th></tr>
  <tr><td>mvaKode</td><td>Description: Classification of incoming and outgoing VAT in accordance with the Norwegian SAF-T standard <br>
Purpose: dissemination of which VAT assessments have been carried out
	</td>
  </tr>
  <tr><td>spesifikasjon</td><td>Description: detailing some VAT conditions that are not included in the Norwegian SAF-T standard <br>
Purpose: dissemination of which VAT assessments have been carried out
	</td>
  </tr> 
  <tr><td>mvaKodeRegnskapssystem</td><td>Description: internal VAT code in the accounting system. There may be several internal VAT codes for a VAT code and possibly a specification. In that case, there will be several lines in the VAT message per VATCode and specification; one per combination of VAT code, specification and VAT CodeAccounting system. <br>
Purpose: Two considerations <br>
Users: recognizable in relation to what they see in the accounting system. <br>
The system suppliers will not have to change VAT codes in the systems
	</td>
  </tr>
  <tr><td>grunnlag</td><td>Description: the amount of which VAT is calculated.
The field must not be filled in for input VAT. <br>
Purpose: Basis for control from the Tax Administration
	</td>
  </tr>
  <tr><td>sats</td><td>Description: The VAT rate used in calculating outgoing VAT. 
The field must not be filled in for incoming VAT. <br>
Purpose: To ensure that only valid rates are used for invoicing
	</td>
  </tr>
  <tr><td>merverdiavgift</td><td>Description: fixed VAT <br>
Purpose: to show calculated VAT per line
      </td>
  </tr>  
  <tr><td>merknad</td><td>Description: information about the content of mvaKode <br>
Purpose: to ensure that the taxpayer can explain his own application of the law where necessary 
  </tr>

</table>

## Description of the fields in the VAT return submission

### MvaMeldingInnsending

<table align=center>
  <tr><th style="width:25%" align=left>Felt</th><th align=left>Description</th></tr>
  <tr><td>meldingskategori</td>
    <td>
        Description: the subtype of the VAT return <br>
        Purpose: to ensure that the user can fulfill their VAT reporting obligations
	</td>
  </tr>
  <tr><td>innsendingstype</td>
    <td>
        Description: A VAT return with meldingskategori alminnelig og primær will always be <strong>komplett</strong> <br>
        Purpose: The field is kept so at a later date it can be opened for auditors to comment/write notes on those vat returns (e.g. VAT compensation) that should be approved by an auditor before submission.
</td>
  </tr>
  <tr><td>instansstatus</td>
    <td>
        Description: This field will be removed since we get the required information from the events on the instance. <br>
        This will be done by making the field optional in a transition and removed at a appropriate time.
	</td>
  </tr>
  <tr><td>oprettetAv</td>
    <td>
        Description: This field should contain the name of the logged in user. <br>
        Purpose: The content of this field will be displayed in Altinn.
	</td>
  </tr>
  <tr><td>oprettingstidspunkt</td>
    <td>
        Description: This field will be removed since we get the required information from the events on the instance. <br>
        This will be done by making the field optional in a transition and removed at a appropriate time.
	</td>
  </tr>
</table>

<br>
Identifikator (Either organisasjonsnummer or foedselsnummer)
<table align=center>
  <tr><th style="width:25%" align=left>Felt</th><th align=left>Description</th></tr>
  <tr>
    <td>organisasjonsnummer</td>
    <td>
        Description: unique identifier for the taxable organisation <br>
        Purpose: to take care of the rights and obligations of the taxpayer
	</td>
  </tr>
  <tr><td>foedselsnummer</td>
    <td>
        Description: unique identifier for the taxable organisation <br>
        Purpose: to take care of the rights and obligations of the taxpayer
	</td>
  </tr>
</table>

<br>
Skattleggingsperiode
<table align=center>
  <tr><th style="width:25%" align=left>Felt</th><th align=left>Description</th></tr>
  <tr>
    <td>periode</td>
    <td>
        Description: the taxation period for which the VAT return applies <br>
        Purpose: to ensure consistency between book keeping and VAT return period.
	</td>
  </tr>
  <tr><td>aar</td>
    <td>
        Description: the taxation year for which the VAT return applies <br>
        Purpose: to ensure consistency between book keeping and VAT return period.
	</td>
  </tr>
</table>

<br>
Vedlegg
<table align=center>
  <tr><th style="width:25%" align=left>Felt</th><th align=left>Description</th></tr>
  <tr>
    <td>vedleggstype</td>
    <td>
        Description: Type of attachments which is uploaded to the instance on Altinn.
        Where you can either use <strong>mva-melding</strong> for the VAT return, or <strong>binaerVedlegg</strong> for general attachments.
	</td>
  </tr>
  <tr><td>kildegruppe</td>
    <td>
        Description: Which group the submission comes from. <br>
        Options: <strong>etat</strong>, <strong>sluttbrukersystem</strong>, <strong>sluttbruker</strong> 
	</td>
  </tr>
  <tr><td>opprettetAv</td>
    <td>
        Description: This field should contain the name of the logged in user. <br>
        Purpose: The content of this field will be displayed in Altinn.
	</td>
  </tr>
  <tr><td>opprettingstidspunkt</td>
    <td>
        Description: This field will be removed since we get the required information from the events on the instance. <br>
        This will be done by making the field optional in a transition and removed at a appropriate time.
	</td>
  </tr>
</table>

<br>
Vedleggsfil
<table align=center>
  <tr><th style="width:25%" align=left>Felt</th><th align=left>Description</th></tr>
  <tr>
    <td>filnavn</td>
    <td>
        Description: Name of the file which is uploaded as attachment <br>
	</td>
  </tr>
  <tr><td>filekstensjon</td>
    <td>
        Description: The file extension for the file which is uploaded as attachment <br>
	</td>
  </tr>
  <tr><td>filinnhold</td>
    <td>
        Description: description of the contents of the file which is uploaded as attachment <br>
	</td>
  </tr>
</table>

## Encoding

| Group and code                                           | Description of code, deductions and specification                                          |
| -------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Sales of goods and services in Norway                    |                                                                                            |
| 3                                                        | Sales and withdrawals of goods and services (standard rate)                                |
| Specification line for code 3                            | withdrawal                                                                                 |
| 31                                                       | Sales and withdrawals of goods and services (middle rate)                                  |
| Specification line for code 31                           | withdrawal                                                                                 |
| 33                                                       | Sales and withdrawals of goods and services (low rate)                                     |
| Specification line for code 33                           | withdrawal                                                                                 |
| 5                                                        | Sales of goods and services exempt from value added tax                                    |
| Specification line for code 5                            | withdrawal                                                                                 |
| 6                                                        | Sales of goods and services outside the scope of the Value Added Tax Act                   |
| Sales of goods and services to other countries (exports) |                                                                                            |
| 52                                                       | Sales of goods and services exempt from value added tax to other countries                 |
| Purchases of goods and services in Norway (Deductions)   |                                                                                            |
| 1                                                        | Purchases of goods and services (standard rate)                                            |
| Specification line for code 1                            | losses on outstanding claims and cancellations                                             |
| Specification line for code 1                            | adjustment of input VAT on capital goods                                                   |
| Specification line for code 1                            | reversal of input VAT on passenger vehicles or Sale etc of real property before completion |
| 11                                                       | Purchases of goods and services (middle rate)                                              |
| Specification line for code 11                           | losses on outstanding claims and cancellations                                             |
| 13                                                       | Purchases of goods and services (low rate)                                                 |
| Specification line for code 13                           | losses on outstanding claims and cancellations                                             |
| Purchases of goods from abroad (import)                  |                                                                                            |
| 81                                                       | Purchases of goods from abroad with deductions (standard rate)                             |
| 81                                                       | Deductions on purchases of goods from abroad with deductions (standard rate)               |
| Specification line for code 81                           | reversal of input VAT on passenger vehicles or Sale etc of real property before completion |
| 14                                                       | Purchases of goods from abroad, value added tax paid upon import (standard rate)           |
| 82                                                       | Purchases of goods from abroad without deduction entitlement (standard rate)               |
| 83                                                       | Purchases of goods from abroad with deductions (middle rate)                               |
| 83                                                       | Deductions on purchases of goods from abroad with deductions (middle rate)                 |
| 15                                                       | Purchases of goods from abroad, value added tax paid upon import (middle rate)             |
| 84                                                       | Purchases of goods from abroad without deduction entitlement (middle rate)                 |
| 85                                                       | Purchases of goods from abroad with a zero-rate                                            |
| Purchases of services from abroad (import)               |                                                                                            |
| 86                                                       | Purchases of services from abroad with deductions (standard rate)                          |
| 86                                                       | Deductions on purchases of services from abroad with deductions (standard rate)            |
| 87                                                       | Purchases of services from abroad without deduction entitlement (standard rate)            |
| 88                                                       | Purchases of services from abroad with deductions (low rate)                               |
| 88                                                       | Deductions on purchases of services from abroad with deductions (low rate)                 |
| 89                                                       | Purchases of services from abroad without deduction entitlement (low rate)                 |
| Fish etc.                                                |                                                                                            |
| 32                                                       | Sales of fish and other marine wildlife resources                                          |
| Specification line for code 32                           | withdrawal                                                                                 |
| 12                                                       | Value added tax paid upon the purchase of fish and other marine wildlife resources         |
| Specification line for code 12                           | Losses on outstanding claims and cancellations                                             |
| Emission allowances and gold                             |
| 51                                                       | Sales of emission allowances and gold to businesses/self-employed persons                  |
| 91                                                       | Purchases of emission allowances and gold                                                  |
| 91                                                       | Deductions on purchases of emission allowances and gold                                    |
| 92                                                       | Purchases of emission allowances and gold without deduction entitlement                    |

[Overview of encoding/ code list:](https://github.com/Skatteetaten/mva-meldingen/tree/master/docs/documentation/informasjonsmodell/kodelister/)

- Encoding for VAT code: [mvaKodeSAFT](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/kodelister/mvaKodeSAFT.xml)
- Encoding for VAT specification: [mvaSpesifikasjon](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/kodelister/mvaSpesifikasjon.xml)
- Encoding for VAT rate: [sats](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/kodelister/sats.xml)
- Encoding for remarks: [merknader](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/kodelister/merknad.xml)
- Encoding for remarks and corresponding VAT code: [merknadTilsvarendeMvaKode.xml](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/kodelister/merknadTilsvarendeMvaKode.xml)

## Remarks in accordance to the VAT codes and the VAT tax return

Overview over remarks in accordance to the VAT codes in the VAT tax return [remarks in accordance to the VAT codes](merknader mot poster og hele meldingen.xlsx)

# XSD for uploading metadata

The submission of the VAT return requires an XML file containg metadata. This file must be according to the XSD for submission.

[no.skatteetaten.fastsetting.avgift.mvamvameldinginnsending.v1.0.xsd](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/xsd/no.skatteetaten.fastsetting.avgift.mvamvameldinginnsending.v1.0.xsd)

# XSD for validation response

XSD for validation documents the structure of the response from the validation service. The feedback will also be according to this XSD.
[no.skatteetaten.fastsetting.avgift.mva.valideringsresultat.v1.xsd](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/xsd/no.skatteetaten.fastsetting.avgift.mva.valideringsresultat.v1.xsd)

# XSD for payment information

[no.skatteetaten.fastsetting.avgift.mva.skattemeldingformerverdiavgift.betalingsinformasjon.v1.0.xsd](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/informasjonsmodell/xsd/no.skatteetaten.fastsetting.avgift.mva.skattemeldingformerverdiavgift.betalingsinformasjon.v1.0.xsd)
