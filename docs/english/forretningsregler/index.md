---
icon: "cloud"
title: "Validation rules"
description: "Validation rules for the VAT tax return"
---
### Change log
<table align=center>
  <tr><th style="width:25%" align=left>Date</th><th align=left> What was changed? </th></tr>
  <tr>
      <td>2021.10.12</td>
      <td>
          <ul>
            <li> The text for rule R018 was corrected. </li>
            <li> R020 - R022 checks both merknad fields </li>
            <li> R028 - R032 now uses the severity level "ugyldig skattemelding" </li>
            <li> R028 - R037 corrected the check for in- and outgoing lines </li>
            <li> R047 - R058 will not run if register data unavailable, descriptions changed </li>
            <li> R072 and R073 added for better feedback when there is an error with a VAT registration </li>
            <li> R059 and R060 exceptions added for VAT registered that have reported termination, bankruptcy or death </li>
            <li> R066 description change </li>
            <li> R067 and R068 updated to handle exception for VAT code 81 </li>
            <li> R078 added (reversal of input VAT specification requires a remark) </li>
            <li> R079 added (KID validation) </li>
            <li> R080 added (account number required for repayments) </li>
            <li> R004 - R017 fixed rules replaced with R074 - R076 which use the code list merknadTilsvarendeMvaKode </li>
          </ul>      
      </td>
  </tr>
  <tr>
      <td>2021.06.07</td>
      <td>
          <ul>
            <li>VAT code 5 added as a valid VAT code for withdrawals. </li>
            <li> VAT code 81 added as a valid VAT code for reversal of input VAT. </li>
            <li> Severity level "mangelfull melding" has been removed and the rules which used this severity level now give the response "avvikende skattemelding". </li>
            <li> Rules R023 - R027 now use the severity level "ugyldig skattemelding". </li>
            <li> The description in rule R040 was corrected. </li>
          </ul>      
	</td>
  </tr>
  <tr>
      <td>2021.06.28</td>
      <td>
          <ul>
            <li> Rules R065-R068 added to the detailed description list. </li>
          </ul>      
	</td>
  </tr>
</table>

## Validation rules

The validation rules are under development an new validation rules will be added.
The following validation rules are definded for the VAT return listing:

- The sum of the calculated VAT from each VAT line shall be equal to the total VAT in the VAT return
- The calculated VAT must be in accordance with the stated VAT-basis multiplied by the current VAT-rate
- Additional information is lacking for output VAT amounts with opposite +/- sign
- Additional information is lacking for input VAT amounts that have been claimed deductable with opposite +/- sign
- Additional information for specification lines that apply to the reversal of input VAT given in VAT §9-6 and §9-7
- Input VAT that has been claimed deductable on goods purchased from abroad must be less than or equal to output VAT (code 81)
- Input VAT that has been claimed deductable on goods purchased from abroad must be less than or equal to output VAT (code 83)
- Input VAT that has been claimed deductable on services purchased from abroad must be less than or equal to output VAT (code 86)
- Input VAT that has been claimed deductable on services purchased from abroad must be less than or equal to output VAT (code 88)
- Input VAT that has been claimed deductable on climate quotas and gold must be less than or equal to output VAT
- There must be output VAT if input VAT has been deducted for deductable goods purchased from abroad (code 81)
- There must be output VAT if input VAT has been deducted for deductable goods purchased from abroad (code 83)
- There must be output VAT if input VAT has been deducted for deductable services purchased from abroad (code 86)
- There must be output VAT if input VAT has been deducted for deductable services purchased from abroad (code 88)
- There must be output VAT if input VAT has been deducted for deductable climate quotas and gold
- There must be deductable input VAT if there is output VAT on goods purchased from abroad (code 81)
- There must be deductable input VAT if there is output VAT on goods purchased from abroad (code 83)
- There must be deductable input VAT if there is output VAT on services purchased from abroad (code 86)
- There must be deductable input VAT if there is output VAT on services purchased from abroad (code 88)
- There must be deductable input VAT if there is output VAT on deductable climate quotas and gold
- Specification lines that apply to losses on outstanding claims can only be submitted on VAT codes 1, 11, 12 or 13
- Specification lines that apply to withdrawals can only be submitted on VAT codes 3, 5, 31, 32 of 33
- Specification lines that apply to adjustment can only be submitted on VAT code 1
- Specification lines that apply to the reversal of input VAT given in VAT §9-6 and §9-7 can only be submitted on VAT code 1 or 81
- The specified category for the VAT return does not match the details in the VAT register (general industry)
- The specified category for the VAT return does not match the details in the VAT register (primary industry)
- The specified tax period type does not match the details in the VAT register (general industry)
- The specified tax period type does not match the details in the VAT register (primary industry)
- The reporting body for a joint registration must be registered for VAT
- Total revnue in the VAT return must be under one million for yearly general industry returns
- Specification lines must have valid VAT codes for returns relating to general industry registrations
- Specification lines must have valid VAT codes for returns relating to primary industry registrations
- Deductions for input VAT must not be declared without a registration in the VAT register (general industry)
- Deductions for input VAT must not be declared without a registration in the VAT register (primary industry)
- Deductions for input and output VAT must not be declared without a registration in the VAT register (general industry)
- Deductions for input and output VAT must not be declared without a registration in the VAT register (primary industry)
- VAT returns must not be sent in before the related tax period has ended (general industry)
- VAT returns must not be sent in before the related tax period has ended (primary industry)
- Input VAT must be declared without a VAT-basis or VAT-rate
- Output VAT must be declared with a VAT-basis and VAT-rate
- Specification lines that apply to the reversal of input VAT given in VAT §9-6 and §9-7 require a remark
- Account number must be registered for VAT returns that require a repayment
- Remarks must be valid for the given VAT code (expected VAT direction)
- Remarks must be valid for the given VAT code (opposite VAT direction)
- Remarks must be valid for the given VAT code (lines with a specification)

