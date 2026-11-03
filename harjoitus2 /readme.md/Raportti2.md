# Palvelinten hallinta - Harjoitus 2 (Voileipä)

---

## x) Lue ja tiivistä

### Karvinen 2026: Sudo withous password
* Käyttäjälle voidaan antaa oikeus suorittaa `sudo`-komentoja ilman salasanan kysymistä määrittämällä sääntö `NOPASSWD: ALL`.
* Muokkaukset tehdään turvallisesti `sudo-visudo`-komennolla tai luomalla erillinen tiedosto `/etc/sudoers.d/`-hakemistoon, sillä `visudo` tarkistaa tiedoston syntaksin ennen tallennusta.
* Testauksessa käytetään komentoa `sudo-k`, joka tyhjentää aiemmin syötetyn salasanan välimuistista, jolloin salasanattomuus voidaan tarkistaa luotettavasti.
* **Oma huomio:** Jos `/etc/sudoers`-tiedostoa muokkaisi suoraan tavallisella tekstieditorilla ja sinne jäisi kirjoitusvirhe, koko järjestelmän `sudo`-toiminto voisi mennä lukkoon.
