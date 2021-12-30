[Käyttöönoton ja ylläpidon ohje](instructions/Käyttöönoton_ja_ylläpidon_ohje_Tilirekisteri.pdf)  
[Deployment and maintenance instructions for the Bank and Payment Account Register](instructions/Deployment_and_maintenance_instructions_for_the_Bank_and_Payment_Account_Register_EN.pdf)  
[Data updating interface description](index_en.md)  
[Instruktioner för produktionssättning och underhåll av bank- och betalkontoregistret](instructions/Instruktioner_för_produktionssättning_och_underhåll_av_bank_och_betalkontoregistret_SV.pdf)  
[Beskrivning av Kontoregistrets uppdateringsgränssnitt](index_sv.md)

# Tilirekisterin päivitysrajapintakuvaus

*Dokumentin versio 2.0.00*

## Versiohistoria

Versio|Päivämäärä|Kuvaus|
---|---|---
1.0|21.10.2019|Versio 1.0|
1.0.1|29.1.2020|JSON skeeman privatePerson objektin firstName ja lastName propertyt yhdistetty fullName propertyksi|
1.0.2|3.2.2020|luonnollisen henkilön kansalaisuus muutettu kansalaisuudet-listaksi|
1.0.3|3.2.2020|Organisaation ominaisuuksista muutettu businessId -> registrationNumber ja poistettu businessIdCountryCode|
1.0.4|5.3.2020|Päivitty sanomatason allekirjoituksen vaatimuksia. Lisätty PKI selite. Päivitetty rajapinnan maksimaalista sanomakokoa ja päivitetty kuvausta tietojen toimittamisesta Tilirekisteriin. Tarkennettu kiistanalaisten/virheellisten tietojen ilmoittamista.|
1.0.5|12.5.2020|Lisätty request/response esimerkki selventämään JWT tokenien ja HTTP headerien käyttöä.|
1.0.6|13.5.2020|Poistettu kappaleesta 3.1 kohta Saapuvan sanoman allekirjoitusvarmenne.|
1.0.7|13.5.2020|Poistettu skeemasta roolin alkupäivän pakollisuus edunsaajan osalta.|
1.0.8|5.6.2020|Lisätty skeemaan tilin ja talletuslokeron roolien vähimmäismääräksi 1.|
1.0.9|11.6.2020|Päivitetty JWS-allekirjoituksen kuvaus kappaleessa 3.4.|
1.0.10|20.8.2020|Päivitetty sanoman maksimikoko ja maininta peräkkäisestä lähetyksestä kappaleessa 3.6.|
1.0.11|24.8.2020|Lisätty tarkentava huomautus liittyen tietoliikenteessä ja sanomien allekirjoituksissa käytettävien avainten pituuksista.|
1.0.12|1.9.2020|Lisätty kappaleeseen 4 tarkennus että roolilistoissa on toimitettava kaikki ajan hetkellä voimassa olevat roolit. Päivitetty sisällysluettelo.|
1.0.13|2.9.2020|Lisätty kappaleeseen 3.4 maininta, että allekirjoitusten sub-kentän on vastattava varmenteen serialnumber-kentän sisältöä.|
1.0.14|1.10.2020|Tarkennettu julkisen avaimen sisältävän varmenteen toimittamisesta Tullille kohdassa 3.4.|
1.0.15|18.3.2021|Poistettu kappaleesta 4 vaatimus, jonka mukaan rajapinnan käyttäjän pitää lähettää vähintään yksi minimisanoma määritellyn ajanjakson kuluessa. Korvattu VRK -> DVV.|
2.0.00|pp.kk.2021|Lisätty uudet, tiedonluovuttajien kategorian mukaiset päivitysrajapinnat, JSON-skeemat ja esimerkkisanomat. Lisätty CorrelationId virheelliseksi ja kiistanalaiseksi ilmoittamissanomiin, jolloin tiedon tietty versio voidaan ilmoittaa virheelliseksi tai kiistanalaiseksi. Lisätty JSON-skeemat virheelliseksi ja kiistanalaiseksi ilmoittamisanomille. Tarkennettu HTTP-vastaukset -listaa.



## Sisällysluettelo

