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

<img width="358" height="186" alt="image" src="https://github.com/user-attachments/assets/134e7d3d-1dcf-4d97-9f47-a45b69742cfc" />
