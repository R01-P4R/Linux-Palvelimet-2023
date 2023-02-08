# x)

 
 
 
 
## 	 Apache Software Foundation 2023: Getting Started (https://httpd.apache.org/docs/2.4/getting-started.html)

Artikkelin tarkoituksena on toimia pohjustuksena Apachen HTTP-palvelimien ja nettisivujen toimintaan.

   * Osoitteet verkossa ilmoitetaan URL:n avulla
   * Asiakasohjelmaa (esim. nettiselain) käytetään yhdistämään palvelimeen
   * Yhdistääkseen palvelimelle asiakasohjelman täytyy ensin yhdistää palvelimen nimi ja IP-osoite
   * Virtual Hostien avulla pystyy ylläpitämään monia eri sivuja samalla palvelimella
   * Apachen palvelin konfiguroidaan tekstitiedostojen avulla
   * Lokit auttavat vianmäärityksessä ja korjauksessa

  
   
 # H6
 Harjoituksen tarkoituksena on vuokrata oma virtuaalipalvelin ja laittaa sen asetukset kuntoon, asentaa weppipalvelin ja etsiä merkkejä murtautumisyrityksistä. Tein harjoituksen omalla pöytäkoneella 08.02.2023.
 

 
 
### Laitteisto
 
* Käyttöjärjestelmä	Microsoft Windows 10 Enterprise LTSC 64 bit
* Prosessori i5-6600
* RAM 16 GB
* Virtuaaliohjelmisto : Oracle VM VirtualBox
* Virtuaalikoneen käyttöjärjestelmä: Debian-live-11.6.0-amd64-xfce+nonfree.iso





## Virtuaali palvelimen vuokraus Linodesta (https://www.linode.com/)

Aluksi loin käyttäjätunnuksen Linoden palveluun ja lisäsin maksukortin. Tämän jälkeen loin uuden virtuaalipalvelimen 'create Linode' kohdasta.

![Add file: Upload](/ss/h61.PNG)
    
Valitsin käyttöjärjestelmäksi Debian 11:sta ja alueeksi Frankfurt, DE (eu-central). Virtuaalipalvelimeksi valitsin Nanode 1 GB:n.  
  
![Add file: Upload](/ss/h62.PNG)  


      

## Uuuden virtuaalipalvelimen käyttöönotto  (21:15

Aloitin harjoituksen yrittämällä yhdistää virtuaalipalvelimeen komennolla 

    $ ssh root@IP-osoite
    
Mutta sain viestin, että ssh-komentoa ei löydy. Yritin käynnisttää ssh:n uudestaan komennolla 

    $ sudo systemctl restart ssh
    
Mutta kyseistä palvelua ei ilmeisesti löydy. Kokeilin vielä tarkistaa ssh:n version komennolla

    $ ssh -v
    
Mutta sain viestin, että ssh-komentoa ei löydy.

![Add file: Upload](/ss/h63.PNG) 


