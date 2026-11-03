# Palvelinten hallinta - Harjoitus 2 (Voileipä)

---

## x) Lue ja tiivistä

### Karvinen 2026: Sudo withous password
* Käyttäjälle voidaan antaa oikeus suorittaa `sudo`-komentoja ilman salasanan kysymistä määrittämällä sääntö `NOPASSWD: ALL`.
* Muokkaukset tehdään turvallisesti `sudo-visudo`-komennolla tai luomalla erillinen tiedosto `/etc/sudoers.d/`-hakemistoon, sillä `visudo` tarkistaa tiedoston syntaksin ennen tallennusta.
* Testauksessa käytetään komentoa `sudo-k`, joka tyhjentää aiemmin syötetyn salasanan välimuistista, jolloin salasanattomuus voidaan tarkistaa luotettavasti.
* **Oma huomio:** Jos `/etc/sudoers`-tiedostoa muokkaisi suoraan tavallisella tekstieditorilla ja sinne jäisi kirjoitusvirhe, koko järjestelmän `sudo`-toiminto voisi mennä lukkoon.

### Munroe 2006: xkcd 149: Sandwich
* Sarjakuvassa henkilö pyytää kaveriltaan voileipää ja saa kieltävän vastauksen, mutta komennolla `sudo make me a sandwich` pyyntö toteutetaan välittömästi.
* **Oma huomio:** Kuvaa hyvin Linuxin käyttöoikeusmallia: tavallisella käyttäjällä ei ole oikeuksia järjestelmätason muutoksiin, mutta `sudo` antaa pääkäyttäjän (root) valtuudet, jolloin järjestelmä tottelee mukisematta.

### Karvinen 2026: Passwordless Sudo with Ansible
* Esittelee, miten salasanaton sudo ja SSH-avainkirjautuminen automatisoidaan Ansiblella luomalla uusi käyttäjä (`antero`), lisäämällä hänet `sudoless`-ryhmään ja luomalla sääntötiedosto polkuun `/etc/sudoers.d/sudoless`.
* Käsittelee "muna vai kana" -ongelmaa: kun salasanatonta sudoa ei vielä ensimmäisellä ajokerralla ole, Playbook ajetaan lipulla `--ask-become-password` (`-K`).
* Muistuttaa Teron säännöstä #1: tee asetus ensin käsin ja vie se vasta toimivana automaatioon.
* **Oma huomio:** Artikkelin luonnoksessa sudoers-tiedoston oikeuksiksi oli laitettu `"0644"`, mutta käytin omassa toteutuksessani tiukempia `"0440"`-oikeuksia sekä `validate: "visudo -cf %s"` -varmistusta.

---

### Ansiblen sisäänrakennettu dokumentaatio (`ansible-doc`)

#### `ansible-doc copy`
* **Johdanto:** Kopioi tiedostoja paikalliselta hallintakoneelta kohdekoneille.
* **Optiot:**
* `content`: Kirjoittaa suoraan merkkijonon kohdetiedoston sisällöksi (`src`-option sijaan).
* `dest`: Kohdepolku palvelimella, johon tiedosto kopioidaan.
* `src`: Kopioitavan lähdetiedoston polku hallintakoneella.
* `owner`, `group`, `mode`: Määrittävät tiedoston omistajan, ryhmän ja käyttöoikeudet.
