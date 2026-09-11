# Kasutusjuhtumid

Ekilexi rakendusliidese kaudu on programmaatiliselt kättesaadav sama sisu, mida kuvab [Sõnaveeb](www.sonaveeb.ee). Siit leiad mõned näidise, kuidas hankida üksikasjalikke keeleandmeid Ekilexi avaliku API kaudu.

## Sisukord

- [1. Keelendi (sõna) id-numbri leidmine](#1-keelendi-sõna-id-numbri-leidmine)
- [2. Definitsiooni leidmine](#2-definitsiooni-leidmine)
- [3. Lemma leidmine](#3-lemma-leidmine)
- [4. Näitelaused](#4-näitelaused)
- [5. Sünonüüm/antonüüm](#5-sünonüümantonüüm)
- [6. Paradigmad](#6-paradigmad)
- [7. Tõlge](#7-tõlge)
- [8. Võrdlusastmed](#8-võrdlusastmed)
- [9. Kõik sõnastikuandmed](#9-kõik-sõnastikuandmed)
- [10. Keeleoskuse tase sõnadel](#10-keeleoskuse-tase-sõnadel)
- [11. Laiendatud tekstiotsing](#11-laiendatud-tekstiotsing)

### 1. KEELENDI (SÕNA) ID-NUMBRI LEIDMINE
Mitmed API-päringud eeldavad otspunkti URL-i lisatud otsitava keelendi identifikaatorit `wordId`. 
Selle parameetri leidmiseks on kaks võimalust:  

**Näide:**  
Leida keelendi "siirup" identifikaator `wordId`.

1. Kasuta sõnaotsingut `GET /api/word/search/siirup`  
Vastusena saadud andmestruktuurist leiab id-numbri rajalt `$words[*].wordId`
    <details>
    <summary>Näita täielikku vastust</summary>  
    
    ```
    {
        "totalCount": 1,
        "words": [
            {
                "wordId": 233407,
                "wordValue": "siirup",
                "wordValuePrese": "siirup",
                "homonymNr": 1,
                "lang": "est",
                "genderCode": "m",
                "prefixoid": false,
                "suffixoid": false,
                "foreign": false,
                "datasetCodes": [
                    "eki",
                    "ety",
                    "kok",
                    "kool_KV",
                    "les",
                    "nems"
                ],
                "lastActivityEventOn": "2025-10-17T20:12:24.58014",
                "wordPublic": true,
                "public": false,
                "wwUnif": false,
                "wwLite": false,
                "wwOs": false
            }
        ]
    }
    ```
    </details>  


2. Otsitava sõna id-numbrit saab ka leida päringuga `GET api/word/ids/{word}/{dataset}/{lang}`  
Selleks tuleb lisaks otsitavale keelendile määrata sõnakogu kood (üldkeele jaoks soovitame kasutada
EKI ühendsõnastikku koodiga `eki`, kõigi sõnakogude koodid leiab `GET api/datasets`) ning
otsitava keelendi keele kood (eesti keele jaoks `est`, kõikide keelte koodid saab pärida `GET api/classifiers/lang`).
Kasutades päringut `GET api/word/ids/siirup/eki/est` saab vastuseks massiivi vastavate id-numbritega.

   <details>
   <summary>Näita täielikku vastust</summary>

     ```
     [
   233407
     ]
     ```
 </details> 

> **Pane tähele!**  
> Kui otsitaval keelendil on homonüüme ehk leidub sama väliskujuga keelendeid (*word*), mis on täiesti eri tähendusega (*meaning*), nagu näiteks "keel",  või kui sama kirjaviisiga esineb vasteid
> teises keeles (nt "lamp", "park"), siis igaühel neist on oma unikaalne järjenumber `wordId`.

### 2. DEFINITSIOONI LEIDMINE
Tean ühte sõna ja tahan teada selle tähenduste seletusi.

**Näide:**  
Leida sõna "lustakas" tähenduste seletused.  

> **Pane tähele!**  
> Tähendusel (*meaning*) võib olla mitu seletust (*definition*).

Sõna tähenduse saamiseks tee päring `GET api/word/details/{wordId}`, kasutades keelendi id-numbrit:  
`GET api/word/details/197153`

Vastuseks saad iga ilmiku jaoks ühe tähenduse ning selle seletused.

> **Pane tähele!**  
> Ilmik (*lexeme*) on andmebaasiolem, mis seob keelendi ja tähenduse ühe sõnakogu jaoks.
> Seega on keelendil ilmikud nii iga tähenduse kui iga sõnakogu jaoks.
> Üks keelend esineb mitmes ilmikus, kui sama keelend esineb mitmes sõnakogus või kui ühel keelendil on mitu tähendust.

| Sisu                   | JSON-i tee                                    | Andmetüüp | Näide                   |
|------------------------|-----------------------------------------------|-----------|-------------------------|
| Keelendi tähenduse seletus | `$lexemes[*].meaning.definitions[*].value`    | String    | "lõbus ja naljakas"     |

<details>
<summary>Näita lühendatud vastust</summary>

```
{
    "word": {
        "wordId": 197153,
        "wordValue": "lustakas",
        "wordValuePrese": "lustakas",
        "homonymNr": 1,
        "lang": "est",
        ...
    },
    "lexemes": [
        {
            "lexemeWord": {
                ...
            },
            "meaning": {
                "meaningId": 57713,
                "firstWordValue": null,
                "lexemeIds": null,
                "definitions": [
                    {
                        "id": 60891,
                        "value": "lõbus ja naljakas",
                        "valuePrese": "lõbus ja naljakas",
                        "lang": "est",
                        ...
                    },
                    {
                        "id": 60896,
                        "value": "lõbusa ja vallatu olekuga",
                        "valuePrese": "lõbusa ja vallatu olekuga",
                        "lang": "est",
                        ...
                    }
                ],
                ...
            },
            "lexemeId": 1245013,
            "wordId": 197153,
            "meaningId": 57713,
            "datasetCode": "eki",
            "datasetName": "EKI ühendsõnastik 2025",
			...
        },
        {
         ...
        }
    ],
...
}
```
</details>  

### 3. LEMMA LEIDMINE
**Probleem:**  
Tean ühte sõna, aga see ei ole algvormis. Soovin leida algvormi.  

> **Pane tähele!**
> Lemma on sõna algvorm, eesti keeles nimisõnade puhul ainsuse nimetav, tegusõnadel ma-tegevusnimi. 
> Näiteks sõnal "tunde" on kaks lemmat: "tund" ja "tunne".

**Näide:**  
Leida sõna "läks" lemma.  
Tuleb teha päring` GET api/form/search/läks `  

Vastusena tuleb massiiv keelenditest, mis on otsinguteksti lemmadeks.  
JSON-ist leia `$[*].wordValue`
<details>
<summary>Näita täielikku vastust</summary>

```
[
    {
        "wordId": 202944,
        "wordValue": "minema",
        "lang": "est",
        "homonymNr": 1
    }
]
```
</details>  

### 4. NÄITELAUSED
**Probleem:**  
Soovin leida, millistes lausetes otsitavat sõna kasutatakse.

**Näide:**  
Leida sõna "naasma" näitelauseid.  
Selleks tuleb teha päring `GET api/word/details/{wordId}`, kasutades otsitava sõna id-numbrit.   

`GET api/word/details/206688`  
Vastusest saab iga ilmiku jaoks näitelaused, milles see keelend esineb. Lisaks näitelausetele leiab samast vastusest levinud sõnapaarid, milles otsisõna koos naabersõnadega sageli esineb ning näitelaused ka nende jaoks.

| Sisu                                            | JSON-i tee                                                                        | Andmetüüp | Näide                                                       |
|-------------------------------------------------|-----------------------------------------------------------------------------------|-----------|-------------------------------------------------------------|
| Näitelaused otsisõnale                          | `$lexemes[*].usages[*].value`                                                     | String    | "Rahuvalvajad naasid missioonilt."                          |
| Otsisõna sõnaühendis koos naabersõnaga          | `$lexemes[*].primaryCollocations[*].relGroups[*].collocations[*].wordValue`       | String    | "edukalt naasma"                                            |
| Näitelaused otsisõnaga seotud sõnaühendi jaoks  | `$lexemes[*].primaryCollocations[*].relGroups[*].collocations[*].usages[*].value` | String    | "Sportlane naasis pärast võistluspausi edukalt tippsporti." |
| Näitelaused ??? teiseste kollokatsioonide kohta | `$lexemes[*].secondaryCollocations[*].usages[*].value`                            | String    | "Laev naaseb peagi liinile."                                |

<details>
<summary>Näita lühendatud vastust</summary>

 ```
{
    "word": {
        "wordId": 206688,
        "wordValue": "naasma",
        "wordValuePrese": "naasma",
        "homonymNr": 1,
        "lang": "est",
        ...
    },
    "lexemes": [
        {
            "lexemeWord": {
                "wordId": 206688,
                "wordValue": "naasma",
                "wordValuePrese": "naasma",
                "homonymNr": 1,
                "lang": "est",
                ...
            },
            "meaning": {
                ...
            },
            "lexemeId": 1268693,
            "wordId": 206688,
            "meaningId": 68079,
            "datasetCode": "eki",
            "datasetName": "EKI ühendsõnastik 2025",
            ...
            "usages": [
                {
                    ...
                    "id": 120459,
                    "value": "Rahuvalvajad naasid missioonilt.",
                    "valuePrese": "Rahuvalvajad naasid missioonilt.",
                    "lang": "est",
                    ...
                },
                ...
                {
                    ...
                    "id": 120461,
                    "value": "Kapten naasis koju.",
                    "valuePrese": "Kapten naasis koju.",
                    "lang": "est",
                    ...
                },
                ...
            ],
            ...
            "primaryCollocations": [
                {
                    "posGroupCode": "D",
                    "posGroupValue": "adverb, määrsõnaga",
                    "relGroups": [
                        {
                            "relGroupCode": "Adv_modifier",
                            "relGroupValue": "Adv_modifier",
                            "collocations": [
                                {
                                    "lexemeId": 3119362,
                                    "wordId": 1916309,
                                    "wordValue": "edukalt naasma",
                                    "usages": [
                                        {
                                            ...
                                            "id": 493895,
                                            "value": "Sportlane naasis pärast võistluspausi edukalt tippsporti.",
                                            "valuePrese": "Sportlane naasis pärast võistluspausi edukalt tippsporti.",
                                            "lang": "est",
                                            ...
                                        }
                                    ],
                                    ...
                                },
                                ...                               
                            ]
                        }
                    ]
                },
                {
                    "posGroupCode": "S",
                    "posGroupValue": "substantiiv, nimisõnaga",
                    "relGroups": [
                        {
                            "relGroupCode": "subject",
                            "relGroupValue": "subject",
                            "collocations": [
                                ...
                                {
                                    "lexemeId": 3119374,
                                    "wordId": 1916321,
                                    "wordValue": "laev naaseb",
                                    "usages": [
                                        {
                                            ...
                                            "id": 455776,
                                            "value": "Laev naaseb peagi liinile.",
                                            "valuePrese": "Laev naaseb peagi liinile.",
                                            "lang": "est",
                                            ...
                                        }
                                    ],
                                    ...
                                }
                            ]
                        },
                        ...
                        {
                            "relGroupCode": "adverbial_alaleütlev",
                            "relGroupValue": "adverbial_alaleütlev",
                            "collocations": [
                                ...
                                {
                                    "lexemeId": 3119382,
                                    "wordId": 1916329,
                                    "wordValue": "tööle naasma",
                                    "usages": [
                                        {
                                            ...
                                            "id": 481565,
                                            "value": "Paljud emad naasevad tööle lapse esimesel-teisel eluaastal.",
                                            "valuePrese": "Paljud emad naasevad tööle lapse esimesel-teisel eluaastal.",
                                            "lang": "est",
                                            ...
                                        }
                                    ],
                                    ...
                                },
                                ...
                            ]
                        },
                        ...
                    ]
                },
                ...
            ],
            "secondaryCollocations": [
                ...
                {
                    "lexemeId": 2975434,
                    "wordId": 1772382,
                    "wordValue": "argipäeva naasma",
                    "usages": [
                        {
                            ...
                            "id": 437778,
                            "value": "Nüüd on õige aeg pidutsemine lõpetada ja naasta argipäeva.",
                            "valuePrese": "Nüüd on õige aeg pidutsemine lõpetada ja naasta argipäeva.",
                            "lang": "est",
                            ...
                        }
                    ],
                    ...
                },
                ...
            ],
            ...
        },
        ...
    ],
    ...
}

```
 </details> 

### 5. SÜNONÜÜM/ANTONÜÜM
**Probleem:**  
Tean ühte sõna ja tahan selle sünonüüme või antonüüme teada.

> **Pane tähele!**  
> Sünonüümid on sõnad, mille tähendus on sama, nt ilus ja kaunis.  
> 
> Antonüümid on sisulise tunnusjoone poolt vastandlikud, kuid tähenduse sisu poolest kokkulangevad sõnapaarid, näiteks must ja valge, soe ja külm.

**Näide:**  
Leida sõnale "kumer" sünonüüme ja antonüüme.  
Selleks tuleb sooritada päring `GET api/word/details/{wordId}`, kasutades otsitava sõna id-numbrit. 

Päringu `GET api/word/details/187665` vastusest leiad sünonüümid iga ilmiku jaoks rajalt
`$lexemes[*]synonymLangGroups[*].synonyms[*].words[*].wordValue`, kuid pane tähele, et `synonymLangGroups[*].lang` 
lähtekeel oleks sobiv. Eesti keele jaoks `"lang": "est"`.   

Samast vastusest leiad antonüümid ehk vastandsõnad iga ilmiku kohta tähenduste seoste juurest rajalt `$lexemes[*].meaning.relations[*].wordValue`, kuid seal leidub lisaks eestikeelsetele ka võõrkeelseid vastandsõnu.

Lisaks võivad kasulikuks osutuda ka osasünonüümid ehk sarnased sõnad, mis on moodustamise järgi jagatud gruppidesse
`$wordRelationDetails[*].primaryWordRelationGroups` objektis.

| Sisu           | JSON-i tee                                                                  | Andmetüüp | Näide     |
|----------------|-----------------------------------------------------------------------------|-----------|-----------|
| Sünonüüm       | `$lexemes[*].synonymLangGroups[*].synonyms[*].words[*].wordValue`           | String    | "kumerik" |
| Sarnaseid sõnu | `$wordRelationDetails[*].primaryWordRelationGroups[*].members[*].wordValue` | String    | "ümar"    |
| Antonüüm       | `$lexemes[*].meaning.relations[*].wordValue`                                | String    | "nõgujas" |

<details>
<summary>Näita lühendatud vastust</summary>

```
{
       "word": {
           "wordId": 187665,
           "wordValue": "kumer",
           "wordValuePrese": "kumer",
           "homonymNr": 1,
           "lang": "est",
           ...
       },
       "lexemes": [
           {
               "lexemeWord": {
                   "wordId": 187665,
                   "wordValue": "kumer",
                   ...
               },
               "meaning": {
                   "meaningId": 676537,
                   ...
                   "relations": [
                       {
                           "id": 10263,
                           "lexemeId": null,
                           "meaningId": 71105,
                           "wordId": 425725,
                           "wordValue": "nõgujas",
                           "wordValuePrese": "nõgujas",
                           "wordLang": "est",
                           ...
                           "relTypeCode": "antonüüm",
                           "relTypeLabel": "vastand, antonüüm",
                            ...
                       },
                       ...
                       {
                           "id": 10263,
                           "lexemeId": null,
                           "meaningId": 71105,
                           "wordId": 209567,
                           "wordValue": "nõgus",
                           "wordValuePrese": "nõgus",
                           "wordLang": "est",
                           ...
                           "relTypeCode": "antonüüm",
                           "relTypeLabel": "vastand, antonüüm",
                           ...
                       }
                   ],
                   ...
               },
               "lexemeId": 1221368,
               "wordId": 187665,
               "meaningId": 676537,
               "datasetCode": "eki",
               "datasetName": "EKI ühendsõnastik 2025",
               ...
               "synonymLangGroups": [
                   ...
                   {
                       "lang": "est",
                       "selected": false,
                       "synonyms": [
                           {
                               "type": "MEANING_WORD",
                               ...
                               "words": [
                                   {
                                       "wordId": 1467878,
                                       "wordValue": "kumerik",
                                       ...
                                   }
                               ],
                               ...
                           },
                           {
                               "type": "MEANING_REL",
                               ...
                               "words": [
                                   {
                                       "wordId": 187729,
                                       "wordValue": "kummis",
                                       ...
                                   }
                               ],
                               ...
                           },
                           {
                               "type": "MEANING_REL",
                               ...
                               "words": [
                                   {
                                       "wordId": 175840,
                                       "wordValue": "kaardus",
                                       ...
                                   }
                               ],
                               ...
                           },
                           ...
                       ],
                       ...
                   },
                   ...
               ],
               ...
           },
           ...
       ],
       "wordRelationDetails": {
           "wordSynRelations": null,
           "primaryWordRelationGroups": [
               {
                   "id": null,
                   "groupTypeCode": "ühend",
                   "groupTypeLabel": "Ühendid",
                   "members": null
               },
               {
                   "id": null,
                   "groupTypeCode": "ls-järelosaga",
                   "groupTypeLabel": "Liitsõnad järelosaga (põhisõna)",
                   "members": [
                       {
                           "id": 2842907,
                           "lexemeId": null,
                           "meaningId": null,
                           "wordId": 366757,
                           "wordValue": "kaksikkumer",
                           "wordValuePrese": "kaksikkumer",
                           "wordLang": "est",
                           ...
                       },
                       ...
                   ]
               },
               {
                   "id": null,
                   "groupTypeCode": "ls-esiosaga",
                   "groupTypeLabel": "Liitsõnad esiosaga (laiend)",
                   "members": [
                       {
                           "id": 2874601,
                           "lexemeId": null,
                           "meaningId": null,
                           "wordId": 187671,
                           "wordValue": "kumerlääts",
                           "wordValuePrese": "kumerlääts",
                           "wordLang": "est",
                           ...
                       },
                       ...
                   ]
               },
               {
                   "id": null,
                   "groupTypeCode": "raw",
                   "groupTypeLabel": "Veel sarnaseid sõnu",
                   "members": [
                       ...
                       {
                           "id": 621411,
                           "lexemeId": null,
                           "meaningId": null,
                           "wordId": 187672,
                           "wordValue": "kumerpeegel",
                           "wordValuePrese": "kumerpeegel",
                           "wordLang": "est",
                           ...
                       },
                       {
                           "id": 621412,
                           "lexemeId": null,
                           "meaningId": null,
                           "wordId": 263834,
                           "wordValue": "ümar",
                           "wordValuePrese": "ümar",
                           "wordLang": "est",
                           ...
                       },
                       ...
                   ]
               },
               ...
           ],
           ...
       },
       ...
}

```
 </details> 

### 6. PARADIGMAD

**Probleem:**  
Tahan teada, kuidas mingit sõna käänata või pöörata.

> **Pane tähele!**  
> Paradigma on muutevormistik ehk käändsõna või pöördsõna muutevormide hulk, mis on kategooriate abil korrastatud (ja tabelina esitatud). Näiteks nimisõna "käsi" muutevormid on: käsi, käe, kätt… jne)

**Näide:**  
Leida sõna "jooksma" muutevormid.
Selleks tuleb sooritada päring `GET api/word/details/{wordId}`, kasutades otsitava sõna id-numbrit.

Päringu `GET api/word/details/173981` vastusest leiad kõik otsitavad vormid rajalt `$word.paradigms[*].forms[*].value`.  

| Sisu           | JSON-i tee                               | Andmetüüp | Näide                                      |
|----------------|------------------------------------------|-----------|--------------------------------------------|
| Paradigma vorm | `$word.paradigms[*].forms[*].value`      | String    | "jooksen"                                  |
| Vormi nimetus  | `$word.paradigms[*].forms[*].morphValue` | String    | "kindla kõneviisi oleviku ainsuse 1.pööre" |

 <details>
<summary>Näita lühendatud vastust</summary>

```
{
    "word": {
        "wordId": 173981,
        "wordValue": "jooksma",
        "wordValuePrese": "jooksma",
        "homonymNr": 1,
        "lang": "est",
        ...
        "paradigms": [
            {
                "paradigmId": 1384781,
                "comment": null,
                "inflectionType": "32",
                "inflectionTypeNr": "32",
                "wordClass": "verb",
                "forms": [
                    {
                        "id": 16071175,
                        "value": "jooksen",
                        ...
                        "morphValue": "kindla kõneviisi oleviku ainsuse 1.pööre",
                        ...
                    },
                    {
                        "id": 16071176,
                        "value": "jooksed",
                        ...
                        "morphValue": "kindla kõneviisi oleviku ainsuse 2.p.",
                        ...
                    },
                    {
                        "id": 16071177,
                        "value": "jookseb",
                        ...
                        "morphValue": "kindla kõneviisi oleviku ainsuse 3.p.",
                        ...
                    },
                    {
                        "id": 16071178,
                        "value": "jookseme",
                        ...
                        "morphValue": "kindla kõneviisi oleviku mitmuse 1.p.",
                        ...
                    },
                    {
                        "id": 16071179,
                        "value": "jooksete",
                        ...
                        "morphValue": "kindla kõneviisi oleviku mitmuse 2.p.",
                        ...
                    },
                    {
                        "id": 16071180,
                        "value": "jooksevad",
                        ...
                        "morphValue": "kindla kõneviisi oleviku mitmuse 3.p.",
                        ...
                    },
                    ...
                    {
                        "id": 16071227,
                        "value": "joostud",
                        ...
                        "morphValue": "mitmeosalise verbi pööratud ja eitatud tud-kesksõna",
                       ...
                    }
                ],
                ...
            }
        ],
        ...
    },
    "lexemes": [
        ...
        ],
    "wordRelationDetails": {
        ...
    },
    ...
}

```
</details>  

Alternatiivina saab päringust `GET api/paradigm/details/173981` kätte ainult paradigma vormid, mille leiab rajalt `$[*].paradigmForms.value`.

   <details>
   <summary>Näita lühendatud vastust</summary>

```
[
    {
        "id": 1384781,
        "wordId": 173981,
        "wordClass": "verb",
        "comment": null,
        "inflectionTypeNr": "32",
        "inflectionType": "32",
        "secondary": false,
        "paradigmForms": [
            {
                "formId": 16071175,
                "value": "jooksen",
                "valuePrese": "jookse<eki-form>n</eki-form>",
                "morphCode": "IndPrSg1",
                "morphGroup1": "pöördelised vormid",
                "morphGroup2": "kindel kõneviis",
                "morphGroup3": "olevik",
                "displayLevel": 2,
                "displayForm": "jookse[n",
                "audioFile": null,
                "morphExists": true,
                "orderBy": 724112,
                "questionable": false
            },
            {
                "formId": 16071176,
                "value": "jooksed",
                "valuePrese": "jookse<eki-form>d</eki-form>",
                "morphCode": "IndPrSg2",
                "morphGroup1": "pöördelised vormid",
                "morphGroup2": "kindel kõneviis",
                "morphGroup3": "olevik",
                "displayLevel": 2,
                "displayForm": "jookse[d",
                "audioFile": null,
                "morphExists": true,
                "orderBy": 724113,
                "questionable": false
            },
            {
                "formId": 16071177,
                "value": "jookseb",
                "valuePrese": "jookse<eki-form>b</eki-form>",
                "morphCode": "IndPrSg3",
                "morphGroup1": "pöördelised vormid",
                "morphGroup2": "kindel kõneviis",
                "morphGroup3": "olevik",
                "displayLevel": 1,
                "displayForm": "jookse[b",
                "audioFile": "h_10275.mp3",
                "morphExists": true,
                "orderBy": 724114,
                "questionable": false
            },
            ...
        ],
        "removeTagNames": null
    }
]
```
 </details> 

### 7. TÕLGE
**Probleem:**  
Mul on üks sõna ja tahan näha selle tõlget.

> **Pane tähele!**  
> Tõlkesõna on sama tähendusega sõna teises keeles.
> 
> Võõrkeelseid keelendeid on Ekilexis väga ebaühtlaselt, enamik neist on lisandunud sõnaraamatute sisu laadimisega Ekilexi andmekogudesse.

**Näide:**  
Leida sõna "samet" vene- ja ingliskeelne vaste.
Selleks tuleb sooritada päring `GET api/meaning/search/{word}`, kasutades parameetrina otsitavat sõna.

Päringu `GET api/meaning/search/samet` vastusest leiad iga tähenduse kohta olemasolevad tõlkevasted 
json-i rajalt `$.results[*].meaningWords[*].wordValue` ning selle keel on märgitud real `$.results[*].meaningWords[*].lang`.  

| Sisu                 | JSON-i tee                               | Andmetüüp | Näide    |
|----------------------|------------------------------------------|-----------|----------|
| Samatähenduslik sõna | `$.results[*].meaningWords[*].wordValue` | String    | "velvet" |
| Keel                 | `$.results[*].meaningWords[*].lang`      | String    | "eng"    |

<details>
<summary>Näita lühendatud vastust</summary>

   ```
   {
    "meaningCount": 1,
    "wordCount": 5,
    "resultCount": 1,
    "results": [
        {
            "meaningId": 94106,
            "meaningDomains": null,
            "meaningWords": [
                {
                    "wordId": 230960,
                    "wordValue": "samet",
                    "wordValuePrese": "samet",
                    "homonymNr": 1,
                    "lang": "est",
                    ...
                },
                {
                    "wordId": 561494,
                    "wordValue": "velvet",
                    "wordValuePrese": "velvet",
                    "homonymNr": 1,
                    "lang": "eng",
                    ...
                },
                {
                    "wordId": 385147,
                    "wordValue": "бархат",
                    "wordValuePrese": "б<eki-stress>а</eki-stress>рхат",
                    "homonymNr": 1,
                    "lang": "rus",
                    ...
                },
                ...
            ],
            "meaningWordsExist": true
        }
    ],
    ...
}
   ```
</details>

### 8. VÕRDLUSASTMED 

**Probleem:**  
Tahan teada, millised on sõna algvõrre, keskvõrre ja ülivõrre.   

> **Pane tähele!**  
>Võrdlusvorme näidatakse omadussõnade ja määrsõnade juures (nt külm, külmem, külmim; kaugel, kaugemal, kõige kaugemal).
 
**Näide:**  
Leida sõna "roheline" võrdlusastmed. Päringu `GET api/word/details/228454` vastusest leiab kesk- ja ülivõrde sõnad rajalt
`$.wordRelationDetails.secondaryWordRelationGroups[*].members[*].wordValue`. 

| Sisu                 | JSON-i tee                                                                  | Andmetüüp | Näide       |
|----------------------|-----------------------------------------------------------------------------|-----------|-------------|
| Võrdlusastme sõna    | `$.wordRelationDetails.secondaryWordRelationGroups[*].members[*].wordValue` | String    | "rohelisem" |
| Võrdlusastme nimetus | `$.wordRelationDetails.secondaryWordRelationGroups[*].groupTypeLabel`       | String    | "keskvõrre" |

<details>
<summary>Näita lühendatud vastust</summary>

```
     {
    "word": {
        "wordId": 228454,
        "wordValue": "roheline",
        "wordValuePrese": "roheline",
        "homonymNr": 1,
        "lang": "est",
        ...
    },
    "lexemes": [
        ...
    ],
    "wordRelationDetails": {
        "wordSynRelations": null,
        "primaryWordRelationGroups": [
            ...
        ],
        "secondaryWordRelationGroups": [
            {
                "id": null,
                "groupTypeCode": "komp",
                "groupTypeLabel": "keskvõrre",
                "members": [
                    {
                        "id": 41355,
                        "lexemeId": null,
                        "meaningId": null,
                        "wordId": 275471,
                        "wordValue": "rohelisem",
                        "wordValuePrese": "rohelisem",
                        "wordLang": "est",
                        ...
                        "relTypeCode": "komp",
                        "relTypeLabel": "keskvõrre",
                        ...
                    }
                ]
            },
            {
                "id": null,
                "groupTypeCode": "superl",
                "groupTypeLabel": "ülivõrre",
                "members": [
                    {
                        "id": 42719,
                        "lexemeId": null,
                        "meaningId": null,
                        "wordId": 276149,
                        "wordValue": "kõige rohelisem",
                        "wordValuePrese": "kõige rohelisem",
                        "wordLang": "est",
                        ...
                        "relTypeCode": "superl",
                        "relTypeLabel": "ülivõrre",
                        ...
                    },
                    {
                        "id": 2704851,
                        "lexemeId": null,
                        "meaningId": null,
                        "wordId": 1173569,
                        "wordValue": "rohelisim",
                        "wordValuePrese": "rohelisim",
                        "wordLang": "est",
                        ...
                        "relTypeCode": "superl",
                        "relTypeLabel": "ülivõrre",
                        ...
                    }
                ]
            },
            ...
        ],
        ...
    },
    ...
}

```
 </details> 

### 9. KÕIK SÕNASTIKUANDMED
**Probleem:**  
Tahan nimekirja kõikidest sõnadest, mis ühes sõnakogus leiduvad.

> **Pane tähele!**  
> Kõikide sõnakogude koodid ja kirjeldused leiad päringuga `GET api/datasets`.

**Näide:**  
Leida kõik keelendid juuksuritöö terminibaasist. Tuleb teha päring `GET api/public_word/{dataset}`, kasutades sõnakogu koodi. 

Vastusena päringule `GET api/public_word/juuksed` esitatakse nimekiri sõnakogus leiduvatest keelenditest.

| Sisu                | JSON-i tee    | Andmetüüp | Näide        |
|---------------------|---------------|-----------|--------------|
| Sõnakogu sõna       | `$[*].value`  | String    | "aktivaator" |
| Sõna identifikaator | `$[*].wordId` | Long      | 155995       |
| Sõna keel           | `$[*].lang`   | String    | "est"        |

<details>
<summary>Näita lühendatud vastust</summary>

```
[
    {
        "wordId": 155995,
        "value": "aktivaator",
        "valuePrese": "aktivaator",
        "lang": "est",
        "homonymNr": 1,
        "morphophonoForm": "aktiv`aator",
        "morphExists": true
    },
    {
        "wordId": 156117,
        "value": "alajaotus",
        "valuePrese": "alajaotus",
        "lang": "est",
        "homonymNr": 1,
        "morphophonoForm": "ala+j`aotus",
        "morphExists": true
    },
    {
        "wordId": 708123,
        "value": "blondeerimine",
        "valuePrese": "blondeerimine",
        "lang": "est",
        "homonymNr": 1,
        "morphExists": false
    },
    {
        "wordId": 537519,
        "value": "clipper",
        "valuePrese": "clipper",
        "lang": "eng",
        "homonymNr": 1,
        "morphophonoForm": "clipper",
        "morphExists": false
    },
    ...
]
```
</details>  


### 10. KEELEOSKUSE TASE SÕNADEL

**Probleem:**  
Tahan teada, millisele keeleoskuse tasemele mingi sõna vastab.

**Näide:**  
Leida keelendi "tasku" keeleoskustase.  
Tuleb teha päring `GET api/word/details/{wordId}`, kasutades otsitava keelendi identifikaatorit.

Päringu `GET api/word/details/242491` vastusest leiad iga ilmiku jaoks selle keeleõppe taseme rajalt `$.lexemes[*].lexemeProficiencyLevelCode`.

| Sisu                  | JSON-i tee                                | Andmetüüp | Näide |
|-----------------------|-------------------------------------------|-----------|-------|
| Keeleoskustaseme kood | `$.lexemes[*].lexemeProficiencyLevelCode` | String    | "A2"  |

<details>
<summary>Näita lühendatud vastust</summary>

```
{
    "word": {
        "wordId": 242491,
        "wordValue": "tasku",
        "wordValuePrese": "tasku",
        "homonymNr": 1,
        "lang": "est",
        ...
    },
    "lexemes": [
        {
            "lexemeWord": {
                ...
            },
            "meaning": {
                ...
            },
            "lexemeId": 1357047,
            "wordId": 242491,
            "meaningId": 36314,
            "datasetCode": "eki",
            "datasetName": "EKI ühendsõnastik 2025",
            ...
            "lexemeProficiencyLevelCode": "A2",
            "lexemeProficiencyLevel": {
                "name": "PROFICIENCY_LEVEL",
                "code": "A2",
                "value": "A2"
            },
            ...
        },
        ...
    ],
    ...
}
```
</details>

### 11. LAIENDATUD TEKSTIOTSING

Järgmistes API-otspunktides saad otsingut laiendada, kasutades `{word}` parameetris metamärke.
* `api/word/search/{word}`  
* `api/word/search/{word}/{datasets}`  
* `api/meaning/search/{word}`  
* `api/meaning/search/{word}/{datasets}`

Otsingus esindab metamärk tärn (*) suvalist hulka sümboleid, küsimärk (?) üht sümbolit.  

Näiteks päringu `api/word/search/*loojang` vastusest ilmnevad sõnad "loojang", "päevaloojang" ja "päikeseloojang". Päringuga `api/word/search/l??ng/eki` leiduvad vasted "laeng" ja "loeng".

<details>
<summary>Näita lühendatud vastust</summary>

```
{
  "totalCount": 2,
  "words": [
    {
      "wordId": 191823,
      "wordValue": "laeng",
      "wordValuePrese": "laeng",
      "homonymNr": 1,
      "lang": "est",
      ...
    },
    {
      "wordId": 195965,
      "wordValue": "loeng",
      "wordValuePrese": "loeng",
      "homonymNr": 1,
      "lang": "est",
      ...
    }
  ]
}
```
 </details> 
