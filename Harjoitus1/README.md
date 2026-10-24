# Raportti: Palvelinten hallinta - Harjoitus 1
x) Lue ja tiivistä
* *Tiivistelmä*
- SSH-avainpari korvaa haavoittuvat salasanat turvallisella kryptografialla
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
 
--- 

d9 Hei Ansible (ensimmäinen Ansible-ajo)
* Asensin tarvittavat työkalut antamalla komennot 'sudo apt-get update' ja 'sudo apt-get install ansible micro bash-compilation tree'. Loin projektille oman kansion ja sitten siirryttiin sinne. Loin inventaariotiedoston 'hosts.ini', johon lisäsin kohdekoneeksi 'localhost'. Tein ensimmäisen testin komennolla 'ansible all -a 'uptime' -i hosts.ini'.

<img width="1004" height="351" alt="image" src="https://github.com/user-attachments/assets/22da452c-e9d6-4858-8eff-679c412b7823" />

* Huomasin tuloksessa varoituksen Python-tulkkiin. Korjasin ongelman lisäämällä 'hosts.ini'-tiedoston loppuun tulkkimuuttujan '[all:vars]' ja 'ansible_python_interpreter=/usr/bin/python3'. Jotta '-i hosts.ini'-määritystä ei olisi tarvinnut toistaa jokaisella kerralla, loin 'ansible.cfg'-tiedoston ja määritin siihen oletusinventaarion sekä 'display_args_to_stdout=true'-asetuksen.

* Loin myös orkestrointitiedoston 'site.yml'. Ajoin ensimmäisen kerran komennon 'ansible-playbook site.yml', jolloin sain virheilmoituksen siitä, että roolia 'hello' ei löydy. Loin vaaditun kansiorakenteen roolille komennolla 'mkdir -p roles/hello/tasks/'. Sen jälkeen loin ja muokkasin roolin tehtävätiedostoa 'roles/hello/tasks/main.yml', jonne kirjoitin tiedoston kopiointikomennon polkuun '/tmp/hello-ansible'.

* <img width="1004" height="202" alt="image" src="https://github.com/user-attachments/assets/9de8662b-fb84-4745-8060-81835d5c7851" />