1. [Johdanto](#luku1)  
  1.1 Termit ja lyhenteet  
  1.2 Dokumentin tarkoitus ja kattavuus  
  1.3 Yleiskuvaus  
2. [Aktiviteettien kuvaus](#luku2)  
  2.1 Pankki- ja maksutilitietojen toimittaminen Tilirekisteriin 
3. [Tietoturva](#tietoturva)  
  3.1 Tunnistaminen  
  3.2 Yhteyksien suojaaminen  
  3.3 Sallittu HTTP-versio  
  3.4 Sanomatason allekirjoitus  
  3.5 Tietoturvapoikkeamien ilmoitusvelvollisuus  
  3.6 Rajapinnan kapasiteetti  
4. [Tilirekisterin päivitysrajapinnan yleiskuvaus](#päivitysrajapinta)  
  4.1 Yleistä  
  4.2 Tiedonluovuttajien kategoriat  
  4.3 Tietojen ilmoittaminen virheelliseksi tai kiistanalaiseksi  
  4.4 Rajapinnat  
  4.5 JSON-skeemat  
  4.6 Esimerkkisanomat  
  4.7 HTTP-vastaukset  
  

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
a) varmenne on DVV:n myöntämä, voimassa, eikä esiinny DVV:n ylläpitämällä sulkulistalla ja varmenteen kohteen serialNumber attribuuttina on kyseisen tiedon luovuttajan Y-tunnus tai ALV-tunnus

tai  
b) varmenne on eIDAS-hyväksytty sivustojen tunnistamisvarmenne, voimassa, eikä esiinny varmenteen tarjoajan ylläpitämällä ajantasaisella sulkulistalla ja varmenteen kohteen organizationIdentifier-attribuuttina on kyseisen tiedon luovuttajan Y-tunnus tai ALV-tunnus.

Huom. Jotta sanomien allekirjoitukset täyttävät alla viitatut kyberturvallisuuskeskuksen tietoturvavaatimukset, tulee allekirjoituksiin käytettävän varmenteen julkisen avaimen (RSA public key) olla vähintään 3072 bittinen. Allekirjoituksiin käytettävän varmenteen käyttötarkoituksiin täytyy myös kuulua ”digitaalinen allekirjoitus”. Nämä seikat tulee huomioida varmennetta tilattaessa.

#### Tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon palvelinvarmenne

Tietoliikenne on suojattava (salaus ja vastapuolen tunnistus) x.509 (versio 3) varmenteita käyttäen.

Yhteyden muodostukseen on käytettävä palvelinvarmennetta, josta käy ilmi ko. tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus. Tiedon luovuttajan valtuuttamalla taholla tarkoitetaan esim. palvelukeskusta, jonka tiedon luovuttaja on valtuuttanut puolestaan huolehtimaan ilmoitusten muodostamisesta ja/tai lähettämisestä. Tätä koskeva valtuutus on toimitettava Tullille kirjallisesti.

Allekirjoituksen hyväksyminen edellyttää, että

joko  
a) palvelinvarmenteen on myöntänyt DVV, varmenne on voimassa, eikä esiinny DVV:n sulkulistalla, varmenteen kohteen serialNumber attribuuttina on kyseisen tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus

tai  
b) palvelinvarmenne on eIDAS-hyväksytty sivustojen tunnistamisvarmenne, voimassa, eikä esiinny varmenteen tarjoajan ylläpitämällä ajantasaisella sulkulistalla ja varmenteen kohteen organizationIdentifier-attribuuttina on kyseisen tiedon luovuttajan tai tiedon luovuttajan valtuuttaman tahon Y-tunnus tai ALV-tunnus.

Mikäli tiedon luovuttajan palvelinvarmenteessa ja lähtevän sanoman allekirjoitusvarmenteessa käytetään samaa Y-tunnusta tai ALV-tunnusta, voidaan kumpaankin tarkoitukseen käyttää samaa varmennetta.

Huom. Jotta tietoliikenteen suojaus täyttää alla viitatut Kyberturvallisuuskeskuksen tietoturvavaatimukset, tulee käytettävän varmenteen julkisen avaimen (RSA public key) olla vähintään 3072 bittinen. Tämä tulee huomioida varmennetta tilattaessa.

#### Tilirekisterin palvelinvarmenne

