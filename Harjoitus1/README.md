# Raportti: Palvelinten hallinta - Harjoitus 1
## x) Lue ja tiivistä
* *Tiivistelmä*
* SSH-avainpari korvaa haavoittuvat salasanat turvallisella kryptografialla
* Julkinen avain kopioidaan palvelimelle komennolla 'ssh-copy-id', minkä jälkeen kirjautuminen tapahtuu ilman salasanakyselyä
* Oma kysymys: Miten avainten hallinta hoidetaan tilanteessa, jossa hallittavia palvelimia on useita? (Ansible tämän ratkaisee)

* Tiivistelmä
* Ansible on konfiguraationhallintatyökalu, joka mahdollistaa infran kuvaamisen koodina.
* Toimii toimii SSH-yhteyden yli, joten kohdekone vaatii SSH-demonin ja Pythonin.
* Keskeisiä osia ovat inventaariotiedosto 'hosts.ini', konfiguraatiotiedosto 'ansible.cfg', järjestelytiedosto 'site.yml' ja roolit 'roles'.
* Oma huomio: Ansible tekee vain tarvittavat muutokset, jolloin samaa komentoa uskaltaa ajaa turvallisesti uudelleen ja uudelleen.
* 
