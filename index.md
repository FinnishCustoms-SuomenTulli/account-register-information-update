# Tilirekisterin päivitysrajapintakuvaus

*Dokumentin versio 0.8*

[English translation](https://github.com/FinnishCustoms-SuomenTulli/Tilirekisteri/blob/master/rajapintakuvaus_en.pdf)

[Svensk översättning](https://github.com/FinnishCustoms-SuomenTulli/Tilirekisteri/blob/master/rajapintakuvaus_sv.pdf)

Huom. käännösten versiot tulevat pienellä viiveellä suomenkieliseen rajapintakuvaukseen verrattuna.

## Versiohistoria

Versio|Päivämäärä|Kuvaus|Tekijä
---|---|---|---
0.3|26.4.2019|Alustava versio|AP,TV
0.4|9.5.2019|Sanomarakenne päivitetty alustavasti ISO 20022 mukaillen|AP|
0.5|22.5.2019|Päivityksiä standardinmukaisuuteen liittyen|AP|
0.6|31.5.2019|Tarkennuksia rajapintakuvauksen käyttöohjeistukseen|AP|
0.7|10.6.2019|Tarkennus virtuaalivaluutan tarjoajien osalta|TV|
0.8|11.9.2019|Rajapintakuvaus jaettu kahdeksi dokumentiksi|AL|

## Sisällysluettelo

1. [Johdanto](#luku1)  
  1.1 Termit ja lyhenteet  
  1.2 Dokumentin tarkoitus ja kattavuus  
  1.3 Yleiskuvaus  
2. [Aktiviteettien kuvaus](#luku2)  
  2.1 Pankki- ja maksutilitietojen toimittaminen  
3. [Tietoturva](#tietoturva)  
  3.1 Tunnistaminen
4. [Tilirekisterin päivitysrajapinta](#päivitysrajapinta)  

## 1. Johdanto <a name="luku1"></a>

### 1.1 Termit ja lyhenteet

Lyhenne tai termi|Selite
---|---
Rajapinta|Standardin mukainen käytäntö tai yhtymäkohta, joka mahdollistaa tietojen siirron laitteiden, ohjelmien tai käyttäjän välillä. 
WS (Web Service)|Verkkopalvelimessa toimiva ohjelmisto, joka tarjoaa standardoitujen internetyhteyskäytäntöjen avulla palveluja sovellusten käytettäväksi. Tilirekisterin tarjoamia palveluja ovat tietojen toimittaminen, tietopyyntö, ja tietojen kysely. Tiedonhakujärjestelmä tarjoaa palveluna tietojen kyselyn.
Endpoint|Rajapintapalvelu, joka on saatavilla tietyssä verkko-osoitteessa

### 1.2 Dokumentin tarkoitus ja kattavuus

Tämä dokumentti on pankki- ja maksutilirekisterin päivitysrajapinnan rajapintakuvaus.

### 1.3 Yleiskuvaus

Tulli on perustanut Tilirekisterihankkeen, joka toteuttaa (EU) 2018/843 direktiivin ja sen täytäntöön panemiseksi säädetyn, Suomen lainsäädäntöön perustuvan pankki- ja maksutilien valvontajärjestelmän.

Järjestelmä koostuu kahdesta osasta: pankki- ja maksutilirekisteristä sekä tiedonhakujärjestelmästä. 

Tässä dokumentissa kuvataan tilirekisterin päivitysrajapinta.

## 2. Aktiviteettien kuvaus <a name="luku2"></a>

Tässä luvussa on esitettu pankki- ja maksutilitietojen toimittaminen vuokaavioina.

### 2.1 Pankki- ja maksutilitietojen toimittaminen tilirekisteriin

Kuvassa 2.1 on esitetty vuokaaviona pankki- ja maksutilitietojen toimittaminen tilirekisteriin.

![Pankki- ja maksutilitietojen toimittaminen](diagrams/flowchart_update.png "Pankki- ja maksutilitietojen toimittaminen")  
*__Kuva 2.1.__ Pankki- ja maksutilitietojen toimittaminen*

Kuvasta nähdään, että päivitysrajapinta on synkroninen. HTTP-vastauksen bodyssa palautetaan joko tieto onnistuneesta päivityksestä tai virheestä esimerkiksi sanoman validoinnissa.

## <a name="tietoturva"></a> 3. Tietoturva
  
### 3.1 Tunnistaminen

Tarkentuu.

Päivitysrajapinnan sanomat allekirjoitetaan JWS-allekirjoituksella (PKI). Tarkempi sanomien allekirjoitusten kuvaus lisätään tähän dokumenttiin myöhemmin.

## <a name="päivitysrajapinta"></a> 4. Tilirekisterin päivitysrajapinnan yleiskuvaus

Päivitysrajapinta toteutetaan REST/JSON-menetelmällä.

Rajapinnan käyttäjän (tiedon luovuttajan) on lähetettävä vähintään yksi (1) minimisanoma (ks. alla *-merkityt kentät täytetty) määritellyssä ajassa, esim. kerran kuukaudessa (watchdog timer reset). Jos yhtään viestiä ei tänä aikana toimiteta, lähetetään vianselvittämispyyntö. Jos tähän ei määritellyssä ajassa reagoida, aloitetaan sanktiomenettely.

Kyselysanomat allekirjoitetaan JWS-allekirjoituksella (PKI, tarkentuu myöhemmin).

Jokaisessa sanomassa tulee olla mukana luontipäivämäärä. 

Jokaisen sanoman tulee sisältää tietojen toimittajan Y-tunnus senderBusinessId propertyssa.

Erikseen määriteltyjen (ks. alla) tietueiden tulee sisältää `servicerIssuedId` property, joka on mikä tahansa tietojen toimittajan antama hyvien tapojen mukainen yksiselitteinen tunniste tietueelle merkkijonona. Esimerkiksi UUIDv4 on hyvä `servicerIssuedId`. Tämän propertyn perusteella tietueet pystytään tunnistamaan esimerkiksi henkilön nimen tai hetun vaihtuessa.

Tili-, asiakkuus-, ja tallelokeroihin liittyvät roolitiedot ja henkilötiedot voidaan merkitä virheelliseksi asettamalla `disputed` property arvoon true. Tällöin uusin versio tiedosta merkitään "virheelliseksi ilmoitettu" -tilaan alkaen sanoman saapumispäivämäärästä.

Sanomassa tulee olla mukana sarjanumero. Sarjanumeroa käytetään varmistamaan, ettei sanomia ole jäänyt saapumatta.

Seuraavassa taulukossa on listattu rajapinnan endpointit. 

|HTTP-metodi|Polku|Tarkoitus ja toiminnallisuus|
|---|---|---|
POST|/information-update|Tietojen toimitusvelvolliset (Maksulaitokset, sähkörahayhteisöt, virtuaalivaluutan tarjoajat tai finanssivalvonnalta saadulla poikkeusluvalla luottolaitokset) käyttävät tätä endpointia asiakkuuksien, tilitietojen sekä tallelokeroiden tietojen toimittamiseen tilirekisteriin.|

Endpointia käytetään tietojen toimittamiseen tilirekisteriin. Sanomassa toimitetaan tiedot asiakkuuksista, tileista ja tallelokeroista.

### Notaatio

Rajapintatietueiden rakenteiseen kuvaukseen käytetään seuraavanlaista notaatiota:
```
Objekti {
  tietue                    tietotyyppi
}
```

### Kuvaus

Alla on kuvattu endpointin vastaanottama ja palauttama sanomarakenne. Pakolliset tietueet merkitty tähdellä (*). Valinta merkitty kahdella tähdellä (**).

#### Request bodyn määritys
```
{ 
  creationDateTime*         date-time
  senderBusinessId*         string
  messageSerialNumber*      integer
  customers                 [ Customer ]
  accounts                  [ Account ]
  safetyDepositBoxes        [ SafetyDepositBox ]                   
}
```

#### <a name="InformationUpdate response"></a> HTTP vastaukset

200 OK

400 Bad Request

Body
```
{
  errorMessage              string
}
```

405 Method Not Allowed

Body
```
{
  errorMessage              string
}
```

500 Internal Server Error

Body
```
{
  errorMessage              string
}
```



#### <a name="Customer"></a> Tietueiden määritykset

Customer
```
Customer { 
  startDate*                date-time
  endDate                   date-time
  identification*           Identification
}
```
Account
```
Account {
  id*                       AccountIdentification
  servicerIssuedId*         string
  openingDate*              date-time
  closingDate               date-time
  roles*                    [ Role ]
}
```

```
"roles": {
    "type": "array",
    "items": {
        "type": "string"
    },
    "minItems": 1,
    "uniqueItems": true
}
```

AccountIdentification
```
AccountIdentification {
  iban**                    string
  other**                   OtherAccountIdentification
}
```
OtherAccountIdentification
```
OtherAccountIdentification {
  id*                       string
  description*              string
}
```

SafetyDepositBox
```
SafetyDepositBox {
  id*                       string
  servicerIssuedId*         string
  startDate*                date-time
  endDate                   date-time
  roles*                    [ Role ]
}
```

```
"roles": {
    "type": "array",
    "items": {
        "type": "string"
    },
    "minItems": 1,
    "uniqueItems": true
}
```

Identification
```
Identification {
  organisation**            Organisation  
  privatePerson**           PrivatePerson                         
}
```
Role
```
Role {
  startDate*                date-time
  endDate                   date-time
  role*                     RoleType
  identification*           Identification
  disputed                  boolean
}
```

|RoleType|kuvaus|
|:---|:---|
|Owner|Omistaja|
|Access|Käyttöoikeuden haltija|
|Beneficiary|Edunsaaja|

Organisation
```
Organisation {
  servicerIssuedId*         string
  registrationAuthority*    string
  businessId**              string
  businessIdCountryCode*    string
  otherOrganisationId**     OtherOrganisationId
  name                      string
  beneficiaries*            [ Role ]   
  disputed                  boolean
}
```
Jos businessId on suomalainen Y-tunnus, businessIdCountryCode arvoksi asetetaan "FI". Jos Y-tunnusta tai vastaavaa ei ole saatavilla, annetaan otherOrganisationId. Jos yhdistyksellä ei ole Y-tunnusta, käytetään OtherOrganisationId: `{id: string, description: "AssociationRegistrationNumber"}`.

OtherOrganisationId 
```
OtherOrganisationId {
  id*                       string
  description*              string
}
```

PrivatePerson
```
PrivatePerson {
  servicerIssuedId*         string
  lastName*                 string
  firstNames*               string
  hetu**                    string
  birthdate*                date-time
  nationality*              string
  identificationDocumentId**string
  disputed                  boolean
}
```
Suomalaisille henkilöille on liitettävä aina suomalainen henkilötunnus.