Tiedon luovuttaja tunnistaa yhteyden vastapuolen Tilirekisteriksi palvelinvarmenteen perusteella seuraavin edellytyksin:  
a) Tilirekisterin ylläpitäjän (Tullin) palvelinvarmenteen on myöntänyt DVV, varmenne on voimassa eikä esiinny DVV:n ylläpitämällä sulkulistalla  
b) varmenteen kohteen serialNumber attributti on “FI02454428” tai “0245442-8”.

### 3.2 Yhteyksien suojaaminen

Tilirekisterin päivitysrajapinnan yhteydet on suojattava TLS-salauksella käyttäen TLS-protokollan versiota 1.2 tai korkeampaa. Yhteyden molemmat päät tunnistetaan yllä kuvatuilla palvelinvarmenteilla käyttäen kaksisuuntaista kättelyä. Yhteys on muodostettava käyttäen ephemeral Diffie-Hellman (DHE) avaintenvaihtoa, jossa jokaiselle sessiolle luodaan uusi uniikki yksityinen salausavain. Tämän menettelyn tarkoituksena on taata salaukselle forward secrecy -ominaisuus, jotta salausavaimen paljastuminen ei jälkikäteen johtaisi salattujen tietojen paljastumiseen.

TLS-salauksessa käytettyjen kryptografisten algoritmien on vastattava kryptografiselta vahvuudeltaan vähintään Viestintäviraston määrittelemiä kryptografisia vahvuusvaatimuksia kansalliselle suojaustasolle ST IV. Tämänhetkiset vahvuusvaatimukset on kuvattu dokumentissa https://www.kyberturvallisuuskeskus.fi/sites/default/files/media/regulation/ohje-kryptografiset-vahvuusvaatimukset-kansalliset-suojaustasot.pdf (Dnro: 190/651/2015).

### 3.3 Sallittu HTTP-versio

Päivitysrajapinnan yhteydet käyttävät HTTP-protokollan versiota 1.1.

### 3.4 Sanomatason allekirjoitus

Päivitysrajapinnan sanomat allekirjoitetaan JWS-allekirjoituksella (PKI). JWS-allekirjoitukseen käytetään RS256 algoritmia ja ne allekirjoitetaan lähettäjän yksityisellä avaimella. Julkisen avaimen sisältävän varmenteen toimittamisesta Tullille ohjeistetaan Pankki- ja maksutilirekisterin käyttöönoton ja ylläpidon ohjeessa.

Allekirjoituksessa käytettyjen kryptografisten algoritmien on vastattava kryptografiselta vahvuudeltaan vähintään Viestintäviraston määrittelemiä kryptografisia vahvuusvaatimuksia kansalliselle suojaustasolle ST IV. Tämänhetkiset vahvuusvaatimukset on kuvattu dokumentissa https://www.kyberturvallisuuskeskus.fi/sites/default/files/media/regulation/ohje-kryptografiset-vahvuusvaatimukset-kansalliset-suojaustasot.pdf (Dnro: 190/651/2015).

Päivityssanomassa on oltava kaksi erillistä JWS-allekirjoitusta (esimerkit alempana):  
a) Authorization headerissa on oltava Bearer token JWS josta löytyy sub-väitteessä (sub claim) lähettäjän Y-tunnus tai ALV-tunnus.  
b) Request bodyssa on oltava JWS jossa "reportUpdate" property sisältää [JSON skeeman](schemas/information_update.json) mukaisen päivityssanoman. 

