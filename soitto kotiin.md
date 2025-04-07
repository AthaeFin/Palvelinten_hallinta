
<h2> A) Vagrantin asennus: </h2>
Asensin omalle windows 11 koneelle Vagrantin osoitteesta: https://developer.hashicorp.com/vagrant/install?product_intent=vagrant, AMD64.

![image](https://github.com/user-attachments/assets/975d2f26-32d4-4a96-bf54-28bcc752851f)

Latauksen päätyttyä käynnistin asennusvelhon (wizard) ja käynnistin koneen uudelleen. 

![Screenshot 2025-04-07 190313](https://github.com/user-attachments/assets/21a89052-04fb-48a8-8d96-d47dbe6283bb)

Avasin komentorivin ja testasin, onko vagrant olemassa:

![Screenshot 2025-04-07 231829](https://github.com/user-attachments/assets/eb55bef7-6bd6-49be-9d59-749451495599)

Kävin manuaalisti tekemässä kansion, jota haluan vagrantin käyttävän, jonka jälkeen komentoriville syötin vagrant init

![Screenshot 2025-04-07 231935](https://github.com/user-attachments/assets/88414ca4-cd54-46de-a09c-e8a6fac0fd8e)
vagrant init luo konfiguraatiotiedoston, jota lähdin tekstieditorilla muokkaamaan. 



<h2> B) Linux-kone: </h2>
perusmuotoisen Debian-koneen saa muokkaamalla konfiguraatiotiedostoa:

![Screenshot 2025-04-07 194351](https://github.com/user-attachments/assets/118cf40d-ecdf-402a-873e-947aee794fe5)

ja kirjoittamalla komentoriville vagrant up:
![Screenshot 2025-04-07 232606](https://github.com/user-attachments/assets/570e66cc-52da-4027-bc6b-ae338936d271)

<h2> C, D ja E) Salt master ja minion: </h2>
Tällä konfiguraatiotiedostolla sain molemmat virtuaalikoneet tehtyä ja konfiguroitua:

![image](https://github.com/user-attachments/assets/4c626145-4560-4bdf-9aae-693aa4bececd)
Kutsun tätä siis Frankensteinin koodiksi, koska osa on Tero Karviselta lainattua, osa täältä: https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/linux-deb.html ja osa omasta hatusta. 

- VIRHEITÄ -
  1. konfiguraatiotiedostoon laitoin isoilla alkukirjaimilla koulun ja opiskelijan, sain vikailmoitusta
     
     ![image](https://github.com/user-attachments/assets/5e46c35f-c620-44a3-9281-bea2b4b458d8)
     Vika korjautui kun laitoin konfiguraatiotiedostoon vain pieniä kirjaimia.

  3. ilmeisesti private_network ei ollut itsekeksittävä tieto, olin määritellyt sisaverkoksi
     ![image](https://github.com/user-attachments/assets/0704bf68-31da-4b08-8afa-0a1b47badb0d)
     Vika korjautui, kun määritteli verkon private_network.

pingaan masterilla minionia:
![image](https://github.com/user-attachments/assets/390d5289-8192-4223-a2e3-0e0d137d6693)

Avaimen hyväksyntä kompuroinnin kautta:
![image](https://github.com/user-attachments/assets/d83fac61-d1ad-47d7-ba48-1f804e4ece99)
- daemon pitää potkaista ensin päälle (oikealla komennolla......)

Testaillaan, toimiiko masterilla käskyttäminen:
![image](https://github.com/user-attachments/assets/4b098065-df2a-481b-8064-d5e65a2ee9b7)

![image](https://github.com/user-attachments/assets/997a985b-4f51-4e28-8eb3-fc81a0e84e24)


<h2> X) Sekalaista: </h2>

- hyödyllisiä komentoja: vagrant destroy (tuhoaa kaikki virtuaalikoneet, oletan että jos laittaa vagrant destroy _hostname_, pelkästään _hostname_ poistuu keskuudesta, RIP), vagrant up (tekee sen mitä konfiguraatiotiedostossa lukee),
  vagrant ssh _hostname_
- jos masterilla on palomuuri päällä, pitää avata portit 4505 ja 4506.
- konfiguraatiotiedostoilla voi määritellä, mikä minioneista tekee mitäkin.

Lähteet: 
https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/?fromSearch=salt%20quickstart%20salt%20stack%20master%20and%20slave%20on%20ubuntu%20linux
https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file
https://terokarvinen.com/2021/salt-run-command-locally/
https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/
https://www.youtube.com/watch?v=7DLfOGt8YvA&ab_channel=AutomationStepbyStep (toisinsanoen Vagranttia taukeille)

https://terokarvinen.com/palvelinten-hallinta/#h2-soitto-kotiin (tehtävänanto)



