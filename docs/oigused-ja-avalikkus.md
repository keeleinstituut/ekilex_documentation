# Ligipääs, avalikkus ja avaldamine

> Versioon: 1.50 · Uuendatud: november 2026

---

**Lühidalt**

- Sõnaveeb, Keeleõppija Sõnaveeb ja ÕS on avalikud — sisselogimist pole vaja.
- Ekilex nõuab kontot. Uus kasutaja saab konto aktiveerimisel automaatselt muutmisõiguse **katsebaasile** (sõnakogu katsetamiseks, ei ilmu Sõnaveebis).
- Muude sõnakogude jaoks tuleb eraldi õigust taotleda.
- Sõnaveebis kuvatavat sisu uuendatakse igal ööl.

---

## Sisukord

- [Mõisted](#mõisted)
- [Ligipääs ja õigused Ekilexis](#ligipääs-ja-õigused-ekilexis)
- [Avaldamine Sõnaveebis](#avaldamine-sõnaveebis)
- [REST API](#rest-api)
- [KKK](#kkk)

---

## Mõisted

| Mõiste | Seletus |
|---|---|
| **Ekilex** | Leksikograafide ja terminoloogide tööriist sõnastike ja terminikogude koostamiseks. Nõuab autentimist. |
| **EKI ühendsõnastik** | EKI leksikograafide üldkeele sõnaraamat, mida koostatakse leksikograafide vaates. |
| **Terminikogu** | Erialaspetsialistide sõnakogu, mida koostatakse terminoloogide vaates. |
| **Sõnaveeb** | Avalik sõnastikuportaal sonaveeb.ee. Kuvab EKI ühendsõnastiku ja terminikogude avalikku sisu. |
| **Keeleõppija Sõnaveeb** | Sõnaveebi lihtsam vaade A2–B1 keeleõppijatele. Sisu pärineb EKI ühendsõnastikust. |
| **ÕS** | Eesti õigekeelsussõnaraamat ÕS 2025. Andmed on EKI ühendsõnastiku alamhulk. Veebiversioon on külmutatud järgmise ÕSi ilmumiseni. |

---

## Ligipääs ja õigused Ekilexis

Õigused on **sõnakoguspetsiifilised** — ühe sõnakogu õigus ei anna ligipääsu teisele.

| Olek | Mida saab teha |
|---|---|
| **Sisselogimata** | Ainult sisselogimine, konto loomine, parooli taastamine |
| **Äsja aktiveeritud konto** | Kõigi nähtavate sõnakogude avalikud kirjed + muutmisõigus katsebaasile |
| **Lugeja (READ)** | Lisaks oma sõnakogu mitteavalikud kirjed |
| **Muutja (CRUD)** | Lugeja + kirjete lisamine, muutmine, kustutamine; allikate haldus |
| **Omanik (OWN)** | Muutja + sõnakogu seaded + õiguste jagamine teistele |
| **Ülevaataja** | Näeb ja muudab kõike kõigis sõnakogudes |
| **Administraator** | Kasutajate ja süsteemi haldus; andmeõigused sõltuvad sõnakoguõigustest |

### Sõnakogu nähtavus ja avalikkus

Igal sõnakogul on kaks seadet:

- **Nähtav** — sõnakogu ilmub Ekilexis otsingutulemuste seas ja filtrites ning on nähtav ligipääsutaotluste lehel. Mittenähtavat sõnakogu näevad ainult kasutajad, kellele on eraldi selle sõnakogu õigus antud, ning ülevaataja ja admin.
- **Avalik** — sõnakogu sisu võib Sõnaveebis ilmuda. Lisaks peab ka üksik kirje olema avalikuks märgitud (ilmik/termin, seletus, kasutusnäide, märkus, allikas jm).

---

## Avaldamine Sõnaveebis

**Terminikogud** — sisu jõuab Sõnaveebi automaatselt igal ööl, kui sõnakogu on avalik ja kirje on avalikuks märgitud.

**EKI ühendsõnastik** — lisaks tuleb toimetajal kirje eraldi avaldada ühte või mitmesse sihtmärki:

| Sihtmärk | Märkus |
|---|---|
| **Sõnaveeb** | Peamine avalik vaade |
| **Keeleõppija Sõnaveeb** | Sõnaveebi alamleht A2–B1 taseme sõnavara ja seotud info otsinguks|
| **ÕS** | Veebiversioon on külmutatud — avaldamine ei muuda praegu nähtavat ÕSi |

---

## REST API

Päringud nõuavad API võtit HTTP päisena. API võti on nähtav kasutajaprofiilis.

| Päring | Nõuded |
|---|---|
| Lugemine | Kehtiv API võti — tagastab ainult avalikud andmed nähtavatest sõnakogudest |
| Kirjutamine | API võti + `apiCrud` lipp (admin lülitab sisse) + CRUD-õigus sihtmärk sõnakogu andmetele |

Interaktiivne dokumentatsioon: [ekilex.ee/api-reference.html](https://ekilex.ee/api-reference.html)

---

## KKK

**Tegin konto. Mida ma kohe näen ja teha saan?**

Konto aktiveerimisel saad automaatselt muutmisõiguse katsebaasile — saad seal kirjeid lisada, muuta ja kustutada. Saad sirvida teiste sõnakogude avalikke andmeid, aga muuta neid ei saa. Päris tööks tuleb taotleda konkreetse sõnakogu õigust.

---

**Kas Sõnaveebi kasutaja näeb kohe muudatusi, mida Ekilexis teen?**

Ei. Sõnaveeb uuendatakse igal ööl — täna tehtud muudatused jõuavad sinna ööl vastu homset.

---

**Kas ma pean taotlema mingeid õigusi, et Ekilexis mingi sõnakoguga tutvuda?**

Ei. Õigusi taotlemata saad sirvida sõnakogu avalikke andmeid.