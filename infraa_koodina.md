# Infraa koodina

### x) tiivistelmä, lähteet merkitty loppuun:
- hakemiston init.sls on YAML konfiguraatiotiedosto
- On kahdenlaisia kieliä, käännettäviä ja tulkattavia. YAML on käännettävissä Pythoniksi, jolloin Salt pystyy käyttämään sitä.
- Isoilla ja pienillä kirjaimilla on väliä, tabulaattorin sijasta käytetään välilyöntejä
- tärkeät tilafunktiot pkg, file, service ja user, cmd:tä vakavasti harkiten



#### Esimerkki konfiguraatiotiedostosta:
![Screenshot 2025-03-24 122813](https://github.com/user-attachments/assets/b71a2f64-871b-443d-9d73-34ece63bc07f)

Käytin tätä docker compose filena omassa projektissa, tässä on kaikki kolme elementtiä, Lists, Scalar, Dictionary.

### a) Hei infrakoodi!

Ensiksi mennään Vagrantilla master-koneelle komennolla:

	vagrant ssh harvardhelia
	
Luodaan uusi hakemisto ja siirrytään sinne:

	mkdir -p /srv/salt/erkki
	cd /srv/salt/erkki
	
tekstieditorilla luodaan konfiguraatiotiedosto, jota kutsutaan myöhemmin:

	sudoedit init.sls	

tekstieditoriin kirjoitetaan:

	/tmp/erkki:
	  esimerkki
 
### b) infrakoodin ajo verkon yli

	sudo salt '*' state.apply erkki
	
"erkki" kutsuu hakemistoa ja siellä olevaa init-tiedostoa, ja ajaa sinne kirjoitetut komennot.

### c) uusi ja parempi init.sls

	mkdir -p /srv/salt/tervehdys
	cd /srv/salt/tervehdys
	sudoedit init.sls
		
init-tiedostossa:

	#tiedoston luonti
	/tmp/kilpikonnia:
	  file.managed
	  
	#ohjelman asentaminen
	tree:
	  pkg.installed
	
	#käyttäjän lisääminen
	kilpakonna:
	  user.present
	
ja lopuksi: 

	sudo salt '*' state.apply tervehdys


testaaminen:

![Screenshot 2025-04-14 161253](https://github.com/user-attachments/assets/d5d3ae74-6f32-45c5-91a9-7b23a89de2b2)

uudestaan idempotentin testaamiseksi:

![Screenshot 2025-04-14 161218](https://github.com/user-attachments/assets/17ad2a60-c713-4e19-8f8e-ab2cffb6685e)

tree ja kaksi tiedostoa (erkkiesimerkki ja kilpikonnia)

![Screenshot 2025-04-14 162715](https://github.com/user-attachments/assets/95c53043-b17b-4313-bd6f-7a12e2de072a)

käyttäjä on luotu (salasanalla ei pääse sisään):

![Screenshot 2025-04-14 162645](https://github.com/user-attachments/assets/2d43bf29-a301-4145-b362-99817ccd924f)



#### Lähteet: 
- https://terokarvinen.com/palvelinten-hallinta/#h3-infraa-koodina tehtävänanto, Tero Karvinen, luettu 14.4.2025
- https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml YAML-käyttöohjeet, luettu 14.4.2025
- https://terokarvinen.com/2024/hello-salt-infra-as-code/ infra koodina, Tero Karvinen, luettu 14.4.2025
- https://terokarvinen.com/2021/salt-run-command-locally/ salt-komentoja, Tero Karvinen, luettu 14.4.2025
