## h1 Sniff

Tehtävät ovat Tero Karvisen ja Lari Iso-Anttilan opintojaksolta [Verkkoon tunkeutuminen ja tiedustelu](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/).

---

#### Laite jolla tehtävät tehdään:

- Apple MacBook Pro M2 Max
- macOS Sequoia 15.3.2
- Hypervisorina Parallels Desktop

---

### x) Lue ja tiivistä.

- Karvinen 2025: [Wireshark - Getting Started](https://terokarvinen.com/wireshark-getting-started/)

    - Jotta Wiresharkia voi käyttää ei root käyttäjä, pitää käyttjä lisätä wireshark groupiin. 
    - Kaappauksen voi talleentaa ja avata .pcap tiedostotyyppinä.
    - Kaappausta voi filtteröidä monella tapaa. 


- Karvinen 2025: [Network Interface Names on Linux](https://terokarvinen.com/network-interface-linux/)

  - Network interface on kuin verkkokortti, mutta se ei välttämättä ole fyysinen. 
  - en - Wired Ethernet
  - wl - WLAN wireless local area network, WiF
  - lo - Loopback adapter
  
```
ip a
```

```
ip route
```

---

### a)  Linux. Asenna Debian tai Kali Linux virtuaalikoneeseen.

Olen jo aiemmin asentanut.

---

### b) Ei voi kalastaa. Osoita, että pystyt katkaisemaan ja palauttamaan virtuaalikoneen Internet-yhteyden.

Parallelsin Network asetusten Host Only sulkee virtuallikoneen pääsyn Internettiin [^1], joten vaihdoin Kalin Network moodiksi Host Only. Ping testi Googleen varmistaa, että yhteyttä ei ole. 

```bash
┌──(parallels㉿kali-linux-2024-2)-[~]
└─$ ping 8.8.8.8
ping: connect: Network is unreachable
```

---

### c) Wireshark. Asenna Wireshark. Sieppaa liikennettä Wiresharkilla. (Vain omaa liikennettäsi. Voit käyttää tähän esimerkiksi virtuaalikonetta).

Asensin Wiresharkin (laitoin Shared Network moden päälle ja asennuksen jälkeen vaihdoin takaisin Host Only modeen).

```
sudo apt-get install wireshark
```

Vaikka Kalissa ei suoraan ollutkaan Wiresharkia, kuului käyttäjäni valmiiksi wireshark gourpiin. 

Seuraavaksi käynnistin toisen virtuaalikoneen (Debian) Parallelsissa ja laitoin myös sen Network moodin Host Only. Laitoin Debianissa Apache2 palvelimen päälle. Laitoin filtteriksi Wiresharkiin Debianin IP-osoitteen kuten Teron ojeessa [^2]. Seuraavaksi avasin Debian tarjoaman Apache2 sivun Kalin selaimessa.

filtteri: `ip.addr == ip-osoite`

Jostain syystä en pysty ottamaan näyttökuvia Kalista. Kaikista muista Parallelsin virtuaalikoneiden näytöistä olen pystynyt ottamaan kuvia. En löytänyt nopeaa ratkaisua ja täyty selvittää myöhemmin mistä johtuu.  

Mutta joka tapauksessa ensimmäinen paketti oli seuraava:  

1   0.000000000     10.37.129.4     10.37.129.5     TCP     74      38286 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM TSval=1746304660 TSecr=0 WS=128

Kyseessä on varmastikkin TCP-protokollan threeway handshakin ensimmäinen pyyntö, koska varmaankin pakko olla, koska se on ensimmäinen pyyntö, mutta koska pyyntö on merkitty [SYN], joka merkitsee varmastikin TCP headerin SYN flagia, joka merkitsee uuden yhteyden avaamista [^3]

### d) Oikeesti TCP/IP. Osoita TCP/IP-mallin neljä kerrosta yhdestä siepatusta paketista. Voit selityksen tueksi laatikoida ne ruutukaappauksesta.

- TCP/IP- mallin neljä kerrosta ovat [^4]:

    - Application Layer (mm. http, https, ssh)
    - Transport Layer (mm. tcp, upd)
    - Internet Layer (ip)
    - Link Layer (ARP, MAC)


- Kerrokset näkyvät Wiresharkin Packet Details Pane:sta [^3] ja ovat tuon edellisen tehtän paketissa:
 
    - (Ymmärtääkseni Application Layeriä ei ole vielä, koska handshaken ensimmäinen pyyntö. En ole varma.) 
    - Transmission Control Protocol, joka Transport Layer
    - Internet Protocol version 4, joka on Internet Layer
    - Ethernet II, joka on Link Layer


- Jos ymmärrän oikein, niin Application Layer tulee mukaan, kun tarkastelee HTTP protokollan pakettia, kuten tämä:

4   0.000406250     10.37.129.4     10.37.129.5     HTTP    440     GET / HTTP/1.1

Pacet Detail Pane näyttää tämän kohdalla kentän Hyper Text Transfer Protocol, joka kuuluu Application Layeriin.


### e) Mitäs tuli surffattua? Avaa surfing-secure.pcap.

- Olettaisin, että käyttäjä on ensin mennyt Googleen ja googlen kautta terokarvinen.comiin.  

- Koneet joita on mukana arvioni mukaan ovat
  
    - 192.168.122.7 on käyttäjän kone
    - 139.162.131.217 on terokarvinen.com: palvelin
    - 192.168.122.1 on domain name serverin osoite, Netlifly
    - 3.75.10.80 ??
    - 135.181.139.209 kuuluu goatcounter.com [^11]
    - 34.117.188.166 Google [^12]

Lopussa on TCP paketit RST lipuilla. RST tarkoittaa hard-close connection ja sitä käytetään yleensä ilmaisemaan, että paketit ovat saapuneet väärään porttiin tai IP-osoitteeseen [^3]. En tiedä onko tällä merkitystä tässä.

Ymmärtääkseni kaappauksessa on 283 pakettia ja se on kestänyt noin 7.5 sekuntia. 

---

### f) Mitä selainta käyttäjä käyttää?

Ymmärtääkseni selaimen tietojen ainakin luulisi olevan Applicatoin layerissä, koska kun tein tehtävän c, niin selaimen tiedot löytyivät juurikin Hypertext Transfer Protocollasta. Nyt paketit, jotka sisältävät HyperText Transfer Protocollan ovat TLS paketteja ja niiden tiedot ovat salattuja.

Jonkin aikaa Wiresharkia tutkittuani ja googlailtuani olettaisin tämän [^7] ja tämän [^8] perusteella olevan niin, että selain pitäis päätellä cipher suitesta. Eli ilmaisestikin tästä.

    Cipher Suite: TLS_AES_128_GCM_SHA256 (0x1301)                 
    Cipher Suite: TLS_CHACHA20_POLY1305_SHA256 (0x1303)
    Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)                       
    Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (0xc02b)      
    Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (0xc02f)        
    Cipher Suite: TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256 (0xcca9)
    Cipher Suite: TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (0xcca8)
    Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 (0xc02c)
    Cipher Suite: TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (0xc030)
    Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA (0xc00a)
    Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA (0xc009)
    Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (0xc013)
    Cipher Suite: TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (0xc014)             
    Cipher Suite: TLS_RSA_WITH_AES_128_GCM_SHA256 (0x009c)
    Cipher Suite: TLS_RSA_WITH_AES_256_GCM_SHA384 (0x009d)
    Cipher Suite: TLS_RSA_WITH_AES_128_CBC_SHA (0x002f)
    Cipher Suite: TLS_RSA_WITH_AES_256_CBC_SHA (0x0035)

Löysin myös tämän [^9] ja sen perusteella ilmeisesti myös nämä tiedot voisivat auttaa selvittämään käytetyn selaimen:

    [JA3 Fullstring: 771,4865-4867-4866-49195-49199-52393-52392-49196-49200-49162-49161-49171-49172-156-157-47-53,0-23-65281-10-11-35-16-5-34-51-43-13-45-28-65037,29-23-24-25-256-257,0]

    [JA3: b5001237acdf006056b409cc433726b0]

    [JA3: a195b9c006fcb23ab9a2343b0871e362]

Tämä ei taida olla ihan vedenpitävä selvitys, mutta googlaamalla löysin xnih nimisen käyttäjän tekemän ohjelman [^9] fingerprintien etsimiseen ja tuon ohjelman koodista löytyvän kohdan mukaan [^10] selaimena olis ollut Firefox.

Jänne nähdä menikö oikein. 

---

### g) Minkä merkkinen verkkokortti käyttäjällä on?

- Tämän MAC-osoitteen 52:54:00:cc:d7:12 perusteella ilmeisesti RealtekU [^5] Löytyi Framesta 238. (ARP)

- Tämän 52:54:00:2f:e1:e5 perusteella myös RealtekU [^6]. Löytyi Framesta 239

---

### h) Millä weppipalvelimella käyttäjä on surffaillut?

- Google
- terokarvinen.com
- Netlify
- GoatCounter

---

### i) Analyysi. Sieppaa pieni määrä omaa liikennettäsi. Analysoi se, eli selitä mahdollisimman perusteellisesti, mitä tapahtuu.

Kyllä. Tämä on puhelimella otettu kuva 😃

![img.png](img/h1-Sniff/img.png)

Tässä on jo tehtävässä c otetusta kaappauksesta, jossa Debian tarjoilee Apachen2:lla Apachen default sivua.

1. Source on  Kalista ja Destination Depianin. TCP handshake avaus. TCP headerissa SYN flag, joka tarkoittaa uuden yhteyden avaamista [^3].
2. Nyt toisinpäin ja Depian vastaa handshakeen. SYN-ACK ja ACK lippu tarkoittaa tietoa paketista. Kaikkien pakettien tulisi komivaiheisen handshaken jälkeen sisältää tämä lippu tai bitti setti [^3]
3. Client eli Kalin selain lähettää Depianille handshaken päättävän ACK lipulla varustetun paketin. 
4. ohitan
5. ohitan
6. http pyyntö statuskoodilla 200 ja sisältää html sivun sisällön. Hyper Text Transfer Protocol näyttä jännän paljon tietoa, kuten serveristä Apache/2.4.6 (Debian). Myös last modified kellonaika näkyy, joka taitaa olla juuri se aika jolloin asensin Depianille Apachen ja käynnistin sen. 


---

### Lähteet

Tero Karvinen. Verkkoon tunkeutuminen ja tiedustelu: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Tero Karvinen. WireShark -Getting Started: https://terokarvinen.com/wireshark-getting-started/

Tero Karvinen. Network Interface Names on Linux: https://terokarvinen.com/network-interface-linux/

[^1]: Parallels. Network modes in Parallels Desktop for Mac: https://kb.parallels.com/4948/

[^2]: Tero Karvinen. WireShark -Getting Started: https://terokarvinen.com/wireshark-getting-started/

[^3]: Master OccupytheWeb 2023. Network Basics for Hackers: How Networks Work and How They Break.https://www.amazon.com/Network-Basics-Hackers-Networks-Break/dp/B0BS3GZ1R9. 

[^4]: Wikipedia. Internet protocol suite: https://en.wikipedia.org/wiki/Internet_protocol_suite.

[^5]: mac.lc:  https://mac.lc/address/52:54:00:cc:d7:12

[^6]: mac.lc: https://mac.lc/address/52:54:00:2f:e1:e5

[^7]: Security Stack Exchange: User agent information from HTTPS flow: https://security.stackexchange.com/questions/37475/user-agent-information-from-https-flow

[^8]: Stack Overflon. Is there a list of which browser supports which TLS cipher suite?: https://stackoverflow.com/questions/31785430/is-there-a-list-of-which-browser-supports-which-tls-cipher-suite

[^9]: xhin: satori: https://github.com/xnih/satori/tree/master

[^10]: xnih: satori/fingerprints/ssl.xml: https://github.com/xnih/satori/blob/master/fingerprints/ssl.xml

[^11]: WhatIsMyIpAddress: https://whatismyipaddress.com/ip/135.181.139.209

[^12]: WhatIsMyIpAddress: https://whatismyipaddress.com/ip/34.117.188.166