Molempien JWS-allekirjoitusten sub-kentässä tulee olla lähettäjän Y-tunnus tai VAT-tunnus samassa muodossa kuin lähettäjän julkisessa varmenteessa SERIALNUMBER-kentässä.

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
  "aud": "accountRegister"
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
  "aud": "accountRegister",
  "reportUpdate": "[JSON OBJECT]"
}
```
### 3.5 Tietoturvapoikkeamien ilmoitusvelvollisuus

Rajapinnan käyttäjä on velvollinen ilmoittamaan viivytyksettä käyttämiensä varmenteiden tai näiden salaisten avainten vaarantumisesta sekä varmenteen myöntäjälle että Tullille.

Rajapinnan käyttäjä on myös velvollinen ilmoittamaan viivytyksettä Tullille mikäli rajapintaa käyttävässä tietojärjestelmässä havaitaan tietoturvapoikkeama.

### 3.6 Rajapinnan kapasiteetti

Rajapinnan sallittu maksimaalinen sanomakoko on 50kB JWT-muodossa. Sanomat tulee lähettää peräkkäin niin, että odotetaan edellisen pyynnön OK-kuittausta ennen seuraavan lähetystä.

## <a name="päivitysrajapinta"></a> 4. Tilirekisterin päivitysrajapinnan yleiskuvaus

### <a name="Yleista"></a> 4.1 Yleistä

Päivitysrajapinta toteutetaan REST/JSON-menetelmällä.

Jokaisessa sanomassa tulee olla mukana luontipäivämäärä.

Jokaisen sanoman tulee sisältää tietojen toimittajan Y-tunnus senderBusinessId kentässä.

Päivityssanoman sanomarakenteessa oikeushenkilöt, asiakkuudet, tilit ja tallelokerot ilmoitetaan avain-arvo-pareina joissa avaimena käytetään tietueelle yksilöllistä UUIDv4 (Universally unique identifier) tunnistetta. Tulli ei myönnä näitä tunnisteita, vaan ne ovat tietojen toimittajan luomia tunnisteita, joilla asiakastiedot voidaan yksilöidä toisistaan. Tämän tunnisteen perusteella tietueet pystytään tunnistamaan esimerkiksi henkilön nimen tai hetun vaihtuessa. Esimerkki päivityssanoman sanomarakenteesta löytyy [täältä](#Esimerkkisanomat).

Päivityssanomissa on mahdollista toimittaa kokonaisia tietueita, jotka viittaavat aiemmin toimitettuihin tietueelle yksilöllisiin tunnisteisiin. Esimerkiksi voidaan toimittaa tiedot tilistä, joka sisältää rooliviittauksia aiemmin toimitettuihin LegalPerson-tietueisiin. Lisäksi voidaan esimerkiksi toimittaa LegalPerson-tietueesta pelkkä nimen muutos, jolloin LegalPerson-tietueeseen liittyviä roolitietoja ei tarvitse toimittaa sanomalla uudestaan.

Kuitenkin on huomioitava, että kun toimitetaan Account, SafetyDepositBox roolilistoja tai Organisation-tietueen roolilistoja, on roolilistojen oltava tällöin aina täydellisiä, ts. ei ole mahdollista toimittaa esimerkiksi Account.roles-kentässä vain uusia rooleja, vaan on toimitettava kaikki sillä ajan hetkellä voimassaolevat roolit.

Sanomien yksilöintiin käytetään X-Correlation-ID tunnistetta (UUIDv4) joka kulkee sanoman headerissa. Jos sitä ei ole lähetetyssä sanomassa se generoidaan automaattisesti ja palautetaan vastaussanomassa.

#### Notaatio

Rajapintatietueiden rakenteiseen kuvaukseen käytetään seuraavanlaista notaatiota:
```
Objekti {
  tietue                    tietotyyppi
}
```

### <a name="Kategoriat"></a> 4.2 Tiedonluovuttajien kategoriat

Tietojen toimitusvelvolliset on jaettu kahteen kategoriaan:

Kategoria 1: luottolaitokset  
Kategoria 2: maksulaitokset, sähkörahayhteisöt ja virtuaalivaluutan tarjoajat  

Päivityssanomien sisältö on kuvattu [JSON skeemoissa](#JSONskeemat).

### <a name="VirheellinenKiistanalainen"></a> 4.3 Tietojen ilmoittaminen virheelliseksi tai kiistanalaiseksi

Toimitettuja tietueita voidaan ilmoittaa joko virheellisiksi tai virheelliseksi epäillyiksi (kiistanalaiseksi). Tähän käytetään tietueen yksilöivää UUIDv4 tunnistetta ja sen päivityssanoman yksilöllistä X-Correlation-ID tunnistetta, jolla tietue on ilmoitettu. Tietue, johon tietueen tunnisteella viitataan, voi olla joko tili, tallelokero tai oikeudellinen henkilö. Esimerkit sanomista löytyvät [täältä](#Esimerkkisanomat).

Molempien kategorioiden tiedon luovuttajat käyttävät samoja rajapintoja tietojen virheelliseksi ja kiistanalaiseksi ilmoittamiseen.

Virheelliseksi epäily voidaan perua jos epäily todetaan aiheettomaksi mutta virheelliseksi ilmoitetun tietueen tilaa ei voi enää muuttaa.

![Tietueen tilan muuttaminen](diagrams/state_diagram_incorrect_disputed.png "Tietueen tilan muuttaminen")  
*__Kuva 4.3.__ Tietueen tilan muuttaminen*

### <a name="Rajapinnat"></a> 4.4 Rajapinnat

Seuraavassa taulukossa on listattu rajapinnan endpointit.

|HTTP-metodi|Polku|Tarkoitus ja toiminnallisuus|
|---|---|---|
POST|/v1/report-update/|Tietojen toimitusvelvolliset (Maksulaitokset, sähkörahayhteisöt, virtuaalivaluutan tarjoajat tai Finanssivalvonnalta saadulla poikkeusluvalla luottolaitokset) käyttävät tätä endpointia asiakkuuksien, tilitietojen sekä tallelokeroiden tietojen toimittamiseen Tilirekisteriin.|
POST|/v2/report-update/cat-1/|Luottolaitokset (Finanssivalvonnalta saadulla poikkeusluvalla) käyttävät tätä endpointia asiakkuuksien, tilitietojen sekä tallelokeroiden tietojen toimittamiseen Tilirekisteriin.|
POST|/v2/report-update/cat-2/|Maksulaitokset, sähkörahayhteisöt ja virtuaalivaluutan tarjoajat käyttävät tätä endpointia asiakkuuksien ja tilitietojen toimittamiseen Tilirekisteriin.|
POST|/v1/report-disputable/|Käytetään ilmoittamaan tietyn aiemmin toimitetun tiedon oikeellisuus mahdollisesti virheellisiksi/kiistanalaisiksi. Tällä endpointilla voidaan myös poistaa kiistanalaisuus mikäli tieto havaitaan oikeaksi. Kiistanalaiseksi ilmoitettu tieto ilmoitetaan todetun virheelliseksi käyttäen POST /v1/report-incorrect/.|
POST|/v1/report-incorrect/|Käytetään ilmoittamaan tietyn aiemmin toimitetun tiedon virheelliseksi. Kun virheellisyys ilmoitetaan kiistanalaiseksi merkittyyn tietoon, tulkitaan kiistanalaisuus ratkaistuksi, ja tieto virheelliseksi todetuksi.|

### <a name="JSONskeemat"></a> 4.5 JSON-skeemat

Sanomien validointia varten on tehty JSON Schema draft 7 mukaiset skeemat:

Päivityssanoma v1 (kaikki tiedon luovuttajat) [skeema](schemas/information_update-v1.json)

Päivityssanoma v2 (luottolaitokset) [skeema](schemas/information_update-v2-credit_institution.json)

Päivityssanoma v2 (maksulaitokset, sähkörahayhteisöt ja virtuaalivaluutan tarjoajat) [skeema](schemas/information_update-v2-other.json)

Tiedon ilmoittaminen kiistanalaiseksi v2 [skeema](schemas/report_disputable.json)

Tiedon ilmoittaminen virheelliseksi v2 [skeema](schemas/report_incorrect.json)

### <a name="Esimerkkisanomat"></a> 4.6 Esimerkkisanomat

Esimerkkisanomat löytyvät alla olevista linkeistä:

[Päivityssanoma v1 (kaikki tiedon luovuttajat)](examples/report-update-v1.json)

[Päivityssanoma v2 (luottolaitokset)](examples/report-update-v2-credit_institution.json)

[Päivityssanoma v2 (maksulaitokset, sähkörahayhteisöt ja virtuaalivaluutan tarjoajat)](examples/report-update-v2-other.json)

[Tiedon ilmoittaminen kiistanalaiseksi v2](examples/report-disputable.json)

[Tiedon ilmoittaminen virheelliseksi v2](examples/report-incorrect.json)

### <a name="InformationUpdate response"></a> 4.7 HTTP-vastaukset

Järjestelmä palauttaa seuraavia HTTP vastauksia:

200 OK

400 Bad Request

Body
```
{
  message              string
  objectErrors         string-taulukko
  fieldErrors          string-taulukko   
}
```

403 Forbidden

Body
```
{
  message              string
}
```

404 Not Found

Body
```
{
  message              string
}
```

405 Method Not Allowed

500 Internal Server Error

Body
```
{
  message              string
}
```


