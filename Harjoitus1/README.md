# Raportti: Palvelinten hallinta - Harjoitus 1
x) Lue ja tiivistä
* *Tiivistelmä*
* SSH-avainpari korvaa haavoittuvat salasanat turvallisella kryptografialla
* Julkinen avain kopioidaan palvelimelle komennolla 'ssh-copy-id', minkä jälkeen kirjautuminen tapahtuu ilman salasanakyselyä
* Oma kysymys: Miten avainten hallinta hoidetaan tilanteessa, jossa hallittavia palvelimia on useita? (Ansible tämän ratkaisee)

* Tiivistelmä
* Ansible on konfiguraationhallintatyökalu, joka mahdollistaa infran kuvaamisen koodina.
* Toimii toimii SSH-yhteyden yli, joten kohdekone vaatii SSH-demonin ja Pythonin.
* Keskeisiä osia ovat inventaariotiedosto 'hosts.ini', konfiguraatiotiedosto 'ansible.cfg', järjestelytiedosto 'site.yml' ja roolit 'roles'.
* Oma huomio: Ansible tekee vain tarvittavat muutokset, jolloin samaa komentoa uskaltaa ajaa turvallisesti uudelleen ja uudelleen.

a) Paikallinen asennus
* Asensin Debian 13-järjestelmän VirtualBox-koneeseen. Asennuksen yhteydessä loin käyttäjätunnuksen 'tommik' ja annoin pääkäyttäjän oikeudet. Testasin toimivuutta käynnistämällä virtuaalikoneen, siirtyi työpöydälle sutjakasti ja verkkoyhteys toimi myös ongelmitta.

---

b) SSH-demoni
* Varmistin, että ssh-demoni toimii virtuaalikoneessa. Ajoin komennon 'ssh localhost', järjestelmä varoitti aluksi isäntäkoneen sormenjäljestä, siihen riitti hyväksyntä tekstillä 'yes'. Annoin tunnukseni salasanan, minkä jälkeen kirjauduin onnistuneesti sisään koneelle. Tarkistin istunnon komennolla 'w', joka näytti kirjautuneet käyttäjät ja yhteydet.

<img width="1004" height="521" alt="image" src="https://github.com/user-attachments/assets/1df72f09-b2f7-4828-9001-5f5271ba1cfc" />

---

c) Pubkey (Salasanaton kirjautuminen avaimella)
* Automatisoin SSH-kirjautumisen julkisella avaimella. Loin aluksi uuden avainparin komennolla 'ssh-keygen'. Sen jälkeen kopioin julkisen avaimen paikalliselle koneelle komennolla 'ssh-copy-id localhost'. Testasin sen toimivuutta ajamalla uudelleen komennon 'ssh localhost'. Nyt kirjautui sisään ilman salasana kyselyä.

 <img width="1004" height="357" alt="image" src="https://github.com/user-attachments/assets/e3102e82-83b8-4cc3-84bd-bdaab2f378d1" />
 