Etsityä netitä löysin mahdollisen ratkaisun ja päätin kokeilla asentaa ssh:n tämän ohjeen mukaan (https://www.cyberciti.biz/faq/debian-linux-install-openssh-sshd-server/) Käyttämällä komentoja 

    $ apt-get update
    
   ja
   
    $ apt-get install openssh-server

Sain seuraavan error-viestin 

![Add file: Upload](/ss/h64.PNG) 

Löysin netistä mahdollisen tavan vapauttaa muistia (https://askubuntu.com/questions/178909/not-enough-space-in-var-cache-apt-archivesVapautin) poistamalla kaikki paketit jota ei ole asennettu komennolla

    $ sudo apt-get autoclean

Komento onnistui ja sain asennettua openssh-serverin.

Varmistin vielä, että ssh-palvelu on päällä komennoilla

    $ sudo systemctl restart ssh
    
    $ sudo systemctl status ssh
    
 ![Add file: Upload](/ss/h65.PNG)    
 
 Ssh näyttäisi olevan käynnissä. Seuraavaksi yritin yhdistää uudestaa virtuaalipalvelimeen komennolla
 
    $ ssh root@IP-osoite
    
   
 Pääsin sisään syötettyäni Linodeen luomani salasanan. Seuraavaksi päivin paketit ja asensin palomuurin komnennoilla
 
    
    $ sudo apt-get update
    
    $ sudo apt-get install ufw
  
  Seuraavaksi loin palomuuriin reiän ssh:lle ja laitoin palomuurin päälle komennoilla
  
    $ sudo ufw allow 22/tcp
    
    $ sudo ufw enable
    
  ![Add file: Upload](/ss/h66.PNG)    
    

Seuraavaksi loin uuden käyttäjän ja annoin sille oikeuksia kommennoilla 

    $ sudo adduser roi
    $ sudo adduser roi sudo
    $ sudo adduser roi adm
    
    
Kokeilin kirjautua uudella käyttäjällä eri terminaalissa komennolla

    $ ssh roi@IP-osoite
    
  Ja pääsin sisään. Seuraavaksi lukitsin root-käyttäjän komenolla
  
    $ sudo usermod --lock root
    
 Tämän jälkeen kävin muokkaamassa muokkaamassa ssh:n konfiguraatio tiedostoa ja poistin root kirjautumisin käytöstä komennolla
 
    $ sudoedit /etc/ssh/sshd_config
    
Ja muuttamalla kohtaa PermitRootLogin yes -> no
   
  ![Add file: Upload](/ss/h67.PNG) 
   

Tämän jälkeen käynnistin ssh:n uudestaan komennolla 

    $ sudo systemctl restart ssh
   
Seuraavaksi kirjauduin sisään uudella käyttäjällä komennolla

     $ ssh roi@IP-osoite
 
 Sitten päivitin ja asensin uusimmat paketit komennoilla 
 
     $ sudo apt-get update
     $ sudo apt-get upgrade
     
## Apache-palvelimen asentaminen virtuaalipalvelimelle (22:27)

  Asensin Apache-palvelimen kommenoilla 
  
      $ sudo apt-get update
      $ sudo apt-get install apache2
      
  Tein palomuuriin reiän Apachelle komennolla
  
       $ sudo ufw allow 80/tcp
       
  ja käynnistin Apache-palvelimen komennolla
  
       $ sudo systemctl start apache2
       
   Tarkistin, että Apache on käynnissä komennoilla 
    
       $ sudo systemctl status apache2 
       $ curl 'localhost'
       
   ![Add file: Upload](/ss/h68.PNG)  
   
   Apachen esimerkkisivu latautui.
   
   ![Add file: Upload](/ss/h69.PNG)  
   
   
 Seuraavaksi vaihdoin Apachen esimerkkisivun komennolla
 
    $ echo Hello | sudo tee /var/www/html/index.html
    
    
   ![Add file: Upload](/ss/h670.PNG)  
    
   
 Tämän jälkeen tein palomuuriin reiän komennolla 
 
      $ sudo ufw allow 80/tcp
      
Kävin selaimessa tarkistamassa muutokset ja testisivu latautui


   ![Add file: Upload](/ss/h671.PNG)  
   
## Murtautumisyritykset (0:08)

Kävin tutkimassa /var/log/auth.log:sta merkkejä murtautumisyrityksistä komennolla

    $ sudo less /var/log/auth.log
   
 ![Add file: Upload](/ss/h673.PNG)  
 
Näyttäisi siltä että monta eri käyttäjää on yrittänyt sisään virtuaalipalvelimeen, mutta todennus on epäonnistunut. Kävin myös katsomassa /var/log/apache2/access.log:ia komennolla
 
    $ sudo less /var/log/apache2/access.log
    
Mutta en löytänyt sieltä mitään omaan silmään hälyyttävää.

 ## Lopuksi 
 
 Tässä harjoituksessa tutustuin vuokrasin virtuaalipalvelimen ja laitoin sen toimintaan. Asensin myös webbipalvelimen ja yutkin myös murtautumisyrityksiä. Ongelmia tuotti ssh:n puuttuminen ja muistin loppuminen. 
 
 
## Lähteet


Linux Palvelimet 2023 alkukevät, Tero Karvinen (https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/)

First Steps on a New Virtual Private Server – an Example on DigitalOcean and Ubuntu 16.04 LTS, Tero Karvinen - 19.11.2017 (https://terokarvinen.com/2017/first-steps-on-a-new-virtual-private-server-an-example-on-digitalocean/)


#### Tehnyt Roi Partanen 08.02.2023-09.02.2023