## Detailed description of the validation rules

Validation of the VAT return is implemented with a set of rules that are run automatically to check the validity of the message.
The rules are designed so that they are both documentation of the rules for the message and runnable by the system.

Example of rule:

```kotlin {.line-numbers}
[1]     MvaMeldingsinnhold_GrunnlagGangerGjeldendeSats_FeilBeregnetMerverdiavgiftForAvgiftslinje(
[2]        "Beregnet avgift i avgiftslinje er ulik produktet av grunnlag og sats" {
[3]            valideringsregel {
[4]                mvaSpesifikasjonslinje
[5]                    .hvor { linje -> linje.grunnlag ikkeEr tomt }
[6]                    .skal { linje -> linje.grunnlag * linje.sats væreRundetNedTil linje.merverdiavgift }
[7]            }
[8]            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
[9]        }
[10]    )
```

Each line can be translated to the following:

** Line 1 - Identifier **: This is the reference for the rule. Each rule has a unique identifier.

** Line 2 - Error message **: This is the error message that will be returned in the validation API if The VAT return is not in accordance with the requirement in the rule.

** Line 3-7 - Validation Rule **: This is the rule that defines what a valid VAT return should be.
If this rule is not met, the validation will fail.

** Line 8 - Severity **: This is the severity if the validation fails.
The following severity levels are defined : AVVIKENDE_SKATTEMELDING (anomalous VAT return), UGYLDIG_SKATTEMELDING (invalid vat return)

##Detailed Specification of the rules

