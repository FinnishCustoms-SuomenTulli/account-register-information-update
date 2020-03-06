[Käyttöönoton ja ylläpidon ohje](instructions/Käyttöönoton_ja_ylläpidon_ohje_Tilirekisteri.pdf)  
[Deployment and maintenance instructions for the Bank and Payment Account Register](instructions/Deployment_and_maintenance_instructions_for_the_Bank_and_Payment_Account_Register_EN.pdf)  
[Data updating interface description](instructions/Data_updating_interface_description_EN.pdf)  
[Instruktioner för produktionssättning och underhåll av bank- och betalkontoregistret](instructions/Instruktioner_för_produktionssättning_och_underhåll_av_bank_och_betalkontoregistret_SV.pdf)  
[Beskrivning av Kontoregistrets uppdateringsgränssnitt](instructions/Beskrivning_av_Kontoregistrets_uppdateringsgränssnitt_SV.pdf)

# Tilirekisterin päivitysrajapintakuvaus

*Dokumentin versio 1.0.4*

## Versiohistoria

Versio|Päivämäärä|Kuvaus|
---|---|---
1.0|21.10.2019|Versio 1.0|
1.0.1|29.1.2020|JSON skeeman privatePerson objektin firstName ja lastName propertyt yhdistetty fullName propertyksi|
1.0.2|3.2.2020|luonnollisen henkilön kansalaisuus muutettu kansalaisuudet-listaksi|
1.0.3|3.2.2020|Organisaation ominaisuuksista muutettu businessId -> registrationNumber ja poistettu businessIdCountryCode|
1.0.4|5.3.2020|Päivitty sanomatason allekirjoituksen vaatimuksia. Lisätty PKI selite. Päivitetty rajapinnan maksimaalista sanomakokoa ja päivitetty kuvausta tietojen toimittamisesta Tilirekisteriin. Tarkennettu kiistanalaisten/virheellisten tietojen ilmoittamista.|

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
PKI|Julkisen avaimen tekniikka. Julkisen avaimen tekniikkaan perustuva sähköinen allekirjoitus luodaan siten, että allekirjoitettavasta tiedosta muodostetaan (tiivistealgoritmilla) tiiviste, joka salataan avainparin yksityisellä avaimella. Salattu tiiviste tallennetaan allekirjoitetun tiedon tai sähköisen asiakirjan yhteyteen tai välitetään muulla tavoin tiedon vastaanottajalle. Vastaanottaja purkaa tiivisteen salauksen avainparin julkisella avaimella, muodostaa uudelleen viestin tai asiakirjan tiedoista tiivisteen ja vertaa sitä allekirjoitukseen liitettyyn tiivisteeseen. Viestin sisältö on muuttumaton, mikäli tiivisteet ovat samat. (Sähköisen asioinnin tietoturvallisuus -ohje)

### 1.2 Dokumentin tarkoitus ja kattavuus

Tämä dokumentti on pankki- ja maksutilirekisterin päivitysrajapinnan rajapintakuvaus.

### 1.3 Yleiskuvaus

Tämä dokumentti on osa Tullin määräystä pankki- ja maksutilien valvontajärjestelmästä. Dokumentin tarkoitus on antaa ohjeet tiedon luovuttajille pankki- ja maksutilirekisterin (myöhemmin Tilirekisteri) päivitysrajapinnan toteuttamiseen. Tätä dokumenttia täydentää Pankki- ja maksutilirekisterin käyttöönoton ja ylläpidon ohje.

Järjestelmä koostuu kahdesta osasta: pankki- ja maksutilirekisteristä sekä tiedonhakujärjestelmästä. 

Tässä dokumentissa kuvataan Tilirekisterin päivitysrajapinta.

## 2. Aktiviteettien kuvaus <a name="luku2"></a>

Tässä luvussa on esitetty pankki- ja maksutilitietojen toimittaminen vuokaavioina.

### 2.1 Pankki- ja maksutilitietojen toimittaminen Tilirekisteriin

Tilirekisteriin toimitetaan ensimmäisellä päivityskerralla kaikki tiedot. Tämän jälkeen seuraavilla päivityskerroilla toimitetaan vain päivittyneet tai uudet tiedot päivittäin.

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

#### Tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon palvelinvarmenne

Tietoliikenne on suojattava (salaus ja vastapuolen tunnistus) x.509 (versio 3) varmenteita käyttäen.

Yhteyden muodostukseen on käytettävä palvelinvarmennetta, josta käy ilmi ko. tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus. Tiedon luovuttajan valtuuttamalla taholla tarkoitetaan esim. palvelukeskusta, jonka tiedon luovuttaja on valtuuttanut puolestaan huolehtimaan ilmoitusten muodostamisesta ja/tai lähettämisestä. Tätä koskeva valtuutus on toimitettava Tullille kirjallisesti.

Allekirjoituksen hyväksyminen edellyttää, että

joko  
a) palvelinvarmenteen on myöntänyt VRK, varmenne on voimassa, eikä esiinny VRK:n sulkulistalla, varmenteen kohteen serialNumber attribuuttina on kyseisen tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus

