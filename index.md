# Tilirekisterin päivitysrajapintakuvaus

*Dokumentin versio 0.9*

## Versiohistoria

Versio|Päivämäärä|Kuvaus|Tekijä
---|---|---|---
0.3|26.4.2019|Alustava versio|AP,TV
0.4|9.5.2019|Sanomarakenne päivitetty alustavasti ISO 20022 mukaillen|AP|
0.5|22.5.2019|Päivityksiä standardinmukaisuuteen liittyen|AP|
0.6|31.5.2019|Tarkennuksia rajapintakuvauksen käyttöohjeistukseen|AP|
0.7|10.6.2019|Tarkennus virtuaalivaluutan tarjoajien osalta|TV|
0.8|11.9.2019|Rajapintakuvaus jaettu kahdeksi dokumentiksi|AL|
0.9|18.10.2019|Tekstimuutoksia varmenteiden osalta|TV|

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
WS (Web Service)|Verkkopalvelimessa toimiva ohjelmisto, joka tarjoaa standardoitujen internetyhteyskäytäntöjen avulla palveluja sovellusten käytettäväksi. Tilirekisterin tarjoamia palveluja ovat tietojen toimittaminen, tietopyyntö ja tietojen kysely. Tiedonhakujärjestelmä tarjoaa palveluna tietojen kyselyn.
Endpoint|Rajapintapalvelu, joka on saatavilla tietyssä verkko-osoitteessa
REST|(Representational State Transfer) HTTP-protokollaan perustuva arkkitehtuurimalli ohjelmointirajapintojen toteuttamiseen.
JSON|(JavaScript Object Notation) avoimen standardin tiedostomuoto tiedonvälitykseen.

### 1.2 Dokumentin tarkoitus ja kattavuus

Tämä dokumentti on pankki- ja maksutilirekisterin päivitysrajapinnan rajapintakuvaus.

### 1.3 Yleiskuvaus

Tämä dokumentti on osa Tullin määräystä pankki- ja maksutilien valvontajärjestelmästä. Dokumentin tarkoitus on antaa ohjeet tiedon luovuttajille pankki- ja maksutilirekisterin (myöhemmin Tilirekisteri) päivitysrajapinnan toteuttamiseen. Tätä dokumenttia täydentää Pankki- ja maksutilirekisterin käyttöönoton ja ylläpidon ohje.

Järjestelmä koostuu kahdesta osasta: pankki- ja maksutilirekisteristä sekä tiedonhakujärjestelmästä. 

Tässä dokumentissa kuvataan Tilirekisterin päivitysrajapinta.

## 2. Aktiviteettien kuvaus <a name="luku2"></a>

Tässä luvussa on esitetty pankki- ja maksutilitietojen toimittaminen vuokaavioina.

### 2.1 Pankki- ja maksutilitietojen toimittaminen Tilirekisteriin

Kuvassa 2.1 on esitetty vuokaaviona pankki- ja maksutilitietojen toimittaminen Tilirekisteriin.

![Pankki- ja maksutilitietojen toimittaminen](diagrams/flowchart_update.png "Pankki- ja maksutilitietojen toimittaminen")  
*__Kuva 2.1.__ Pankki- ja maksutilitietojen toimittaminen*

Kuvasta nähdään, että päivitysrajapinta on synkroninen. HTTP-vastauksen bodyssa palautetaan joko tieto onnistuneesta päivityksestä tai virheestä esimerkiksi sanoman validoinnissa.

## <a name="tietoturva"></a> 3. Tietoturva
  
### 3.1 Tunnistaminen

#### Lähtevän sanoman allekirjoitusvarmenne

Lähtevät sanomat on automaattisesti allekirjoitettava käyttäen x.509 (versio 3) palvelinvarmennetta, josta käy ilmi ko. tiedon luovuttajan Y-tunnus tai ALV-tunnus. Allekirjoituksen hyväksyminen edellyttää, että

joko  
a) varmenne on VRK:n myöntämä, voimassa, eikä esiinny VRK:n ylläpitämällä sulkulistalla ja varmenteen kohteen serialNumber attribuuttina on kyseisen tiedon luovuttajan Y-tunnus tai ALV-tunnus

tai  
b) varmenne on eIDAS-hyväksytty sivustojen tunnistamisvarmenne, voimassa, eikä esiinny varmenteen tarjoajan ylläpitämällä ajantasaisella sulkulistalla ja varmenteen kohteen organizationIdentifier-attribuuttina on kyseisen tiedon luovuttajan Y-tunnus tai ALV-tunnus.

#### Saapuvan sanoman allekirjoitusvarmenne

Tilirekisteristä saapuvien sanomien allekirjoitus on hyväksyttävä, edellyttäen että  
a) allekirjoituksessa käytetty varmenne on VRK:n myöntämä, voimassa, eikä esiinny VRK:n ylläpitämällä sulkulistalla  
b) varmenteen kohteen serialNumber attribuuttina on Tullin Y-tunnus “0245442-8” tai kirjaimet FI ja Tullin Y-tunnuksen numero-osa: “FI02454428”.

#### Tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon tietoliikennevarmenne

