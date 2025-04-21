## pkg-file service

### x) Tiivistä
https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/


- pkg-file-service on yksinkertaisuudessaan kolmivaiheinen: 
1. Ohjelman asennus, 2. korvaa konfigurointitiedosto, 3. uudelleenkäynnistä daemon.

- luodaan init.sls omaan hakemistoonsa /srv/salt/ohjelma/init.sls:

		ohjelma:
		  pkg.installed
		  
		## korvaa alkuperäisen konfigurointitiedoston
		/etc/ohjelma/ohjelma_config:
		  file.managed:
		    - source: salt://ohjelma_config

- luodaan kopio alkuperäisestä konfigurointitiedostosta /etc/ohjelma/ohjelma_config /srv/salt/ohjelma/ohjelma_config

### a) Apache manuaalisesti

Potkaisin edellisten tehtävien masterin ja minionin käyntiin pienillä init-tiedoston muokkauksilla, laitoin nyt salonkikelpoisesti (tylsästi) nimet master ja minion, ja master käynnistää asennuksen lopuksi salt-masterin, jotta minionin avaimen voi hyväksyä. 

Vagrantilla kirjoitin vagrant ssh master, jonka jälkeen asensin apache2-ohjelman ja käynnistin sen:

		sudo apt install apache2
		sudo service apache2 restart

jonka jälkeen vielä tarkistin, että se on ihan aikuisten oikeasti päällä:

		systemctl status apache2
  
![Screenshot 2025-04-21 182310](https://github.com/user-attachments/assets/fa4b7f55-926c-4fb2-a65e-7f8d037170c3)

Koska kärsin jonkinasteisesta valikoivasta muistista, lähdin etsiskelemään index.html-tiedostoa, joka on apachen oletussivu, lopulta löysin sen hakemistosta /var/www/html, otin varmuuskopion ja muokkasin siitä yksinkertaisen testisivun, testatakseni, että se toimii:

		sudo cp index.html kopioindex.html
		sudoedit index.html
		
Hieno kuin mikä:

![Screenshot 2025-04-21 202404](https://github.com/user-attachments/assets/74f2ec8a-011f-40e9-9dcf-3234a903ecb0)


Selaimella kokeiltuna:

![Screenshot 2025-04-21 184805](https://github.com/user-attachments/assets/62adec0a-dab5-4754-966f-9e299f2f2e98)


lopuksi pistin molemmat koneet poistoon: 

		vagrant destroy


### Apache koodilla

Pistin uudestaan Vagrantilla master- ja minion -virtuaalikoneet pystyyn, tällä kertaa tavoitteena tehdä init.sls -tiedosto, joka hakee index.html -tiedoston Apachea varten tehdystä hakemistosta polusta /srv/salt/apache/index.html. Tämän jälkeen ajetaan tilakäsky, ja lopputuloksena selaimella katsottuna meillä pitäisi olla simppeli testisivu oletussivuna. 

		sudo mkdir -p /srv/salt/apache2
		cd /srv/salt/apache2
		sudoedit index.html
		sudoedit init.sls

index.html:

![Screenshot 2025-04-21 214330](https://github.com/user-attachments/assets/8af7b066-0d9b-494b-88ec-f0bc2c508d4c)


init.sls:

		apache2:
		  pkg.installed
		
		/var/wwww/html/index.html:
		  file.managed:
		    - source: /srv/salt/apache2/index.html
		    
![Screenshot 2025-04-21 220443](https://github.com/user-attachments/assets/c81b4f64-c0e5-4b5d-842a-78be2a7fb099)


Ajoin tilan masterilla, testasin sen olemassaolon ja kokeilin selaimella, onko testisivu korvannut oletussivun: 

		sudo salt-call --local state.apply apache
		systemctl status apache2
  
  ![Screenshot 2025-04-21 222022](https://github.com/user-attachments/assets/1d0cf466-4e83-416a-8af6-d63f215005de)

  ![Screenshot 2025-04-21 220539](https://github.com/user-attachments/assets/73cdb9fa-32a2-4f38-bd7a-173963607579)


### b) SSHD

Edelläkuvatusti luodaan uusi hakemisto, init.sls ja konfiguraatiotiedosto. 

Manusti ensin asensin openssh-serverin:

		sudo apt install openssh-server
		
Jonka jälkeen lähdin tutkiskelemaan hakemistoa /etc/ssh/, josta löysin konfiguraatiotiedoston sshd_config, jonka jälkeen tein uuden hakemiston ja kopioin edellämainitun tiedoston sinne.

		sudo mkdir -p /srv/salt/sshd
		sudo cp sshd_config /srv/salt/sshd/

Siirryin takaisin juuri tekemääni moduulin hakemistoon, jossa muokkasin tiedostoa:

		sudoedit sshd_config 

![Screenshot 2025-04-21 225112](https://github.com/user-attachments/assets/6364b9e5-af60-4696-a5a9-0b46206b5fd0)

Takapirulta kysyin, että mikä olisi kiva portti, vastaukseksi sain pitkän tarinan Half-Life -servereistä, varatuista porteista ja numeron 27022, joten käytin sitä, vapautin Vagrantille portin 22. Lopuksi poistin openssh-serverin:

		sudo salt-call --local -l info state.single pkg.removed openssh-server

Tämän jälkeen tein /srv/salt/sshd -hakemistoon init.sls -tiedoston, joka ei millään meinannut toimia, mutta luulen ymmärtäväni mistä kiikasti: edellisen tehtävän tiedostopolku oli paikallinen, mutta tällä kertaa laitoin verkon yli minionin suoritettavaksi, jolloin tiedostopoluksi määrittelin salt://sshd/shhd_config, jolloin rupesi kummasti toimimaan:

![image](https://github.com/user-attachments/assets/3e7bdcd7-f227-427f-b1de-e45229800e57)

![Screenshot 2025-04-22 004732](https://github.com/user-attachments/assets/c386a7d8-d9c9-427c-b040-1bd86ea29dfa) ![Screenshot 2025-04-22 004747](https://github.com/user-attachments/assets/f20811eb-1960-4c4b-98ff-7ca4664a68fd)




Ojasta allikkoon, julkinen avain ei toiminut. Lähdin korjaamaan ongelmaa generoimalla master-koneella komennolla ssh-keygen avaimen:

		cd .ssh
		ssh-keygen
		cat id_rsa.pub

![image](https://github.com/user-attachments/assets/2fed4dd3-e2a5-4b11-80bf-3d6289aeb5fc)

Tulosteen kopioin minionille avaamalla uuden komentoikkunan, alempaan id_rsa.pub kohtaan tulee pasteta se, mitä ylempänä kopioitiin:

		cd .ssh
		echo ssh.rsa id_rsa.pub >> authorized_keys

tämän jälkeen yhteys toimi:
		
		ssh -p 27022 vagrant@10.20.30.101

![image](https://github.com/user-attachments/assets/12e9b6e4-232e-40a3-b231-d019f9fef9f2)

Tehtävän tekemiseen meni n. 6h, mukaanlukien raportti. 

#### lähteet:
kotitehtävä: Tero Karvinen, https://terokarvinen.com/palvelinten-hallinta/#h4-pkg-file-service luettu: 22.4.2025

pkg-file-service, Tero Karvinen, 2018, https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/ luettu: 22.4.2025

salt states, Tero Karvinen, 2018 https://terokarvinen.com/2018/salt-states-i-want-my-computers-like-this/ luettu: 22.4.2025
