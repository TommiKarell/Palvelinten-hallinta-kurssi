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
Ja sitten liitin sen palveluntarjoalle ja otin yhteyttä komennolla:
````bash
ssh root@85.9.222.228
````
---

c) Alkutoimet
- Loin palvelimelle oman käyttäjän ja annoin sille sudo-oikeudet seuraavilla komennoilla:
````bash
'adduser tommik
usermod -aG sudo tommik
````
- Kopioin SSH-oikeudetm asensin UFW-palomuurin, avasin portin ja otin muurin käyttöön ja tarkistin tilan ja päivitin järjestelmän komennoilla:
````bash
sudo apt-get update && sudo apt-get install -y ufw
sudo ufw allow 22/tcp
sudo ufw enable
sudo reboot
````

<img width="643" height="206" alt="image" src="https://github.com/user-attachments/assets/ccb3c14f-3623-4fee-bf0e-8110417b818b" />

<img width="1004" height="886" alt="image" src="https://github.com/user-attachments/assets/2418a438-54fd-434e-a4fe-7d95fcf8fb38" />

---

d) Webbipalvelin
- Asensin Apache2-palvelimen ja avasin sille portin 80 komennoilla:
````bash
sudo apt-get install -y apache2
sudo ufw allow 80/tcp
````
- Korjasin oletussivun omalla sisällölläni komennolla:
````bash
echo "Hei maailma! Tommin VPS-palvelin toimii." | sudo tee /var/www/html/index.html
````
<img width="643" height="214" alt="image" src="https://github.com/user-attachments/assets/dabfd262-86b9-4156-be83-420651dccace" />
---

Lähteet
- Karvinen, Tero. Kurssimateriaalit ja tehtävänannot: Palvelinten hallinta.
- Partanen, Aleksi. 2025. Linux-palvelimet: Viides viikko – Nimekäs. Luettavissa: https://partanennn.github.io/linux-servers/practices/fifth-week.html
- AONCODES. Linux-course: h4.md. Luettavissa: https://github.com/AONCODES/linux-course/blob/main/h4.md
- Tekoäly (Google Gemini): Käytetty apuna tekstin jäsentelyssä, muokkailussa ja Markdownin muokkailussa myös 