Tietoliikenne on suojattava (salaus ja vastapuolen tunnistus) x.509 (versio 3) varmenteita käyttäen.

Yhteyden muodostukseen on käytettävä palvelinvarmennetta, josta käy ilmi ko. tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus. Tiedon luovuttajan valtuuttamalla taholla tarkoitetaan esim. palvelukeskusta, jonka tiedon luovuttaja on valtuuttanut puolestaan huolehtimaan ilmoitusten muodostamisesta ja/tai lähettämisestä. Tätä koskeva valtuutus on toimitettava Tullille kirjallisesti.

Allekirjoituksen hyväksyminen edellyttää, että

joko  
a) palvelinvarmenteen on myöntänyt VRK, varmenne on voimassa, eikä esiinny VRK:n sulkulistalla, varmenteen kohteen serialNumber attribuuttina on kyseisen tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus

tai  
b) palvelinvarmenne on eIDAS-hyväksytty sivustojen tunnistamisvarmenne, voimassa, eikä esiinny varmenteen tarjoajan ylläpitämällä ajantasaisella sulkulistalla ja varmenteen kohteen organizationIdentifier-attribuuttina on kyseisen tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus.

Mikäli tiedon luovuttajan tietoliikennevarmenteessa ja lähtevän sanoman allekirjoitusvarmenteessa käytetään samaa Y-tunnusta tai ALV-tunnusta, voidaan kumpaankin tarkoitukseen käyttää samaa varmennetta.

#### Tilirekisterin tietoliikennevarmenne

Tiedon luovuttaja tunnistaa yhteyden vastapuolen Tilirekisteriksi palvelinvarmenteen perusteella seuraavin edellytyksin:  
a) Tilirekisterin ylläpitäjän (Tullin) palvelinvarmenteen on myöntänyt VRK, varmenne on voimassa eikä esiinny VRK:n ylläpitämällä sulkulistalla  
b) varmenteen kohteen serialNumber attributti on “FI02454428” tai “0245442-8”.

### 3.2 Yhteyksien suojaaminen

Tilirekisterin päivitysrajapinnan yhteydet on suojattava TLS-salauksella käyttäen TLS-protokollan versiota 1.2 tai korkeampaa. Yhteyden molemmat päät tunnistetaan yllä kuvatuilla palvelinvarmenteilla käyttäen kaksisuuntaista kättelyä. Yhteys on muodostettava käyttäen ephemeral Diffie-Hellman (DHE) avaintenvaihtoa, jossa jokaiselle sessiolle luodaan uusi uniikki yksityinen salausavain. Tämän menettelyn tarkoituksena on taata salaukselle forward secrecy -ominaisuus, jotta salausavaimen paljastuminen ei jälkikäteen johtaisi salattujen tietojen paljastumiseen.

TLS-salauksessa käytettyjen kryptografisten algoritmien on vastattava kryptografiselta vahvuudeltaan vähintään Viestintäviraston määrittelemiä kryptografisia vahvuusvaatimuksia kansalliselle suojaustasolle ST IV. Tämänhetkiset vahvuusvaatimukset on kuvattu dokumentissa https://www.kyberturvallisuuskeskus.fi/sites/default/files/media/regulation/ohje-kryptografiset-vahvuusvaatimukset-kansalliset-suojaustasot.pdf (Dnro: 190/651/2015).

### 3.3 Sallittu HTTP-versio

Päivitysrajapinnan yhteydet käyttävät HTTP-protokollan versiota 1.1.

### 3.4 Sanomatason allekirjoitus

Päivitysrajapinnan sanomat allekirjoitetaan JWS-allekirjoituksella (PKI). JWS-allekirjoitukseen käytetään RS256 algoritmia ja ne allekirjoitetaan lähettäjän yksityisellä avaimella. Julkisen avaimen toimittamisesta Tullille ohjeistetaan Pankki- ja maksutilirekisterin käyttöönoton ja ylläpidon ohjeessa.

Päivityssanomassa on oltava kaksi erillistä JWS-allekirjoitusta (esimerkit alempana):  
a) Authorization headerissa on oltava Bearer token JWS josta löytyy lähettäjän Y-tunnus.  
b) Request bodyssa on oltava JWS jossa "reportUpdate" property sisältää [JSON skeeman](schemas/information_update.json) mukaisen päivityssanoman. 

Virheellisen tai virheelliseksi epäillyn sanoman ilmoitus eroaa päivityssanomasta niin, että "reportUpdate" claim jää kokonaan pois ja sen tilalla on joko "reportDisputable" tai "reportIncorrect" tilanteesta riippuen ([kts. Tilirekisterin päivitysrajapinnan yleiskuvaus](#päivitysrajapinta)).

a) Authorization header JWS:

JWT Header
```
{
  "alg": "RS256",
  "typ": "JWT"
}
```
JWT Payload
```
{
  "sub": "[SUBJECT]",
  "senderBusinessId": "[BUSINESS_ID]"
}
```

b) Request body JWS:

JWT Header
```
{
  "alg": "RS256",
  "typ": "JWT"
}
```
JWT Payload
```
{
  "sub": "[SUBJECT]",
  "reportUpdate": "[JSON STRING]"
}
```
### 3.5 Tietoturvapoikkeamien ilmoitusvelvollisuus

