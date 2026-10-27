# Palvelinten hallinta - harjoitus 0
x) Lue ja tiivistä
- *Tiivistelmä*
-AONCODES-materiaali käsitteli palvelimen turvallisuutta, käyttäjien hallintaa ja salasanakirjautumisen korvaamista turvallisilla SSH-avaimilla.
-Oma kysymys/huomio: SSH-avainten toimivuus on testattava aina ennen salasanakirjautumisen sulkemista, jotta ei lukitse itseään ulos

---

- *Tiivistelmä*
- Aleksi Partasen materiaali käsitteli domain-nimen hankkimista, A-tietueiden ja alidomainien määrittämistä sekä DNS-tietojen tutkimista dnsutils-työkaluilla
- Oma kysymys/huomio: Nimipalvelimien TTL-asetukset vaikuttavat suoraan siihen, kuinka nopeasti muutokset näkyvät verkossa.

---

a) Olen jo asentanut Debianin viime kurssilla

---

b) VPS
- Vuokrasin virtuaalisen palvelimen Helsingin konesalista, käytin palvelua UpCloud Oy. Tarkistin paikallisen koneeni julkisen SSH-avaimen seuraavalla komennolla:
````bash
cat ~/.ssh/id_ed25519.pub
````