tai  
b) palvelinvarmenne on eIDAS-hyväksytty sivustojen tunnistamisvarmenne, voimassa, eikä esiinny varmenteen tarjoajan ylläpitämällä ajantasaisella sulkulistalla ja varmenteen kohteen organizationIdentifier-attribuuttina on kyseisen tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus.

Mikäli tiedon luovuttajan palvelinvarmenteessa ja lähtevän sanoman allekirjoitusvarmenteessa käytetään samaa Y-tunnusta tai ALV-tunnusta, voidaan kumpaankin tarkoitukseen käyttää samaa varmennetta.

#### Tilirekisterin palvelinvarmenne

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

Allekirjoituksessa käytettyjen kryptografisten algoritmien on vastattava kryptografiselta vahvuudeltaan vähintään Viestintäviraston määrittelemiä kryptografisia vahvuusvaatimuksia kansalliselle suojaustasolle ST IV. Tämänhetkiset vahvuusvaatimukset on kuvattu dokumentissa https://www.kyberturvallisuuskeskus.fi/sites/default/files/media/regulation/ohje-kryptografiset-vahvuusvaatimukset-kansalliset-suojaustasot.pdf (Dnro: 190/651/2015).

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

Rajapinnan sallittu maksimaalinen sanomakoko on 5MB. Rajapinnan sallittu maksimaalinen päivitystiheys lisätään tähän dokumenttiin myöhemmin.

## <a name="päivitysrajapinta"></a> 4. Tilirekisterin päivitysrajapinnan yleiskuvaus

Päivitysrajapinta toteutetaan REST/JSON-menetelmällä.

Rajapinnan käyttäjän (tiedon toimittajan) on lähetettävä vähintään yksi (1) minimisanoma (ks. alla *-merkityt kentät täytetty) määritellyssä ajassa, esim. kerran kuukaudessa (watchdog timer reset). Jos yhtään viestiä ei tänä aikana toimiteta, lähetetään vianselvittämispyyntö. Jos tähän ei määritellyssä ajassa reagoida, aloitetaan sanktiomenettely.

Jokaisessa sanomassa tulee olla mukana luontipäivämäärä.

Jokaisen sanoman tulee sisältää tietojen toimittajan Y-tunnus senderBusinessId kentässä.

Päivityssanoman sanomarakenteessa oikeushenkilöt, asiakkuudet, tilit ja tallelokerot ilmoitetaan avain-arvo-pareina joissa avaimena käytetään tietueelle yksilöllistä UUIDv4 (Universally unique identifier) tunnistetta. Tulli ei myönnä näitä tunnisteita, vaan ne ovat tietojen toimittajan luomia tunnisteita, joilla asiakastiedot voidaan yksilöidä toisistaan. Tämän tunnisteen perusteella tietueet pystytään tunnistamaan esimerkiksi henkilön nimen tai hetun vaihtuessa. Esimerkki päivityssanoman sanomarakenteesta löytyy [täältä](#sanomarakenne).

Sanomien yksilöintiin käytetään X-Correlation-ID tunnistetta (UUIDv4) joka kulkee sanoman headerissa. Jos sitä ei ole lähetetyssä sanomassa se generoidaan automaattisesti ja palautetaan vastaussanomassa.

Toimitettuja tietoja voidaan ilmoittaa joko virheellisiksi tai virheelliseksi epäillyiksi erillisillä sanomilla ja endpointeilla.
Tähän käytetään aiemmin mainittua tietueelle yksilöllistä UUIDv4 tunnistetta. Esimerkit sanomista löytyvät [täältä](#sanomarakenne).

Seuraavassa taulukossa on listattu rajapinnan endpointit.

|HTTP-metodi|Polku|Tarkoitus ja toiminnallisuus|
|---|---|---|
POST|/report-update|Tietojen toimitusvelvolliset (Maksulaitokset, sähkörahayhteisöt, virtuaalivaluutan tarjoajat tai Finanssivalvonnalta saadulla poikkeusluvalla luottolaitokset) käyttävät tätä endpointia asiakkuuksien, tilitietojen sekä tallelokeroiden tietojen toimittamiseen Tilirekisteriin.|
POST|/report-disputable|Käytetään ilmoittamaan tietyn aiemmin toimitetun tiedon oikeellisuus mahdollisesti virheellisiksi/kiistanalaisiksi. Tällä endpointilla voidaan myös poistaa kiistanalaisuus mikäli tieto havaitaan oikeaksi. Kiistanalaiseksi ilmoitettu tieto ilmoitetaan todetun virheelliseksi käyttäen POST /report-incorrect.|
POST|/report-incorrect|Käytetään ilmoittamaan tietyn aiemmin toimitetun tiedon virheelliseksi. Kun virheellisyys ilmoitetaan kiistanalaiseksi merkittyyn tietoon, tulkitaan kiistanalaisuus ratkaistuksi, ja tieto virheelliseksi todetuksi.|

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

[Päivityssanoma](examples/report-update.json)

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