```kotlin
       MVA_MELDINGSINNHOLD_SUM_MVA_FEIL_SUMMERING_AV_AVGIFTLINJER(
        "Summen av merverdiavgift for hver avgiftslinje er ikke lik feltet fastsattMerverdiavgift" {
            valideringsregel {
                mvaSpesifikasjonslinje.summenAv { linje ->
                    linje.merverdiavgift
                } skalVære skattegrunnlagOgBeregnetSkatt.fastsattMerverdiavgift
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R018 }
        }
    ),
    MVA_MELDINGSINNHOLD_GRUNNLAG_GANGER_GJELDENDE_SATS_FEIL_BEREGNET_MERVERDIAVGIFT_FOR_AVGIFTSLINJE(
        "Beregnet avgift i avgiftslinje er ulik produktet av grunnlag og sats" {
            valideringsregel {
                mvaSpesifikasjonslinje
                    .hvor { linje -> linje.grunnlag ikkeEr tomt }
                    .skal { linje -> linje.grunnlag * linje.sats væreRundetNedTil linje.merverdiavgift }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R019 }
        }
    ),

    MVA_MELDINGSINNHOLD_UTGÅENDE_MOTSATT_FORTEGN_MERKNAD_TIL_MVA_KODEN_MANGLER(
        "Merknad til beløp med motsatt fortegn som gjelder grunnlag og utgående avgift mangler"
        {
            valideringsregel {
                kodene(3, 6, 31, 32, 33, 51, 52, 81, 82, 83, 84, 85, 86, 87, 88, 89, 91, 92)
                    .hvor { linje -> linje.grunnlag erMindreEnn 0.0 }
                    .skal { linje ->
                        (linje.merknad?.beskrivelse har innhold) eller
                            (linje.merknad?.utvalgtMerknad har innhold)
                    }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R020 }
        }
    ),

    MVA_MELDINGSINNHOLD_INNGÅENDE_MOTSATT_FORTEGN_MERKNAD_TIL_MVA_KODEN_MANGLER(
        "Merknad til beløp med motsatt fortegn som gjelder fradragsført inngående avgift mangler"
        {
            valideringsregel {
                kodene(1, 11, 12, 13, 14, 15, 81, 83, 86, 88, 91)
                    .hvor { linje -> linje.grunnlag er tomt og (linje.merverdiavgift erStørreEnn 0.0) }
                    .skal { linje ->
                        ((linje.merknad?.beskrivelse har innhold) eller (linje.merknad?.utvalgtMerknad har innhold)) medmindre (
                            linje.mvaKode er "1" og (
                                linje.spesifikasjon væreMedI spesifikasjonene(
                                    JUSTERING.spesifikasjon,
                                    TAPPÅKRAV.spesifikasjon,
                                    TILBAKEFØRING.spesifikasjon
                                )
                                )
                                eller (
                                    linje.mvaKode væreMedI mvaKodene(1, 11, 12, 13) og (
                                        linje.spesifikasjon er TAPPÅKRAV.spesifikasjon.kode
                                        )
                                    )
                            )
                    }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R021 }
        }
    ),

    MVA_MELDINGSINNHOLD_SPESIFIKASJONSLINJE_TILBAKEFØRING_INNGÅENDE_AVGIFT_9_6_OG_9_7_MOTSATT_FORTEGN_MERKNAD_MANGLER(
        "Merknad til beløp med motsatt fortegn som gjelder spesifikasjonslinje for tilbakeføring av inngående mva gitt i mval §9-6 og §9-7"
        {
            valideringsregel {
                kodene(1)
                    .hvor { linje ->
                        linje.grunnlag er tomt og (linje.merverdiavgift erMindreEnn 0.0) og (linje.spesifikasjon er TILBAKEFØRING.spesifikasjon.kode)
                    }
                    .skal { linje -> ((linje.merknad?.beskrivelse har innhold) eller (linje.merknad?.utvalgtMerknad har innhold)) }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R022 }
        }
    ),

    MVA_MELDINGSINNHOLD_VARER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_MELLOM_BEREGNET_OG_INNGÅENDE_AVGIFT_MVA_KODE_81(
        "Fradragsført inngående avgift som gjelder varer kjøpt fra utlandet med fradragsrett, skal være mindre enn eller lik beregnet avgift"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 81 og (linje.merverdiavgift erMindreEnn 0.0) } såSkal {
                    utgåendeMerverdiavgiftMvaKode(81) uansetFortegnVæreStørreEllerLikEnn inngåendeMerverdiavgiftMvaKode(
                        81
                    )
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R023 }
        }
    ),

    MVA_MELDINGSINNHOLD_VARER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_MELLOM_BEREGNET_OG_INNGÅENDE_AVGIFT_MVA_KODE_83(
        "Fradragsført inngående avgift som gjelder varer kjøpt fra utlandet med fradragsrett, skal være mindre enn eller lik beregnet avgift"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 83 og (linje.merverdiavgift erMindreEnn 0.0) } såSkal {
                    utgåendeMerverdiavgiftMvaKode(83) uansetFortegnVæreStørreEllerLikEnn inngåendeMerverdiavgiftMvaKode(
                        83
                    )
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R024 }
        }
    ),

    MVA_MELDINGSINNHOLD_OMVENDT_AVGIFTSPLIKT_FOR_TJENSTER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_MELLOM_BEREGNET_OG_INNGÅENDE_AVGIFT_MVA_KODE_86(
        "Fradragsført inngående avgift som gjelder tjenester kjøpt fra utlandet med fradragsrett, skal være mindre enn eller lik beregnet avgift"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 86 og (linje.merverdiavgift erMindreEnn 0.0) } såSkal {
                    utgåendeMerverdiavgiftMvaKode(86) uansetFortegnVæreStørreEllerLikEnn inngåendeMerverdiavgiftMvaKode(
                        86
                    )
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R025 }
        }
    ),

    MVA_MELDINGSINNHOLD_OMVENDT_AVGIFTSPLIKT_FOR_TJENSTER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_MELLOM_BEREGNET_OG_INNGÅENDE_AVGIFT_MVA_KODE_88(
        "Fradragsført inngående avgift som gjelder tjenester kjøpt fra utlandet med fradragsrett, skal være mindre enn eller lik beregnet avgift"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 88 og (linje.merverdiavgift erMindreEnn 0.0) } såSkal {
                    utgåendeMerverdiavgiftMvaKode(88) uansetFortegnVæreStørreEllerLikEnn inngåendeMerverdiavgiftMvaKode(
                        88
                    )
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R026 }
        }
    ),

    MVA_MELDINGSINNHOLD_KJØP_AV_KLIMAKVOTER_OG_GULL_MED_FRADRAGSRETT_AVVIK_MELLOM_BEREGNET_OG_INNGÅENDE_AVGIFT_MVA_KODE_91(
        "Fradragsført inngående avgift som gjelder kjøp av klimakvoter og gull med fradragsrett, skal være mindre enn eller lik beregnet avgift"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 91 og (linje.merverdiavgift erMindreEnn 0.0) } såSkal {
                    utgåendeMerverdiavgiftMvaKode(91) uansetFortegnVæreStørreEllerLikEnn inngåendeMerverdiavgiftMvaKode(
                        91
                    )
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R027 }
        }
    ),

    MVA_MELDINGSINNHOLD_VARER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_UTGÅENDE_AVGIFT_MANGLER_MVA_KODE_81(
        "Utgående avgift skal være beregnet dersom det er ført fradrag for inngående avgift som gjelder kjøp av varer fra utlandet med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 81 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 81 og (linje.merverdiavgift erStørreEnn 0.0 og (linje.grunnlag har innhold)) }
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R028 }
        }
    ),

    MVA_MELDINGSINNHOLD_VARER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_UTGÅENDE_AVGIFT_MANGLER_MVA_KODE_83(
        "Utgående avgift skal være beregnet dersom det er ført fradrag for inngående avgift som gjelder kjøp av varer fra utlandet med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 83 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 83 og (linje.merverdiavgift erStørreEnn 0.0 og (linje.grunnlag har innhold)) }
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R029 }
        }
    ),

    MVA_MELDINGSINNHOLD_OMVENDT_AVGIFTSPLIKT_FOR_TJENSTER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_UTGÅENDE_AVGIFT_MANGLER_MVA_KODE_86(
        "Utgående avgift skal være beregnet dersom det er ført fradrag for inngående avgift som gjelder tjenester kjøpt fra utlandet med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 86 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 86 og (linje.merverdiavgift erStørreEnn 0.0 og (linje.grunnlag har innhold)) }
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R030 }
        }
    ),

    MVA_MELDINGSINNHOLD_OMVENDT_AVGIFTSPLIKT_FOR_TJENSTER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_UTGÅENDE_AVGIFT_MANGLER_MVA_KODE_88(
        "Utgående avgift skal være beregnet dersom det er ført fradrag for inngående avgift som gjelder tjenester kjøpt fra utlandet med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 88 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 88 og (linje.merverdiavgift erStørreEnn 0.0 og (linje.grunnlag har innhold)) }
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R031 }
        }
    ),

    MVA_MELDINGSINNHOLD_KJØP_AV_KLIMAKVOTER_OG_GULL_MED_FRADRAGSRETT_AVVIK_UTGÅENDE_AVGIFT_MANGLER_MVA_KODE_91(
        "Utgående avgift skal være beregnet dersom det er ført fradrag for inngående avgift som gjelder kjøp av klimakvoter og gull med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje -> linje.mvaKode er 91 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 91 og (linje.merverdiavgift erStørreEnn 0.0 og (linje.grunnlag har innhold)) }
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R032 }
        }
    ),

    MVA_MELDINGSINNHOLD_OMVENDT_AVGIFTSPLIKT_FOR_TJENSTER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_INNGÅENDE_AVGIFT_MANGLER_MVA_KODE_81(
        "Det skal være fradragsført inngående avgift dersom det er beregnet utgående avgift ved kjøp av varer fra utlandet med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje ->
                    linje.mvaKode er 81 og (linje.grunnlag har innhold) og (linje.spesifikasjon er tomt)
                }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 81 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R033 }
        }
    ),

    MVA_MELDINGSINNHOLD_OMVENDT_AVGIFTSPLIKT_FOR_TJENSTER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_INNGÅENDE_AVGIFT_MANGLER_MVA_KODE_83(
        "Det skal være fradragsført inngående avgift dersom det er beregnet utgående avgift ved kjøp av varer fra utlandet med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje ->
                    linje.mvaKode er 83 og (linje.grunnlag har innhold) og (linje.spesifikasjon er tomt)
                }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 83 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R034 }
        }
    ),

    MVA_MELDINGSINNHOLD_VARER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_INNGÅENDE_AVGIFT_MANGLER_MVA_KODE_86(
        "Det skal være fradragsført inngående avgift dersom det er beregnet utgående avgift for tjenester kjøpt fra utlandet med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje ->
                    linje.mvaKode er 86 og (linje.grunnlag har innhold) og (linje.spesifikasjon er tomt)
                }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 86 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R035 }
        }
    ),

    MVA_MELDINGSINNHOLD_VARER_KJØPT_FRA_UTLANDET_MED_FRADRAGSRETT_AVVIK_INNGÅENDE_AVGIFT_MANGLER_MVA_KODE_88(
        "Det skal være fradragsført inngående avgift dersom det er beregnet utgående avgift for tjenester kjøpt fra utlandet med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje ->
                    linje.mvaKode er 88 og (linje.grunnlag har innhold) og (linje.spesifikasjon er tomt)
                }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 88 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R036 }
        }
    ),

    MVA_MELDINGSINNHOLD_KJØP_AV_KLIMAKVOTER_OG_GULL_MED_FRADRAGSRETT_AVVIK_INNGÅENDE_AVGIFT_MANGLER_MVA_KODE_91(
        "Det skal være fradragsført inngående avgift dersom det er beregnet utgående avgift ved kjøp av klimakvoter og gull med fradragsrett"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.finnes { linje ->
                    linje.mvaKode er 91 og (linje.grunnlag har innhold) og (linje.spesifikasjon er tomt)
                }.såSkal {
                    mvaSpesifikasjonslinje.inneholder { linje -> linje.mvaKode er 91 og (linje.grunnlag er tomt) og (linje.spesifikasjon er tomt) }
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R037 }
        }
    ),
    
    MVA_MELDINGSINNHOLD_SPESIFIKASJONSLINJE_TAP_PÅ_KRAV_FØRT_PÅ_FEIL_MVA_KODE(
        "Spesifikasjonslinje som gjelder tap på krav kan kun sendes inn på mva-kode 1, 11, 12 eller 13"
        {
            valideringsregel {
                mvaSpesifikasjonslinje
                    .hvor { linje -> linje.spesifikasjon er TAPPÅKRAV.spesifikasjon.kode }
                    .skal { linje -> linje.mvaKode væreMedI mvaKodene(1, 11, 12, 13) }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { XSD_FORMAT_OG_LOVLIGE_VERDIER }
            regelnummer { R038 }
        }
    ),

    MVA_MELDINGSINNHOLD_SPESIFIKASJONSLINJE_UTTAK_FØRT_PÅ_FEIL_MVA_KODE(
        "Spesifikasjonslinje som gjelder uttak kan kun sendes inn på mva-kode 3, 5, 31, 32 eller 33"
        {
            valideringsregel {
                mvaSpesifikasjonslinje
                    .hvor { linje -> linje.spesifikasjon er UTTAK.spesifikasjon.kode }
                    .skal { linje -> linje.mvaKode væreMedI mvaKodene(3, 5, 31, 32, 33) }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { XSD_FORMAT_OG_LOVLIGE_VERDIER }
            regelnummer { R039 }
        }
    ),

    MVA_MELDINGSINNHOLD_SPESIFIKASJONSLINJE_JUSTERING_FØRT_PÅ_FEIL_MVA_KODE(
        "Spesifikasjonslinje som gjelder justering kan kun sendes inn på mva-kode 1"
        {
            valideringsregel {
                mvaSpesifikasjonslinje
                    .hvor { linje -> linje.spesifikasjon er JUSTERING.spesifikasjon.kode }
                    .skal { linje -> linje.mvaKode være 1 }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { XSD_FORMAT_OG_LOVLIGE_VERDIER }
            regelnummer { R040 }
        }
    ),

    MVA_MELDINGSINNHOLD_SPESIFIKASJONSLINJE_TILBAKEFØRING_INNGÅENDE_AVGIFT_9_6_OG_9_7_FØRT_PÅ_FEIL_MVA_KODE(
        "Spesifikasjonslinje som gjelder tilbakeføring av inngående mva gitt i Merverdiavgiftsloven §9-6 og §9-7 kan kun sendes inn på mva-kode 1 eller 81"
        {
            valideringsregel {
                mvaSpesifikasjonslinje
                    .hvor { linje -> linje.spesifikasjon er TILBAKEFØRING.spesifikasjon.kode }
                    .skal { linje -> linje.mvaKode væreMedI mvaKodene(1, 81) }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { XSD_FORMAT_OG_LOVLIGE_VERDIER }
            regelnummer { R041 }
        }
    ),
    
    MVA_PLIKT_OPPGITT_MELDINGSKATEGORI_ALMINNELIG_NÆRING_FINNES_IKKE(
        "Det finnes ingen plikt i mva-registeret med oppgitt meldingskategori for denne parten."
        {
            valideringsregel {
                registerDataHentetOk er OK og (meldingskategori er alminnelig) såSkal {
                    meldingskategori være registrertMeldingskategori
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { PLIKT }
            regelnummer { R047 }
        }
    ),

    MVA_PLIKT_OPPGITT_MELDINGSKATEGORI_PRIMÆRNÆRING_FINNES_IKKE(
        "Det finnes ingen plikt i mva-registeret med oppgitt meldingskategori for denne parten."
        {
            valideringsregel {
                registerDataHentetOk er OK og (meldingskategori er primærnæring) såSkal {
                    meldingskategori være registrertMeldingskategori
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { PLIKT }
            regelnummer { R048 }
        }
    ),

    MVA_PLIKT_OPPGITT_SKATTLEGGINGSPERIODE_FOR__MELDINGSKATEGORI_ALMINNELIG_NÆRING_FINNES_IKKE(
        "Oppgitt skattleggingsperiode stemmer ikke med opplysningene for denne meldingskategorien i mva-registeret"
        {
            valideringsregel {
                registerDataHentetOk er OK og
                    (meldingskategori er alminnelig og (registrertSkattleggingsperiodetype har innhold)).såSkal {
                        skattleggingsperiodetype være registrertSkattleggingsperiodetype
                    }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { PLIKT }
            regelnummer { R049 }
        }
    ),

    MVA_PLIKT_OPPGITT_SKATTLEGGINGSPERIODE_FOR__MELDINGSKATEGORI__PRIMÆRNÆRING_FINNES_IKKE(
        "Oppgitt skattleggingsperiodetype stemmer ikke med opplysningene for denne meldingskategorien i mva-registeret"
        {
            valideringsregel {
                registerDataHentetOk er OK og
                    (meldingskategori er primærnæring og (registrertSkattleggingsperiodetype har innhold)) såSkal {
                    skattleggingsperiodetype være registrertSkattleggingsperiodetype
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { PLIKT }
            regelnummer { R050 }
        }
    ),
    
    MVA_PLIKT_FOR__MELDINGSKATEGORI_DEKKER_IKKE_SKATTELEGGINGSPERIODE_ALMINNELIG(
        "Det finnes ingen plikt med oppgitt meldingskategori som innkluderer start datoen av oppgitt skattleggingsperiode"
        {
            valideringsregel {
                registerDataHentetOk er OK og
                    (meldingskategori er alminnelig og (registrertMeldingskategori er alminnelig)) såSkal {
                    startDatoForPeriodeVæreInnenforGyldigFraOgTilDatoer
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { PLIKT }
            regelnummer { R072 }
        }
    ),

    MVA_PLIKT_FOR__MELDINGSKATEGORI_DEKKER_IKKE_SKATTELEGGINGSPERIODE_PRIMAERNAERING(
        "Det finnes ingen plikt med oppgitt meldingskategori som innkluderer start datoen av oppgitt skattleggingsperiode"
        {
            valideringsregel {
                registerDataHentetOk er OK og
                    (meldingskategori er primærnæring og (registrertMeldingskategori er primærnæring)) såSkal {
                    startDatoForPeriodeVæreInnenforGyldigFraOgTilDatoer
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { PLIKT }
            regelnummer { R073 }
        }
    ),

    MVA_PLIKT_DELTAKERE_I_FELLESREGISTRERING_HAR_IKKE_MVA_PLIKT(
        "Det er rapporterende enhet i en fellesregistrering som har mva-plikt og skal sende inn mva-melding for fellesregistreringen"
        {
            valideringsregel {
                registerDataHentetOk er OK og (registrertRapporterendeEnhet har innhold) såSkal {
                    skattepliktig være registrertRapporterendeEnhet
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MVA_REGISTER_OPPLYSNINGER }
            regelnummer { R051 }
        }
    ),

    MVA_MELDINGSINNHOLD_OMSETNING_OVER_EN_MILLON_FOR_ÅRSTERMINPLIKT(
        "For å kunne levere mva-melding som alminnelig næring med års termin så skal avgiftspliktig omsetningen være under en million i året"
        {
            valideringsregel {
                meldingskategori er alminnelig og (skattleggingsperiodetype er årlig) såSkal {
                    omsetning væreMindreEnn 1100000.0
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R052 }
        }
    ),

    MVA_MELDINGSINNHOLD_UGYLDIG_MVA_KODE_FOR_OPPGITT_MELDINGSKATEGORI_ALMINNELIG_NÆRING_MED_PLIKT(
        "Kun gyldige mva-koder til meldingskategori 'Alminnelig næring' hvor det finnes plikt i mva-registere kan sendes inn"
        {
            valideringsregel {
                registerDataHentetOk er OK og
                    (meldingskategori er alminnelig og (registrertMeldingskategori har innhold)) såSkal {
                    mvaSpesifikasjonslinje.skal { linje ->
                        linje.mvaKode væreMedI mvaKodene(
                            1,
                            11,
                            12,
                            13,
                            14,
                            15,
                            3,
                            31,
                            32,
                            33,
                            5,
                            51,
                            52,
                            6,
                            81,
                            82,
                            83,
                            84,
                            85,
                            86,
                            87,
                            88,
                            89,
                            91,
                            92
                        )
                    }
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R053 }
        }
    ),

    MVA_MELDINGSINNHOLD_UGYLDIG_MVA_KODE_FOR_OPPGITT_MELDINGSKATEGORI_PRIMÆRNÆRING_MED_PLIKT(
        "Kun gyldige mva-koder til meldingskategori 'Primærnæring' hvor det finnes plikt i mva-registeret kan sendes inn"
        {
            valideringsregel {
                registerDataHentetOk er OK og
                    (meldingskategori er primærnæring og (registrertMeldingskategori har innhold)) såSkal {
                    mvaSpesifikasjonslinje.skal { linje ->
                        linje.mvaKode væreMedI mvaKodene(
                            1,
                            11,
                            12,
                            13,
                            14,
                            15,
                            3,
                            31,
                            32,
                            33,
                            5,
                            51,
                            52,
                            6,
                            81,
                            82,
                            83,
                            84,
                            85,
                            86,
                            87,
                            88,
                            89,
                            91,
                            92
                        )
                    }
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R054 }
        }
    ),

    MVA_MELDINGSINNHOLD_FRADRAG_INNGÅENDE_AVGIFT_OPPGITT_MELDINGSKATEGORI_ALMINNELIG_NÆRING_UTEN_PLIKT(
        "Det kan ikke føres fradrag for inngående avgift på en termin hvor det ikke finnes plikt i mva-registeret"
        {
            valideringsregel {
                registerDataHentetOk er OK og
                    (meldingskategori er alminnelig og (registrertMeldingskategori er tomt)) såSkal {
                    mvaSpesifikasjonslinje.skal { linje ->
                        linje.mvaKode væreMedI mvaKodene(3, 31, 32, 33, 87, 89, 92)
                    }
                } medmindre (
                    mvaSpesifikasjonslinje.inneholder { linje ->
                        linje.mvaKode væreMedI mvaKodene(
                            3,
                            31,
                            32,
                            33,
                            87,
                            89,
                            92
                        )
                    } og (
                        mvaSpesifikasjonslinje.inneholder { linje ->
                            linje.mvaKode væreMedI mvaKodene(
                                1,
                                11,
                                12,
                                13,
                                14,
                                15
                            )
                        }
                        )
                    )
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R055 }
        }
    ),

    MVA_MELDINGSINNHOLD_FRADRAG_INNGÅENDE_AVGIFT_FOR_OPPGITT_MELDINGSKATEGORI_PRIMÆRNÆRING_UTEN_PLIKT(
        "Det kan ikke føres fradrag for inngående avgift på en termin hvor det ikke finnes plikt i mva-registeret"
        {
            valideringsregel {
                registerDataHentetOk er OK og
                    (meldingskategori er primærnæring og (registrertMeldingskategori er tomt)) såSkal {
                    mvaSpesifikasjonslinje.skal { linje ->
                        linje.mvaKode væreMedI mvaKodene(3, 31, 32, 33, 87, 89, 92)
                    }
                } medmindre (
                    mvaSpesifikasjonslinje.inneholder { linje ->
                        linje.mvaKode væreMedI mvaKodene(
                            3,
                            31,
                            32,
                            33,
                            87,
                            89,
                            92
                        )
                    } og (
                        mvaSpesifikasjonslinje.inneholder { linje ->
                            linje.mvaKode væreMedI mvaKodene(
                                1,
                                11,
                                12,
                                13,
                                14,
                                15
                            )
                        }
                        )
                    )
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R056 }
        }
    ),

    MVA_MELDINGSINNHOLD_FRADRAG_INNGÅENDE_AVGIFT_OG_UTGÅENDE_AVGIFT_FOR_OPPGITT_MELDINGSKATEGORI_ALMINNELIG_NÆRING_UTEN_PLIKT(
        "Det kan ikke føres fradrag for inngående avgift på en termin hvor det ikke finnes plikt i mva-registeret"
        {
            valideringsregel {
                registerDataHentetOk er OK og (
                    meldingskategori er alminnelig og (registrertMeldingskategori er tomt) og (
                        mvaSpesifikasjonslinje.inneholder { linje ->
                            linje.mvaKode væreMedI mvaKodene(
                                3,
                                31,
                                32,
                                33
                            )
                        }
                        )
                    ).såSkal {
                    mvaSpesifikasjonslinje.detIkkeFinner { linje ->
                        linje.mvaKode væreMedI mvaKodene(
                            1,
                            11,
                            12,
                            13,
                            14,
                            15
                        )
                    }
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R057 }
        }
    ),

    MVA_MELDINGSINNHOLD_FRADRAG_INNGÅENDE_AVGIFT_OG_UTGÅENDE_AVGIFT_FOR_OPPGITT_MELDINGSKATEGORI_PRIMÆRNÆRING_UTEN_PLIKT(
        "Det kan ikke føres fradrag for inngående avgift på en termin hvor det ikke finnes plikt i mva-registeret"
        {
            valideringsregel {
                registerDataHentetOk er OK og (
                    meldingskategori er primærnæring og (registrertMeldingskategori er tomt) og (
                        mvaSpesifikasjonslinje.inneholder { linje ->
                            linje.mvaKode væreMedI mvaKodene(
                                3,
                                31,
                                32,
                                33
                            )
                        }
                        )
                    ).såSkal {
                    mvaSpesifikasjonslinje.detIkkeFinner { linje ->
                        linje.mvaKode væreMedI mvaKodene(
                            1,
                            11,
                            12,
                            13,
                            14,
                            15
                        )
                    }
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R058 }
        }
    ),

    MVA_PLIKT_SKATTLEGGINGSPERIODEN_FOR__MELDINGSKATEGORI_ALMINNELIG_NÆRING_MÅ_VÆRE_FERDIG(
        "Det kan ikke sendes inn mva-melding før gjeldende skattleggingsperiode er ferdig"
        {
            valideringsregel {
                meldingskategori er alminnelig såSkal {
                    (nå væreEtter slutTerminsdato) medmindre (skattepliktigHarMeldtOpphør eller skattepliktigErRegistrertSomKonkursbo eller skattepliktigErRegistrertSomDødsbo)
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { PLIKT }
            regelnummer { R059 }
        }
    ),

    MVA_PLIKT_SKATTLEGGINGSPERIODEN_FOR__MELDINGSKATEGORI_PRIMÆRNÆRING_MÅ_VÆRE_FERDIG(
        "Det kan ikke sendes inn mva-melding før gjeldende skattleggingsperiode er ferdig"
        {
            valideringsregel {
                meldingskategori er primærnæring såSkal {
                    (nå væreEtter slutTerminsdato) medmindre (skattepliktigHarMeldtOpphør eller skattepliktigErRegistrertSomKonkursbo eller skattepliktigErRegistrertSomDødsbo)
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { PLIKT }
            regelnummer { R060 }
        }
    ),
    
    MVA_KODE_FOR_INNGÅENDE_AVGIFT_HAR_FEILAKTIG_GRUNNLAG_OG_SATS(
        "Beløp som gjelder inngående avgift skal ikke ha med grunnlag og sats"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.hvor { linje ->
                    linje.mvaKode væreMedI mvaKodene(1, 11, 12, 13, 14, 15) eller (
                        linje.spesifikasjon væreMedI spesifikasjonene(
                            JUSTERING.spesifikasjon,
                            TAPPÅKRAV.spesifikasjon,
                            TILBAKEFØRING.spesifikasjon
                        ) og (linje.mvaKode er 1)
                        )
                }
                    .skal { linje -> linje.sats være tomt og (linje.grunnlag være tomt) }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R065 }
        }
    ),

    MVA_KODE_FOR_UTGÅENDE_AVGIFT_MANGLER_GRUNNLAG_OG_SATS(
        "Beløp som gjelder utgående avgift skal ha med grunnlag og sats"
        {
            valideringsregel {
                mvaSpesifikasjonslinje.hvor { linje ->
                    linje.mvaKode væreMedI mvaKodene(3, 5, 6, 31, 32, 33, 51, 52) eller (
                        linje.spesifikasjon er UTTAK.spesifikasjon.kode og (
                            linje.mvaKode væreMedI mvaKodene(
                                3,
                                5,
                                31,
                                32,
                                33
                            )
                            )
                        )
                }
                    .skal { linje -> linje.sats ha innhold og (linje.grunnlag ha innhold) }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R066 }
        }
    ),

    MVA_KODE_FOR_UTGÅENDE_AVGIFT_MANGLER_GRUNNLAG_OG_SATS_MVA_KODE_81_TIl_92(
        "Beløp som gjelder utgående avgift skal ha med grunnlag og sats"
        {
            valideringsregel {
                kodene(81, 82, 83, 84, 85, 86, 87, 88, 89, 91, 92)
                    .hvor { linje -> linje.merverdiavgift erStørreEnn 0.0 }
                    .skal { linje ->
                        (linje.sats ha innhold og (linje.grunnlag ha innhold)) eller
                            (linje.mvaKode være 81 og (linje.spesifikasjon være TILBAKEFØRING.spesifikasjon.kode))
                    }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R067 }
        }
    ),

    MVA_KODE_FOR_INNGÅENDE_AVGIFT_HAR_FEILAKTIG_GRUNNLAG_OG_SATS_MVA_KODE_81_TIL_91(
        "Beløp som gjelder inngående avgift skal ikke ha med grunnlag og sats"
        {
            valideringsregel {
                kodene(81, 83, 86, 88, 91)
                    .hvor { linje -> linje.merverdiavgift erMindreEnn 0.0 }
                    .skal { linje ->
                        (linje.sats være tomt og (linje.grunnlag være tomt)) eller
                            (linje.mvaKode være 81 og (linje.spesifikasjon være TILBAKEFØRING.spesifikasjon.kode))
                    }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R068 }
        }
    ),
    
    MVA_MELDINGSINNHOLD_MERKNAD_MANGLER(
        "For denne spesifikasjonslinjen kreves det en gyldig merknad"
        {
            valideringsregel {

                mvaSpesifikasjonslinje
                    .hvor { linje ->
                        linje.spesifikasjon er TILBAKEFØRING.spesifikasjon.kode
                    }
                    .skal { linje ->
                        (linje.merknad?.beskrivelse ha innhold) eller (linje.merknad?.utvalgtMerknad ha innhold)
                    }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R078 }
        }
    ),
    
    MVA_MELDINGSINNHOLD_KID_NUMMER_GYLDIG(
        "KID nummer må være gyldig hvis oppgitt"
        {
            valideringsregel {
                (kidNummer har innhold) såSkal {
                    (kidNummer inneholde kunTallEllerKunTallMedBindestrekEtterSisteSiffer) og
                        (kidNummer haLengdeStørreEnn 1) og (kidNummer haLengdeMindreEnn 26)
                }
            }
            alvorlighetsgrad { UGYLDIG_SKATTEMELDING }
            kategori { MELDINGSINNHOLD }
            regelnummer { R079 }
        }
    ),
    
    MVA_MELDINGSINNHOLD_KONTONUMMER_FINNES_IKKE(
        "Kontonummer mangler ved utbetaling av merverdiavgift"
        {
            valideringsregel {
                (skattegrunnlagOgBeregnetSkatt.fastsattMerverdiavgift kunne føreTilEnUtbetaling) såSkal {
                    registrertKontonummer har innhold
                }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { KONTONUMMER }
            regelnummer { R080 }
        }
    ),
    
    MVA_KODE_MERKNAD_FORTEGN_GYLDIG_VANLIG_FORTEGN(
        "Merknaden er ikke gyldig på denne mva-koden med vanlig fortegn" {
            valideringsregel {
                mvaSpesifikasjonslinje
                    .hvor { linje ->
                        linje.merknad?.utvalgtMerknad har innhold og linje.harVanligFortegn() og
                            (linje.spesifikasjon er tomt)
                    }
                    .skal { linje ->
                        linje.haGyldigMerknadForOppgittMvaKodeOgBeløpOgSpesifikasjon()
                    }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MERKNAD }
            regelnummer { R074 }
        }
    ),

    MVA_KODE_MERKNAD_FORTEGN_GYLDIG_MOTSATT_FORTEGN(
        "Merknaden er ikke gyldig på denne mva-koden med motsatt fortegn" {
            valideringsregel {
                mvaSpesifikasjonslinje
                    .hvor { linje ->
                        linje.merknad?.utvalgtMerknad har innhold og linje.harMotsattFortegn() og
                            (linje.spesifikasjon er tomt)
                    }
                    .skal { linje ->
                        linje.haGyldigMerknadForOppgittMvaKodeOgBeløpOgSpesifikasjon()
                    }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MERKNAD }
            regelnummer { R075 }
        }
    ),

    MVA_KODE_MERKNAD_FORTEGN_GYLDIG_FOR_SPESIFIKASJON(
        "Merknaden er ikke gyldig på denne mva-koden med oppgitt spesifikasjon og fortegn" {
            valideringsregel {
                mvaSpesifikasjonslinje
                    .hvor { linje ->
                        linje.merknad?.utvalgtMerknad har innhold og (linje.spesifikasjon har innhold)
                    }
                    .skal { linje ->
                        linje.haGyldigMerknadForOppgittMvaKodeOgBeløpOgSpesifikasjon()
                    }
            }
            alvorlighetsgrad { AVVIKENDE_SKATTEMELDING }
            kategori { MERKNAD }
            regelnummer { R076 }
        }
    )

```