Rajapinnan käyttäjä on velvollinen ilmoittamaan viivytyksettä käyttämiensä varmenteiden tai näiden salaisten avainten vaarantumisesta sekä varmenteen myöntäjälle että Tullille.

Rajapinnan käyttäjä on myös velvollinen ilmoittamaan viivytyksettä Tullille mikäli rajapintaa käyttävässä tietojärjestelmässä havaitaan tietoturvapoikkeama.

### 3.6 Rajapinnan kapasiteetti

Rajapinnan sallittu maksimaalinen päivitystiheys ja sanomien koko lisätään tähän dokumenttiin myöhemmin.

## <a name="päivitysrajapinta"></a> 4. Tilirekisterin päivitysrajapinnan yleiskuvaus

Päivitysrajapinta toteutetaan REST/JSON-menetelmällä.

Rajapinnan käyttäjän (tiedon toimittajan) on lähetettävä vähintään yksi (1) minimisanoma (ks. alla *-merkityt kentät täytetty) määritellyssä ajassa, esim. kerran kuukaudessa (watchdog timer reset). Jos yhtään viestiä ei tänä aikana toimiteta, lähetetään vianselvittämispyyntö. Jos tähän ei määritellyssä ajassa reagoida, aloitetaan sanktiomenettely.

Jokaisessa sanomassa tulee olla mukana luontipäivämäärä.

Jokaisen sanoman tulee sisältää tietojen toimittajan Y-tunnus senderBusinessId kentässä.

Päivityssanoman sanomarakenteessa oikeushenkilöt, asiakkuudet, tilit ja tallelokerot ilmoitetaan avain-arvo-pareina joissa avaimena käytetään tietueelle yksilöllistä UUIDv4 (Universally unique identifier) tunnistetta. Tulli ei myönnä näitä tunnisteita, vaan ne ovat tietojen toimittajan luomia tunnisteita, joilla asiakastiedot voidaan yksilöidä toisistaan. Tämän tunnisteen perusteella tietueet pystytään tunnistamaan esimerkiksi henkilön nimen tai hetun vaihtuessa. Esimerkki päivityssanoman sanomarakenteesta löytyy [täältä](#sanomarakenne).

Sanomien yksilöintiin käytetään X-Correlation-ID tunnistetta (UUIDv4) joka kulkee sanoman headerissa. Jos sitä ei ole lähetetyssä sanomassa se generoidaan automaattisesti ja palautetaan vastaussanomassa.

Toimitettuja tietoja voidaan ilmoittaa joko virheellisiksi tai virheelliseksi epäillyiksi erillisillä sanomilla ja endpointeilla.
Tähän käytetään aiemmin mainittua X-Correlation-ID tunnistetta. Esimerkit sanomista löytyvät [täältä](#sanomarakenne).

Seuraavassa taulukossa on listattu rajapinnan endpointit.

|HTTP-metodi|Polku|Tarkoitus ja toiminnallisuus|
|---|---|---|
POST|/report-update|Tietojen toimitusvelvolliset (Maksulaitokset, sähkörahayhteisöt, virtuaalivaluutan tarjoajat tai Finanssivalvonnalta saadulla poikkeusluvalla luottolaitokset) käyttävät tätä endpointia asiakkuuksien, tilitietojen sekä tallelokeroiden tietojen toimittamiseen Tilirekisteriin.|
POST|/report-disputable|Käytetään ilmoittamaan tietyn aiemmin toimitetun sanoman tietojen oikeellisuus mahdollisesti virheellisiksi/kiistanalaisiksi. Tällä endpointilla voidaan myös poistaa kiistanalaisuus mikäli tieto havaitaan oikeaksi. Kiistanalaiseksi ilmoitettu tieto ilmoitetaan todetun virheelliseksi käyttäen POST /report-incorrect.|
POST|/report-incorrect|Käytetään ilmoittamaan tietyn aiemmin toimitetun sanoman tiedot virheellisiksi. Kun virheellisyys ilmoitetaan kiistanalaiseksi merkittyyn tietoon, tulkitaan kiistanalaisuus ratkaistuksi, ja tieto virheelliseksi todetuksi.|

Endpointia käytetään tietojen toimittamiseen Tilirekisteriin. Sanomassa toimitetaan tiedot asiakkuuksista, tileista ja tallelokeroista.

### Notaatio

Rajapintatietueiden rakenteiseen kuvaukseen käytetään seuraavanlaista notaatiota:
```
Objekti {
  tietue                    tietotyyppi
}
```

### <a name="sanomarakenne"></a>Sanomarakenteen kuvaus

Esimerkkisanomat löytyvät alla olevista linkeistä:

[Päivityssanoma](examples/example.json)

[Tiedon ilmoittaminen kiistanalaiseksi](examples/report-disputable.json)

[Tiedon ilmoittaminen virheelliseksi](examples/report-incorrect.json)

Päivityssanoman JSON rakenteen validointia varten on tehty [JSON Schema draft 7 mukainen skeema](schemas/information_update.json).

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
