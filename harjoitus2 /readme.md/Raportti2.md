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

---

## a) Sudoless
Testasin ensin, että salasanaton SSH-yhteys toimii omalla tunnuksellani (`ssh localhost`). Tämän jälkeen määritin `tommik`-käyttäjälleni salasanattoman sudon:

```bash
sudo visudo -f /etc/sudoers.d/tommik
```

Lisäsin tiedostoon rivin:
```text
tommik ALL=(ALL) NOPASSWD: ALL
```

Testasin toimivuuden nollaamalla sudo-välimuistin ja tarkistamalla käyttäjän:

```bash
sudo -k
sudo whoami
```

Komento palautti suoraan `root` kysymättä salasanaa.

<img width="971" height="183" alt="image" src="https://github.com/user-attachments/assets/3a14f9c7-c60d-47d2-b3e5-7def91ad9bf6" />

---

## b) Antero

Loin työkansion `~/h2` sekä perusasetustiedostot `hosts.ini` ja `ansible.cfg`:

**`hosts.ini`:**

```ini
localhost
```

**`ansible.cfg`:**

```ini
[defaults]
inventory = hosts.ini
host_key_checking = False
interpreter_python = auto_silent
```

HUOMIO!! `interpreter_python = auto_silent`-rivi lisätty vasta kesken tehtävän, aluksi en muistanut koko hommaa.

Rakensin `site.yaml`-playbookin, joka loi `antero`-käyttäjän, kopioi hänelle julkisen SSH-avaimeni ja määrittää salasanattoman sudon:

```yaml
---
- name: Harjoitus 2 - Voileipa
  hosts: all
  become: true

  tasks:
    - name: Create passwordless user antero
      ansible.builtin.user:
        name: antero
        state: present
        create_home: true
        shell: /bin/bash

    - name: Set authorized key for antero
      ansible.posix.authorized_key:
        user: antero
        state: present
        key: "{{ lookup('file', '/home/tommik/.ssh/id_ed25519.pub') }}"

    - name: Allow passwordless sudo for antero
      ansible.builtin.copy:
        content: "antero ALL=(ALL) NOPASSWD: ALL\n"
        dest: /etc/sudoers.d/antero
        owner: root
        group: root
        mode: "0440"
        validate: "visudo -cf %s"
```

Ajoin playbookin komennolla `ansible-playbook site.yaml` ja testasin kirjautumisen sekä salasanattoman sudon:

```bash
ssh antero@localhost
sudo -k
sudo echo "See you at TeroKarvinen.com"
```

Kirjautuminen ja `sudo echo` menivät suoraan läpi ilman salasanakyselyä.

<img width="1004" height="259" alt="image" src="https://github.com/user-attachments/assets/4431fb5e-2ccf-4078-8a81-5ede68a5cd6a" />

---

## c) Package
Lisäsin `site.yaml`-tiedostoon tehtävän, joka asentaa kaksi pakettia (`tree` ja `htop`) `apt`-moduulilla:

```yaml
    - name: Install two packages
      ansible.builtin.apt:
        name:
          - tree
          - htop
        state: present
        update_cache: true
```

Ajoin `ansible-playbook site.yaml` ja varmistin asennuksen ajamalla terminaalissa komennon `tree`.

<img width="1004" height="311" alt="image" src="https://github.com/user-attachments/assets/8469effe-1225-4be9-a75d-e5472e97fb00" />

---

## d) File

Loin työkansioon tiedoston `voileipa.txt` ja lisäsin `site.yaml`-playbookkiin tehtävän, joka kopioi sen Anteron kotihakemistoon:

```yaml
    - name: Copy multiline file to antero home
      ansible.builtin.copy:
        src: voileipa.txt
        dest: /home/antero/voileipa.txt
        owner: antero
        group: antero
        mode: "0640"
```

Ajoin playbookin ja tarkistin tiedoston oikeudet sekä sisällön, aluksi ilmoitti että Permission denied, sudolla onnistui:

```bash
sudo ls -la /home/antero/voileipa.txt
sudo cat /home/antero/voileipa.txt
```

### Käyttöoikeuksien selitys (`0640` / `-rw-r-----`)
* **`-` (ensimmäinen merkki):** Ilmaisee, että kyseessä on tavallinen tiedosto.
* **`6` eli `rw-` (Omistaja / Owner = `antero`):** Tiedoston omistajalla on lukuoikeus (`r` = 4) ja kirjoitusoikeus (`w` = 2), mutta ei suoritusoikeutta (`-` = 0).
* **`4` eli `r--` (Ryhmä / Group = `antero`):** Ryhmään kuuluvilla käyttäjillä on vain lukuoikeus (`r` = 4), mutta ei oikeutta muokata tai suorittaa tiedostoa.
* **`0` eli `---` (Muut / Others):** Kaikilta muilta käyttäjiltä on estetty kaikki oikeudet. Kun yritin lukea tiedostoa suoraan `tommik`-käyttäjällä ilman sudoa, järjestelmä esti sen (`Permission denied`), mikä vahvistaa oikeuksien toimivan.

<img width="1004" height="262" alt="image" src="https://github.com/user-attachments/assets/90b733e5-f9e8-455f-8d93-5fcf72a489f5" />

---

## e) Jotain muuta

Tutustuin uuteen moduliin komennolla `ansible-doc cron` ja lisäsin `site-yaml`-playbookiin tehtävän, joka luo `antero`-käyttäjälle päivittäisen ajastetun tehtävän (kirjoittaa aikaleiman lokitiedostoon joka päivä klo 12:00):

```yaml
    - name: Create a scheduled cron job for antero
      ansible.builtin.cron:
        name: "Daily timestamp log"
        user: antero
        hour: "12"
        minute: "0"
        job: "date >> /home/antero/paivakirja.log"
        state: present
```

Ajoin playbookin ja tarkistin Anteron crontab-taulun:

```bash
sudo crontab -u antero -l
```

Terminaaliin ilmestyi Ansiblen luoma rivi `0 12 *** date >> /home/antero/paivakirja.log`.

<img width="1004" height="483" alt="image" src="https://github.com/user-attachments/assets/1b9f34a4-82e1-42f3-a3c1-77a3098fd510" />

---

## Lähteet
* Karvinen, Tero 2026: Palvelinten hallinta-kurssin tehtävänanto harjoitukseen 2.
* Karvinen, Tero 2026: Sudo without password. Luettavissa: https://terokarvinen.com/passwordless-sudo/.
* Munroe, Randalla 2006: xkcd 149: Sandwich. Luettavissa: https://xkcd.com/149/.
* Karvinen, Tero 2026: Passwordless Sudo with Ansible. Luettavissa: https://terokarvinen.com/passwordless-sudo-with-ansible/.
* Ansible Documentation: `ansible-doc` (copy, apt, file, user, authorized_key, cron).
* Tekoälyä (Google Gemini) käytetty tukena oppimiseen, vianmääritykseen ja raportin jäsentelyyn.

