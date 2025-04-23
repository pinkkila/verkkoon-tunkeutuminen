## h4 NFC ja RFID

Tehtävät ovat Tero Karvisen ja Lari Iso-Anttilan opintojaksolta [Verkkoon tunkeutuminen ja tiedustelu](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/) [^1], [^8].

---

### 1. Tarkastele käytössäsi olevia RFID tuotteita, mieti miten hyvin olet suojautunut RFID urkinnalta?

RFID (radio frequency identification [^2]) tuotteet, jotka minulla ainakin ovat käytössä ovat puhelin, joka ymmärtääkseni pääosin käyttää NFC:tä joka on subset RFID:stä [^3], passi ja pankkikortti (sekin on NFC [^4]).

Uskoakseni olen ainakin kohtalaisesti suojautunut RFID urkinnalta johtuen toki pääasiassa siitä, että tällä hetkellä minulla on niin vähän tuotteita, jotka käyttävät sitä. Pankkikortin suhteen, ehkä voisi olla mahdollista joutua hyökkäyksen kohteeksi, mutta hyökkääjällä tulisi ilmeisesti olla rekisteröity korttimaksupääte käytössään [^5].  

Aiemmassa työpaikassani oli käytössä kulkulätkä ja nyt näin jäkikäteen käy enemmän järkeen, miksi pelkällä kulkulätkällä ei päässy sisälle kuin periaatteessa tyhjään käytävään. Eteenpäin päästääkseen piti käyttää kulkulätkää ja näppäillä numerosarja. Lisäksi oli niin, että numerosarja ei käynyt kuin aikana jolloin työpaikka oli auki. Muuna aikana myös varashälytin oli päällä. Voisi olettaa, että kulkulätkä oli suht helposti kopioitavissa ja todellinen valvonta hoidettiin tosiaan muilla keinoin.

---

### 2. Tutustu APDU komentojen rakenteeseen (voit käyttää tekoälyä tutustumiseen)

Package javacard.framework sisältää java luokan APDU. APDU eli Application Protocol Data Unit on viestintäformaatti kortin ja "off-card" sovellusten välillä. APDU tarjoaa metodit kortin inputin ja outputin hallintaan [^6].

APDU luokan metodeita:

- getBuffer() - palauttaa APDU puskurin tavu-taulukon (byte[])  
- setIncomingAndReceive() - ensisijainen vastaanotto metodi
- receiveBytes(short b0ff) - saa niin monta tavua dataa kuin mahtuu ilman APDU puskurin ylivuotoa.  
- setOutgoing() - käytetään asettamaan data siirron suunta lähteväksi ja saamaan odotettu responsen pituus
- setOutgoingLength() - asettaa response datan varsinaisen pituuden

---

### 3. Tutki ja kerro minkä mielenkiintoisen RFID hakkerointi uutiset löysit. (Vinkki, useimmat liittyvät henkilökortteihin)

https://www.wired.com/story/saflok-hotel-lock-unsaflok-hack-technique/ [^7]

Tietoturvatutkijat löysivät haavoittuvuuden RFID-avainkorteilla toimivista lukoista. Hakkerointi mahdollisti lukkojen avaamisen kahdella teetetyllä kortilla muutamassa sekunnissa. Kyseisiä lukkoja oli/on käytössä 3 miljoonassa ovessa 131 eri maassa. 

Hyökkäykseen tarvittiin vain validi tai vanhentunut korttiavain ja 300 dollarin RFID laite. Luetun kortin datalla teetetään kaksi korttia (voi käyttää myös Android puhelinta ja Flipperiä), joista toinen uudelleen ohjelmoi lukon ja toinen avaa sen. 

Haavoittuvuuden aiheuttaa valmistajan (Dormakaba) heikosti toteuttama kryptaus ja tuotteissa käytetty MIFARE Classic RFID järjestelmä.

---

## Lähteet

[^1]: Tero Karvinen. Verkkoon tunkeutuminen ja tiedustelu: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

[^2]: Wikipedia. RFID: https://fi.wikipedia.org/wiki/RFID

[^3]: George Lawton. RFID vs. NFC: Learn the pros and cons of each: https://www.techtarget.com/searcherp/tip/RFID-vs-NFC-Learn-the-pros-and-cons-of-each

[^4]: Osuuspankki. Lähimaksu – nopea ja turvallinen lähimaksaminen: https://www.op.fi/henkiloasiakkaat/paivittaiset/maksaminen/lahimaksaminen-nopeaa-ja-turvallista

[^5]: Wikipedia. RFID-skimmaus: https://fi.wikipedia.org/wiki/RFID-skimmaus

[^6]: Oracle. Package javacard.framework: https://docs.oracle.com/en/java/javacard/3.1/jc_api_srvc/api_classic/javacard/framework/package-summary.html 

[^7]: Andy Greenberg. Hackers Found a Way to Open Any of 3 Million Hotel Keycard Locks in Seconds: https://www.wired.com/story/saflok-hotel-lock-unsaflok-hack-technique/

[^8]: Lari Iso Anttila. 4. NFC ja RFID moodle: https://hhmoodle.haaga-helia.fi/course/view.php?id=42566&section=1#tabs-tree-start