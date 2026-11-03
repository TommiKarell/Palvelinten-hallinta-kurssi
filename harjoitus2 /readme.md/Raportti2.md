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
* **Esimerkki:**
```yaml
- name: Copy file with owner and permissions
  ansible.builtin.copy:
    src: /srv/myfiles/foo.conf
    dest: /etc/foo.conf
    owner: foo
    group: foo
    mode: '0644'
ansible-doc apt
```

#### `ansible-doc apt`
* **Johdanto:** Hallinnoi apt-ohjelmistopaketteja Debian- ja Ubuntu-pohjaisissa järjestelmissä.
* **Optiot:**
  * `name`: Asennettavan tai poistettavan paketin nimi tai lista paketeista.
  * `state`: Paketin haluttu tila (esim. `present` = asennettu, `absent` = poistettu).
  * `update_cache`: Päivittää pakettilistan ennen asennusta (`yes`/`no`), vastaa komentoa `apt update`.
* **Esimerkki:**

```yaml
- name: Update repositories cache and install "foo" package
  ansible.builtin.apt:
    name: foo
    update_cache: yes
```

#### `ansible-doc file`
* **Johdanto:** Asettaa tiedostojen, hakemistojen ja symbolisten linkkien attribuutteja tai luo ja poistaa niitä.
* **Optiot:**
  * `path`: Kohdetiedoston tai -hakemiston polku.
  * `recurse`: Asettaa attribuutit rekursiivisesti kansion sisällölle.
  * `src`: Linkitettävän lähdetiedoston polku (käytetään kun `state: link`).
  * `state`: Haluttu tila (esim. `directory`, `touch`, `link`, `absent`).
  * `owner`, `group`, `mode`: Omistaja, ryhmä ja käyttöoikeudet.
* **Esimerkki:**

```yaml
- name: Create a directory if it does not exist
  ansible.builtin.file:
    path: /etc/some_directory
    state: directory
    mode: '0755'
```

#### `ansible-doc user`
* **Johdanto:** Hallinnoi käyttäjätilejä ja niiden ominaisuuksia kohdejärjestelmässä.
* **Optiot:**
  * `name`: Käyttäjätunnuksen nimi.
  * `create_home`: Luo käyttäjälle kotihakemiston (`yes`/`no`).
  * `comment`: Käyttäjän lisätiedot / koko nimi (GECOS-kenttä).
  * `groups`: Lista ryhmistä, joihin käyttäjä liitetään.
  * `shell`: Käyttäjän oletuskomentotulkki (esim. `/bin/bash`).
  * `state`: Onko käyttäjä olemassa (`present`) vai poistetaanko se (`absent`).
  * `system`: Määrittää, luodaanko järjestelmätili (`yes`/`no`).
* **Esimerkki:**

```yaml
- name: Add the user 'johnd' with a specific uid and a primary group of 'admin'
  ansible.builtin.user:
    name: johnd
    comment: John Doe
    uid: 1040
    group: admin
```

#### `ansible-doc authorized_key`
* **Johdanto:** Lisää tai poistaa julkisia SSH-avaimia käyttäjän `authorized_keys`-tiedostosta.
* **Optiot:**
  * `user`: Käyttäjätunnus, jonka SSH-avaimia hallitaan.
  * `key`: Julkinen SSH-avain tekstinä tai polkuna tiedostoon (`lookup`-funktiolla).
* **Esimerkki:**

```yaml
- name: Set authorized key taken from file
  ansible.posix.authorized_key:
    user: charlie
    state: present
    key: "{{ lookup('file', '/home/charlie/.ssh/id_rsa.pub') }}"
```
