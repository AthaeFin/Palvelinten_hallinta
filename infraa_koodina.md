# Infraa koodina

### x) tiivistelmä
- hakemiston init.sls on YAML konfiguraatiotiedosto
- On kahdenlaisia kieliä, käännettäviä ja tulkattavia. YAML on käännettävissä Pythoniksi, jolloin Salt pystyy käyttämään sitä.
- Isoilla ja pienillä kirjaimilla on väliä, tabulaattorin sijasta käytetään välilyöntejä
- tärkeät tilafunktiot pkg, file, service ja user, cmd:tä vakavasti harkiten



#### esimerkki konfiguraatiotiedostosta:
![](../Pictures/Screenshots/Screenshot 2025-03-24 122813.png)
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

![](../Pictures/Screenshots/Screenshot 2025-04-14 161253.png)

uudestaan idempotentin testaamiseksi:

![](../Pictures/Screenshots/Screenshot 2025-04-14 161218.png)

tree ja kaksi tiedostoa (erkkiesimerkki ja kilpikonnia)

![](../Pictures/Screenshots/Screenshot 2025-04-14 162715.png)

käyttäjä on luotu (salasanalla ei pääse sisään):

![](../Pictures/Screenshots/Screenshot 2025-04-14 162645.png)


#### Lähteet: 
- https://terokarvinen.com/palvelinten-hallinta/#h3-infraa-koodina tehtävänanto, Tero Karvinen, luettu 14.4.2025
- https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml YAML-käyttöohjeet, luettu 14.4.2025
- https://terokarvinen.com/2024/hello-salt-infra-as-code/ infra koodina, Tero Karvinen, luettu 14.4.2025
- https://terokarvinen.com/2021/salt-run-command-locally/ salt-komentoja, Tero Karvinen, luettu 14.4.2025
