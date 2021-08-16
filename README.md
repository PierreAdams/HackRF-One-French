# HackRF-One
Présentation et démonstration des fonctionnalitées du HackRF One
## SDR 
Dans un premier temps, il est intéressant de savoir ce qu'est le SDR : 
> Software Defined Radio : Pour faire simple il s'agit d'une radio (recepteur/émeteur) remplacée logicielement  
__Avantage__ : la puissance du pc permet de traiter les signaux  
 
## HackRF One  
le HackRF est un outil de SDR, crée par [Great Scott Gadget](https://greatscottgadgets.com)  
![Scan](https://user-images.githubusercontent.com/39098396/79736639-6d92e600-82fa-11ea-8e76-a5e6a3ea749a.jpeg)  
__Prix__ : environ 300€ (Antenne comprise)  
__Réception / Emission__ : de 10Mhz à 6Ghz

## PortaPack  
Le HackRF peut être integré dans un boite nommée un "PortaPack" [lien github](https://github.com/furrtek/portapack-havoc)  
![Scan](https://user-images.githubusercontent.com/39098396/79736658-72579a00-82fa-11ea-8692-116d2b8a5461.jpeg)  
__Prix__ : 200 €  
le portapack permet d'avoir une interface graphique, et d'effectuer des manipulation sur le hackRF One.  
- le firmware officiel, développer par sharebrained : https://github.com/sharebrained/portapack-hackrf/  
- Havoc est un firmware __Non officiel__ pour le portapack développé par le grand [furrtek](https://github.com/furrtek/portapack-havoc)  

__Note :__ il existe des versions non officiels des portapacks pour hackRF One pour l'avoir testé, celui-ci fonctionne tout aussi bien ! : [Lien Amazon](https://www.amazon.fr/TOOGOO-Panneau-Tactile-Portapack-lhorloge/dp/B07WHJ8WGX/ref=sr_1_fkmr1_2?__mk_fr_FR=%C3%85M%C3%85%C5%BD%C3%95%C3%91&dchild=1&keywords=portapack+bogood&qid=1587725047&sr=8-2-fkmr1)  

Pour installer le firmware __Havoc__ sur Windows, une vidéo youtube trés claire à été réalisée sur le sujet :   
[Lien Youtube](https://www.youtube.com/watch?v=f0S9jWkRaQU.gif)

Lorsque le firmware de votre portapack sera installé il sera toujours possible de lancer le mode hackRF (sur pc) il suffit d'activer l'option __"HackRF Mode"__ dans le menu.  

![Scan](https://user-images.githubusercontent.com/39098396/80219795-42363100-8643-11ea-89bd-fab7d90ed604.PNG)  

--------------------------------------------------------------------


## Premier Branchement + Mise à jour du Firmware :  

Lorsque vous aurez reçu le HackRF, il faut d'abord mettre à jour le firmware et installer le paquet HackRF sur un PC:   
installer le paquet hackrf (Linux) :   
``` apt install hackrf ```   

Pour mettre à jour le firmware, se rendre sur le github de [Michael Ossmann](https://github.com/mossmann), l'un des menbres de Great Scott Gadget et le createur du Hackrf One.  
Télecharger son [Repo HackRF](https://github.com/mossmann/hackrf)  

* Installer l'image standard du firmware :  
``` 
__hackrf_spiflash -w hackrf_one_usb.bin__
```

* Mettre à jour le CPLD (Complex Programmable Logic Device) :
```
__hackrf_cpldjtag -x sgpio_if/default.xsvf__
```

* Vérifier si la mise à jour à été appliquée :    
```
__hackrf_info__
```

``` hackrf_info 
hackrf_info version: unknown
libhackrf version: unknown (0.5)
Found HackRF
Index: 0
Serial number: 000000000000000057b068dc22451163
Board ID Number: 2 (HackRF One)
Firmware Version: 2018.01.1 (API:1.02)
Part ID Number: 0xa000cb3c 0x00724357
```

## Commandes HackRF One : 

__Hackrf_info__ : lors du branchement du hackRF One, cette commande permet d'avoir des informations sur sa version   

__Hackrf_transfer__ : permet de capturer un transfère et de le restransmettre par la suite

__hackrf_debug__ : Commande de debug du hackRF One 

__hackrf_sweep__ : Commande qui va balayer un champ de fréquences et l'analyser 

__hackrf_spiflash__ : Mettre à jour le firmware 

__hackrf_cpldjtag__ : Mettre à jour le cpld


## Petit rappel utile - Convertion MHz - Ghz 
![Scan](https://user-images.githubusercontent.com/39098396/79741752-109b2e00-8302-11ea-80d2-f7132bdcff0b.png)


# Passons à la Pratique :  

## Portail :  

Passons maintenant à la pratique :  
pour se faire je vous conseille quelques logiciels de SDR sous linux :  
* [sdr angel](https://github.com/f4exb/sdrangel) 
* [Spectrum Analyzer](https://github.com/pavsa/hackrf-spectrum-analyzer)  
* [gqrx](https://gqrx.dk/)

J'utilise gqrx que je trouve très pratique et simple d'utilisation.
Nous allons pour commencer, essayer d'intercepter un signal d'une télécommande de portail :  
la première chose à faire est de trouver sur quelle fréquence la telecommande va communiquer avec le portail (en géneral au alentour de 433 Mhz), souvent indiquer soit sur la telecommande, soit sur le site du constructeur. 
Si ce n'est pas indiqué :  
> Internet est votre ami 

Avec gqrx nous allons pouvoir vérifier et ajuster la fréquence d'écoute (Assurez vous que le logiciel se base sur votre hackRF dans les paramètres de gqrx)  
Une fois sur la bonne fréquence, lorsque nous activons la télecommande nous appercevons bien le signal émis.

![Scan](https://user-images.githubusercontent.com/39098396/79851629-e0b55e80-83c5-11ea-8a63-2675dcfc38d9.png)

Nous avons donc maintenant une fréquence précise : 433.910000 Mhz 
 
Passons ensuite à l'enregistrement :   
``` hackrf_transfer -s 2 -f 433910000 -r open  ```   

Déclencher le signal pendant l'enregistrement :  

``` hackrf_transfer -s 2 -f 433910000 -t open -a 1 -x 24  ```   

Puis, réinvoquer le signal devant le portail pour l'ouvrir. 
Options utilisées :
```
 -s : précise le taux d'echantillons en MHz
 -f : précise la fréquence exacte enregistrée ou diffusée
 -r : Nom du fichier ou sera stocké notre signal 
 -t : Nom du fichier ou sera lu notre signal (afin de le retransmettre)
 -a : Amplifie le signal
 -x : le gain entre 0 et 47 dB (en Décibel)
```

On peut créer un script pour plus de professionnalisme : 

```
figlet Sesame, Ouvre toi ! 
hackrf_transfer -s 2 -f 433910000 -t open -a 1 -x 24
figlet C'est ouvert !  
```   
[Regarder la video](https://www.youtube.com/watch?v=HM1JgZUscY0)

## Talkie-Walkie : 

Il s'agit du même procédé, nous trouvons la fréquence, capturons le signal, le stockons et le rediffusons à notre guise. 

Dans mon cas, je capte le signal sur la fréquence 446 Mhz.

je capture une séquence d'apel par exemple  :   
```hackrf_transfer -s 2 -f 446000000 -r HackyWalkie```  
et la rediffuse    
```hackrf_transfer -s 2 -f 446000000 -r HackyWalkie```   

[Regarder la vidéo](https://www.youtube.com/watch?v=4-qAzXwfjRY)

## Manipulation - Portapack :  

Avec le portapack, toutes les manipulations que nous avons effectuées précedemment, pourront desormais être réalisés directement sur le portapack. 

Voici le menu d'__Havoc__ :  

![Scan](https://user-images.githubusercontent.com/39098396/80214273-d8b22480-863a-11ea-9671-3e846ce77b4f.PNG)

Prenons par exemple, une lampe avec une télécommande Hertzienne, nous pouvons voir qu'elle communique avec celle-ci sur la fréquence 433.920 Mhz.    
![Scan](https://user-images.githubusercontent.com/39098396/80212189-55430400-8637-11ea-9ea9-405f934ed19b.JPG)

Sur le portapack, dans l'onglet "Capture" nous allons régler la fréquence sur 433.9 Mhz, écouter le signal puis l'enregistrer afin de le rediffuser : 

![Scan](https://user-images.githubusercontent.com/39098396/80214995-08adf780-863c-11ea-871c-abbaafd03fc4.PNG)  
![Scan](https://user-images.githubusercontent.com/39098396/80214019-6a6d6200-863a-11ea-8f5b-724de95f83ec.PNG)


Une fois le signal capturé : se rendre dans la partie "Replay", ouvrir le fichier contenant le sginal capturé. (éventuellement régler la fréquence de diffusion)


![Scan](https://user-images.githubusercontent.com/39098396/80214776-b371e600-863b-11ea-8f68-dff7b8b85ac1.PNG)  
 
 __et hop le signal est rediffusé !!__ 

## Fonctionnalités Portapack : 

Le Wiki du Github de furrtek explique très bien toutes les options que nous offre le Portapack: https://github.com/furrtek/portapack-havoc/wiki

L'onglet __Play Dead__ est une sorte de barrière pour empecher les néophytes d'utiliser le portapack :  
l'erreur Firmware suivante apparait lorsqu'on rentre dans l'onglet : 

![Scan](https://user-images.githubusercontent.com/39098396/80584597-33b89280-8a12-11ea-81ae-130242bf418f.PNG)

Pour sortir, il faut entrer la combinaison prédefinie dans l'onglet : __Settings__ > __Play dead__    
Par defaut cette combinaison est :  __Haut-Bas-Gauche-Droite__ (Le bouton __Reset__ marche aussi ^^)  

L'onglet __"Search/CLose Call"__ permet d'identifier exactement la fréquence d'un signal, on précise un fenêtre, et on déclenche l'émetteur :   
![Scan](https://user-images.githubusercontent.com/39098396/80237787-9bab5980-865d-11ea-888f-cdbe48752591.PNG)  

L'onglet __Receivers__ permet de recevoir toutes sortes de fréquences :  

* __AD-B: Plane :__ système de surveillance pour le contrôle du trafic aérien (connaitre la position des avions)
* __ACARS :__ système de communication/surveillance entre les aéronef et les sations au sol
* __AIS :__  système d’échanges automatisés de messages entre navires par radio 
* __AFSk :__ audio frequency-shift keying, conçue pour véhiculer la voix ou de la musique, par exemple une liaison téléphone ou radio.
* __AUDIO :__ Recpteur Audio capable de recevoir different mode de fréquences (AM et FM) ![lien Github sharebrained](https://github.com/sharebrained/portapack-hackrf/wiki/Audio-Modes)
* __ERT :__ Paquet contenant les données d'un compteur éléctrique/Gaz pour faciliter la collecte de données.
* __POCSAG :__ Protocole de transmission radio utilisé pour les réseaux de radiomessagerie.
* __Radiosondes :__ 
* __TPMS :__ Tyre Pressure Monitoring System en anglais, Système de surveillance de la pression des pneumatiques.
* __APRS :__ Automatic Packet Reporting System est un système de radiocommunication numérique utilisé par les radioamateurs, qui permet le partage d'informations d'intérêt local.
* __DMR framing :__ Digital Mobile Radio, norme de radio numérique mobile ouverte et utilisée dans des produits commerciaux à travers le monde.
* __SigFox :__ Sigfox est un opérateur de télécommunications français communiquant sur la fréquence 868 MHz.
* __LoRa :__ LoRaWAN est un protocole de télécommunication permettant la communication à bas débit, par radio, d'objets à faible consommation électrique communiquant selon la technologie LoRa 
* __SSTV :__ Slow Scan Television, est une activité radioamateur qui vise à la transmission analogique d'images ([J'y consacre quelques lignes juste ici ](https://github.com/PierreAdams/HackRF-One-en-Francais/blob/master/README.md#sstv-slow-scan-television-)) 
* __TETRA framing :__ Terrestrial Trunked Radio, destinés aux équipes de sécurité : elle opére entre 380-400 MHz pour les services d’urgence et dans les bandes 410-430 MHz | 450-470 MHz | 870-880 MHz pour les applications civiles et privées.


L'onglet __Transmitters__ permet de transmettre toutes ces fréquences. 

Quelques sigles que l'on va retrouver lors de l'utilisation du hackRF One couplé du Portapack : 

* __LNA :__ Amplificateur à faible bruit.
* __AMP :__ Amplificateur.
* __VGA :__ Gain.


Info sur les Leds :  

![Scan](https://user-images.githubusercontent.com/39098396/80587157-59e03180-8a16-11ea-8317-6111b7c383ff.jpg)  

Chaque led à une couleur differente :


* __3V3, 1V8, RF :__  lorsque ces leds sont allumées, cela signifie que le Hackrf est allimenté (par une batterie externe par exemple)  
* __USB :__ connexion USB à un ordinateur, la communication est donc possible via les commandes __hackrf..__  
* __TX :__  le hackRF Transmet   
* __RX :__  le hackRF Recoit

* Le bouton __Reset__ : permet une remise à zero du Microcontrôleur
* Le bouton __ISP / DFU__ : permet de modifier les données en cas de probleme sur le HackRF


## SSTV (Slow Scan Television) : 

SSTV est une activité qui merite d'être creusée tant elle est interressante, comme vu précedemment c'est un mode qui permet de recevoir / émettre des images 

### SSTV with HackRF ONE   
Grace au HackRF One, nous pouvons également émettre des images via le mode SSTV :   

Démonstration en vidéo :   

[Vidéo SSTV Transmission](https://youtu.be/Pa1LIobzjHE) 

Voici l'image de base : (l'image doit etre en format Bitmap (320x256)  
![Scan](https://user-images.githubusercontent.com/39098396/127194828-9e39c4d0-284b-42cf-a762-f22c090206b7.jpg)
 
Voici la photo une fois recue :   
![Scan](https://user-images.githubusercontent.com/39098396/127194769-ca636417-7380-4816-aceb-ef0caa8e4974.PNG) 

autre exemple : 
[HackRF_SSTV](https://twitter.com/i/status/846778098670616576)


Logiciels permettant de decoder les images depuis un signal : 
- Windows : [MMSTV](https://hamsoft.ca/pages/mmsstv.php)   (Gratuit)
- Linux : [QSSTV](http://users.telenet.be/on4qz/qsstv/index.html)   (Gratuit) 
- iOS : [“SSTV Slow Scan TV” by Black Cat Systems](https://apps.apple.com/app/sstv/id387910013) (Payant, 3,49€)
- Mac OS X : [Multiscan 3B SSTV](https://www.qsl.net/kd6cji/) (gratuit) 
- Android : [Robot3](https://play.google.com/store/apps/details?id=xdsopl.robot36&hl=en) (Gratuit)    

  
source : [AMSAT](https://site.amsat-f.org/satellites/station-spatiale-internationale-i-s-s/ariss/recevoir-la-sstv-avec-liss/)  

Il existe des exemples de signaux SSTV à cette adresse afin de s'entrainer : [WIKI_SSTV](https://www.sigidwiki.com/wiki/Slow-Scan_Television_(SSTV))

*Fait interessant :*      
il existe un programme nommé **ARISS SSTV** (Amateur Radio on the International Space Station) precedemment SAREX crée en 2011, qui transmet des images depuis l'ISS (Station Spacial International) : 
Le mode utilisé pour recevoir les images SSTV de la station est le mode **PD120**. La reception d’une image prend environ 2 minutes sur la fréquence **145.800 Mhz**   
Nous pouvons aussi uplaoder les images que nous avons recus : [spaceflightsoftwaret](https://www.spaceflightsoftware.com/ARISS_SSTV/index.php)


Challenge très intéressant de la plateforme [Root-me](https://www.root-me.org/) :     
[Root-me challenge RF - Transmission Satellite](https://www.root-me.org/fr/Challenges/Reseau/RF-Transmission-satellite)

*Comment faire : *   
Dans un premier temps il est neccessaire de savoir où se situe la Station Spatial International : 
- [Orbitron](http://blog.f1src.org/page/orbitron.htm) (Software)
- [n2yo](https://www.n2yo.com/?s=25544&df=1) (WebSite)
- [SpotTheStation](https://spotthestation.nasa.gov/tracking_map.cfm) 

il faut que l'ISS se trouve a proximité de notre antenne, pour commencer à recevoir le signal SSTV :     

Vidéo youtube : [ISS_SSTV_Receive](https://www.youtube.com/watch?v=QGnGE2c9io4)


## Tempest SDR [avec le HackRF ONE]

Tempest SDR est un [outil open source de Martin Marinov](https://github.com/martinmarinov/TempestSDR) qui permet, grace aux ondes émisent de façon non intentionelle, d'afficher le contenu d'un écran. Concretement : un cable HDMI émet des ondes 'garbage', celles ci peuvent être récuperées et grace à l'outil Tempest SDR, nous pouvons transformer ces ondes en affichage en temps réel. 
[source](https://www.rtl-sdr.com/?s=tempestSDR)

![SDR](https://user-images.githubusercontent.com/39098396/125200291-b7068280-e26a-11eb-8c93-a0ac73c8bc2e.jpeg)

Il faut dans un premier temps connaître sur quelle fréquence radio notre port HDMI envoie son 'garbage' : 
nous pouvons la déterminer sur GQRX en faisant des tests de branchements / debranchements du cable. 
Je détermine donc que la 'fréquence d'émission' de mon cable est : 198 000 000 khz 

![GQRX](https://user-images.githubusercontent.com/39098396/125199684-a1dc2480-e267-11eb-9803-69e3d6367335.png)

Je branche mon hackRF, je pointe l'antenne vers le port HDMI de mon écran, puis je lance l'outil en écoutant sur la bonne fréquence, je dois aussi connaitre la résolution et le taux de rafraichissement de l'écran cible, et voila :    

![TempestSDR](https://user-images.githubusercontent.com/39098396/125199259-a9023300-e265-11eb-867c-96a8a2418b7f.PNG)   
petite [Vidéo](https://user-images.githubusercontent.com/39098396/125199892-9c330e80-e268-11eb-9fa8-df8dac52068b.mp4) demonstrative de l'outil et de ce qu'on peut faire avec 


## GPS Spoofing via HackRF One 

Le GPS Spoofing est une technique qui consiste à envoyer de fausses informations à un systeme GPS afin d'afficher la mauvaise position. 

__Fonctionnement d'un GPS :__ Un recepteur GPS fonctionne grace au calcul de la distance entre sont capteur et plusieurs satellites émeteurs (4) via des ondes, il determine et affiche sa position telle que nous la voyons sur un GPS classique.     

![Image](https://user-images.githubusercontent.com/39098396/129039739-0d750fc3-58a5-407b-bcb7-1504cb977551.png)

la fréquence de signal d'un GPS basique est de 1575,42MHZ

GPS-SDR-SIM (Compatible Linux et Windows) est un outil (disponible sur ce ![repo](https://github.com/osqzss/gps-sdr-sim)) permettant de mener ce genre d'action. 

### Démonstration : 

 - Dans un premier temps nous devons télécharger le fichier [BRDC du jour](https://cddis.nasa.gov/archive/gnss/data/daily/) (Broadcast Ephemeris Data) qui contient les posittions de chaque satelitte GPS.
 
__Petit Script qui automatise le process :__
```
#!/bin/bash

# Declaration des Variables 
day=$(date +%j)
year=$(date +%Y)
yr=$(date +%y)
RINEX_NAV_FILE="brdc${day}0.${yr}n"

# Téléchargement & extraction du fichier 
wget -q ftps://gdc.cddis.eosdis.nasa.gov/pub/gps/data/daily/$year/brdc/$RINEX_NAV_FILE.gz -O brdc_file`date +%d_%m_%y`.gz
gunzip brdc_file`date +%d_%m_%y`.gz

echo -e " le fichier \033[32m"brdc_file`date +%d_%m_%y` "\033[00mest extrait"
```

- Trouver une position gps et avoir les infos suivantes : (Latitude,Longitude,hauteur)

- Puis générer le fichier gpssim.bin executant cette commande en passant comme argument les positions GPS voulu :

```
$ ./gps-sdr-sim -e brdc_file -b 8 -l 48.859057,2.293276,30
```

```
Usage: gps-sdr-sim [options]
Options:
  -e <gps_nav>     RINEX navigation file for GPS ephemerides (required)
  -u <user_motion> User motion file (dynamic mode)
  -g <nmea_gga>    NMEA GGA stream (dynamic mode)
  -c <location>    ECEF X,Y,Z in meters (static mode) e.g. 3967283.15,1022538.18,4872414.48
  -l <location>    Lat,Lon,Hgt (static mode) e.g. 30.286502,120.032669,100
  -t <date,time>   Scenario start time YYYY/MM/DD,hh:mm:ss
  -T <date,time>   Overwrite TOC and TOE to scenario start time
  -d <duration>    Duration [sec] (dynamic mode max: 300 static mode max: 86400)
  -o <output>      I/Q sampling data file (default: gpssim.bin ; use - for stdout)
  -s <frequency>   Sampling frequency [Hz] (default: 2600000)
  -b <iq_bits>     I/Q data format [1/8/16] (default: 16)
  -i               Disable ionospheric delay for spacecraft scenario
  -v               Show details about simulated channels
```

- Exécuter la commande suivante en branchant le HackRF

```
hackrf_transfer -t gpssim.bin -f 1575420000 -s 2600000 -a 1 -x 42
```

```
Usage: hackrf_transfer [OPTIONS] 
Options :
   -r <filename> # Receive data into file.
   -t <filename> # Transmit data from file.
   -w # Receive data into file with WAV header and automatic name.
   # This is for SDR# compatibility and may not work with other software.
   [-f set_freq_hz] # Set Freq in Hz
   [-a set_amp] # Set Amp 1=Enable, 0=Disable.
   [-l gain_db] # Set lna gain, 0-40dB, 8dB steps
   [-i gain_db] # Set vga(if) gain, 0-62dB, 2dB steps
   [-x gain_db] # Set TX vga gain, 0-47dB, 1dB steps
   [-s sample_rate_hz] # Set sample rate in Hz (8/10/12.5/16/20MHz)
   [-n num_samples] # Number of samples to transfer (default is unlimited).
   [-b baseband_filter_bw_hz] # Set baseband filter bandwidth in MHz.
       Possible values: 1.75/2.5/3.5/5/5.5/6/7/8/9/10/12/14/15/20/24/28MHz, default < sample_rate_hz.
```

Si nous exécutons un GQRX sur les fréquences GPS nous voyons bien les ondes que le HackRF envoie : 

![Image](https://user-images.githubusercontent.com/39098396/128635162-733e9233-48e7-4b09-9c0f-25af4831c5e2.png)   

Voici le résultat en vidéo : 
![Vidéo](https://user-images.githubusercontent.com/39098396/128639352-11dc3526-e1a2-4cf4-ae4e-3d4ed705c560.MOV)   

Cet outil possède une puissante de portée de plusieurs mètres.   
Cela fonctionne aussi sur Iphone et son application intégrée : __Plans__ : 

![Image](https://user-images.githubusercontent.com/39098396/128639774-73033997-319a-4aa2-9a6c-c1b1d208ea66.JPG)


### Attaque sur Drone (DJI Mavic Mini) :

Les drones possedent une sécurité GPS qui bloque les vols lorsque les utilisateurs se situent dans certaines Zones (Militaire, Aéroport, Centre Ville)

![Image2](https://user-images.githubusercontent.com/39098396/128742786-bf17a5ed-7cb3-461e-8665-d5c9fb075072.png)  

![Image](https://user-images.githubusercontent.com/39098396/128742210-63b51ac0-80b2-4621-8ba0-9b5edbb01256.jpeg)

Voici une map qui répertorie les endroits Autorisés / Interdits partout dans le monde : [Drone-Spot](https://www.drone-spot.tech)

Cependant, il faut savoir que le drone se fie au GPS du téléphone, ce qui veut donc dire que nous pouvons spoofer la positions GPS du téléphone afin de faire décoler notre drone dans des endroits non legitime. 

Exemple :  49.199544,5.470661,0

Mais qu'en est t'il d'un spoofing d'endroits 'NO FLY ZONE' lors d'un vol de drone dans un endoit autorisé : 
Dans ce cas, je vais simuler un vol au dessus d'un aéroport pendant un vol légitime : 

la position va se changer et le message suivant apparait : (le pilote dispose de 90 secondes pour partir de la NON FLY ZONE) sinon le drone procedera à un aterissage automatique. 


![Image](https://user-images.githubusercontent.com/39098396/129611809-87c16fdb-399d-4334-a379-bdf492f5191e.PNG)

Puis impossible de faire redecoller le drone par la suite : 


![Image](https://user-images.githubusercontent.com/39098396/129612565-74461bb0-c93d-4657-abf9-27c13c2f7f5d.PNG)

