# HackRF-One
Présentation et démonstration des fonctionnalitées du HackRF One
# SDR 
Tout d'abord il est intéressant de savoir ce qu'est le SDR : 
Software Defined Radio c'est pour faire simple, une radio (recepteur/emeteur) remplacée logicielement  
__Avantage__ : la puissance du pc permet de traiter les signaux  
 
# HackRF One  
le HackRF est un outil de SDR, crée par [Great Scott Gadget](https://greatscottgadgets.com)  
![Scan](https://user-images.githubusercontent.com/39098396/79736639-6d92e600-82fa-11ea-8e76-a5e6a3ea749a.jpeg)  
__Prix__ : environ 300€ (Antenne comprise)  
__Réception / Emission__ : de 10Mhz à 6Ghz

# PortaPack  
Le HackRf peut être integré dans un boite nomée un "PortaPack" [lien github](https://github.com/furrtek/portapack-havoc)  
![Scan](https://user-images.githubusercontent.com/39098396/79736658-72579a00-82fa-11ea-8692-116d2b8a5461.jpeg)  
__Prix__ : 200 €  
le portapack permet d'avoir une interface graphique directement et d'effectuer des manips sur le hackRF One.  
- le firmware officiel, développer par sharebrained : https://github.com/sharebrained/portapack-hackrf/  
- Havoc est un firmware __Non officiel__ pour le portapack développé par le grand [furrtek](https://github.com/furrtek/portapack-havoc)  

__Note :__ il existe des versions non officiels des portapacks pour hackRF One pour l'avoir testé, celui fonctionne tout aussi bien ! : [Lien Amazon](https://www.amazon.fr/TOOGOO-Panneau-Tactile-Portapack-lhorloge/dp/B07WHJ8WGX/ref=sr_1_fkmr1_2?__mk_fr_FR=%C3%85M%C3%85%C5%BD%C3%95%C3%91&dchild=1&keywords=portapack+bogood&qid=1587725047&sr=8-2-fkmr1)  

Pour installer le firmware __Havoc__ sur Windows, une vidéo youtube trés claire à été réaliser sur le sujet :   
[Lien Youtube](https://www.youtube.com/watch?v=f0S9jWkRaQU.gif)

Lorsque le firmware de votre portapack sera installé il sera toujours possible d'activer le mode hackRF (sur pc) il suffit d'activer l'option __"HackRF Mode"__ dans le menu.  

![Scan](https://user-images.githubusercontent.com/39098396/80219795-42363100-8643-11ea-89bd-fab7d90ed604.PNG)  

--------------------------------------------------------------------


# Premier Branchement + Mise à jour du Firmware :  

La première chose à faire une fois le HackRF reçu, il faut mettre à jour le firmware et installer le paquet HackRF:   
installer le paquet hackrf :   
``` apt install hackrf ```   

Pour mettre à jour le firmware, se rendre sur le github de [Michael Ossmann](https://github.com/mossmann), l'un des menbres de Great Scott Gadget et le createur du Hackrf One.  
Télecharger son [Repo HackRF](https://github.com/mossmann/hackrf)  

pour installer l'image standard du firmware :  
__hackrf_spiflash -w hackrf_one_usb.bin__

Pour mettre à jour le CPLD (Complex Programmable Logic Device)   
__hackrf_cpldjtag -x sgpio_if/default.xsvf__

Verifier si la mise à jour à été appliquée :    
__hackrf_info__
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

# Commandes HackRF One : 

__Hackrf_info__ : lors du branchement du hackRF One, cette commande permet d'avoir des informations sur sa version   

__Hackrf_transfer__ : permet de capturer un transfère et de le restransmettre par la suite

__hackrf_debug__ : Commande de debug du hackRF One 

__hackrf_sweep__ : Commande qui va balayer un champ de fréquences et l'analyser 

__hackrf_spiflash__ : Mettre à jour le firmware 

__hackrf_cpldjtag__ : Mettre à jour le cpld


# Petit rappel utile - Convertion MHz - Ghz 
![Scan](https://user-images.githubusercontent.com/39098396/79741752-109b2e00-8302-11ea-80d2-f7132bdcff0b.png)


# Pratique :  

## Portail :  

Passons maintenant à la pratique,   
pour se faire je vous conseile quelques logiciels de SDR sous linux!  
* [sdr angel](https://github.com/f4exb/sdrangel) 
* [Spectrum Analyzer](https://github.com/pavsa/hackrf-spectrum-analyzer)  
* [gqrx](https://gqrx.dk/)

J'utilise gqrx que je trouve très pratique, simple d'utilisation.
Nous allons pour commencer, essayer d'intercepter un signal d'une telecommande de portail :  
la première choses a faire est de trouver sur quelle fréquence la telecommande va communiquer avec le portail (en géneral 433 Mhz) souvent indiquer soit sur la telecommande, soit sur le site du constructeur si c'est pas indiqué :  
> internet est votre ami 

Avec gqrx on va pouvoir verifier et ajuster la fréquence (Assurez vous que le logiciel se base sur votre hackRF dans les parametres de gqrx)  
Une fois sur notre fréquence lorsque nous activons la télecommande nous appercevons bien le signal emis  

![Scan](https://user-images.githubusercontent.com/39098396/79851629-e0b55e80-83c5-11ea-8a63-2675dcfc38d9.png)

Nous avons donc maintenant une fréquence précise. (433.910000 Mhz)
Passons ensuite à l'enregistrement :   
``` hackrf_transfer -s 2 -f 433910000 -r open  ```   
provoquer le signal pendant l'enregistrement   
``` hackrf_transfer -s 2 -f 433910000 -t open -a 1 -x 24  ```   
puis réinvoquer le signal devant le portail pour l'ouvrir  
options  utilisées 
- s : précise le taux d'echantillons en MHz
- f : précise la fréquence exact enregistré ou diffusé
- r : Nom du fichier ou va être stocké notre signal 
- t : Nom du fichier ou va être lu notre signal (afin de le retransmettre)
- a : Amplifie le signal
- x : le gain entre 0 et 47 dB (en Décibel)

On peut créer un script pour plus de proffesionnalisme : 

```
figlet Sesame, Ouvre toi ! 
hackrf_transfer -s 2 -f 433910000 -t open -a 1 -x 24
figlet C'est ouvert !  
```   
[Regarder la video](https://www.youtube.com/watch?v=HM1JgZUscY0)

## Talkie-Walkie : 

C'est exactement le même procédé nous trouvons la fréquence, capturons le signal, le stockons et le rediffusons à notre guise. 

Dans mon cas, je capte le signal sur la fréquence 446 Mhz 
je capture une séquence d'apell par exemple  :   
```hackrf_transfer -s 2 -f 446000000 -r HackyWalkie```  
et la difuse    
```hackrf_transfer -s 2 -f 446000000 -r HackyWalkie```   

[Regarder la vidéo](https://www.youtube.com/watch?v=4-qAzXwfjRY)

# Pratique - Portapack :  

Avec le portapack, toutes les manipulations effectuées juste avant pourront desormais être réalisés directement sur le portapack. 

Voici le menu d'__Havoc__ :  

![Scan](https://user-images.githubusercontent.com/39098396/80214273-d8b22480-863a-11ea-9671-3e846ce77b4f.PNG)

Prenons par exemple, une lampe avec une télécommande Hertzienne, nous pouvons voir qu'elle communique avec la lampe sur la fréquence 433.920 Mhz    
![Scan](https://user-images.githubusercontent.com/39098396/80212189-55430400-8637-11ea-9ea9-405f934ed19b.JPG)

Sur le portapack, dans l'onglet "Capture" nous allons régler la fréquence sur 433.9 Mhz, écouter le signal puis l'enregistrer afin de le rediffuser par la suite. 

![Scan](https://user-images.githubusercontent.com/39098396/80214995-08adf780-863c-11ea-871c-abbaafd03fc4.PNG)  
![Scan](https://user-images.githubusercontent.com/39098396/80214019-6a6d6200-863a-11ea-8f5b-724de95f83ec.PNG)


Une fois le signal capturé : se rendre dans la partie "Replay", puis ouvrir le fichier contenant le sginal capturé à l'instant. (éventuellement régler la fréquence de diffusion)


![Scan](https://user-images.githubusercontent.com/39098396/80214776-b371e600-863b-11ea-8f68-dff7b8b85ac1.PNG)  
 
 __et hop le signal est rediffusé !!__ 

# Fonctionnalités Portapack : 

Le Wiki du Github de furrtek explique très bien toutes les options que nous offre le Portapack: https://github.com/furrtek/portapack-havoc/wiki

L'onglet __Play Dead__ est une sorte de barrière pour empecher les ignorants d'utiliser le portapack :  
l'erreur Firmware suivante apparait lorsqu'on rentre dans l'onglet : 

![Scan](https://user-images.githubusercontent.com/39098396/80584597-33b89280-8a12-11ea-81ae-130242bf418f.PNG)

Pour sortir, il faut entrer la combinaison prédefinit dans l'onglet : __Settings__ > __Play dead__    
Par defaut cette combinaison est :  __Haut-Bas-Gauche-Droite__ (Le bouton __Reset__ marche aussi ^^)  

L'onglet __"Search/CLose Call"__ permet d'identifier exactement la fréquence d'un signal, on précise un fenêtre, et on déclenche l'émetteur :   
![Scan](https://user-images.githubusercontent.com/39098396/80237787-9bab5980-865d-11ea-888f-cdbe48752591.PNG)  

L'onglet __Receivers__ permet de recevoir toutes sortes de fréquences :  

* __AD-B: Plane :__ système de surveillance pour le contrôle du trafic aérien (connaitre la position des avions)
* __ACARS :__ système de communication/surveillance entre les aéronef et les sations au sol
* __AIS :__  système d’échanges automatisés de messages entre navires par radio 
* __AFSk :__ audio frequency-shift keying,  conçue pour véhiculer la voix ou de la musique, par exemple une liaison téléphone ou radio.
* __AUDIO :__ Recpteur Audio capable de recevoir different mode de fréquences (AM et FM) ![lien Github sharebrained](https://github.com/sharebrained/portapack-hackrf/wiki/Audio-Modes)
* __ERT :__ Paquet contenant les données d'un compteur éléctrique/Gaz pour faciliter la collect de données.
* __POCSAG :__ Protocole de transmission radio utilisé pour les réseaux de radiomessagerie.
* __Radiosondes :__ 
* __TPMS :__ Tyre Pressure Monitoring System en anglais, Système de surveillance de la pression des pneumatiques.
* __APRS :__ Automatic Packet Reporting System est un système de radiocommunication numérique utilisé par les radioamateurs, qui permet le partage d'informations d'intérêt local.
* __DMR framing :__ Digital Mobile Radio, norme de radio numérique mobile ouverte et utilisée dans des produits commerciaux à travers le monde.
* __SigFox :__ Sigfox est un opérateur de télécommunications français communiquant sur la fréquence 868 MHz.
* __LoRa :__ LoRaWAN est un protocole de télécommunication permettant la communication à bas débit, par radio, d'objets à faible consommation électrique communiquant selon la technologie LoRa 
* __SSTV :__ Slow Scan Television, est une activité radioamateur qui vise à la transmission analogique d'images fixes à l'aide d'une bande passante réduite correspondant à celle de la parole.
* __TETRA framing :__ Terrestrial Trunked Radio, destinés aux équipes de sécurité : elle opere entre 380-400 MHz pour les services d’urgence et dans les bandes 410-430 MHz | 450-470 MHz | 870-880 MHz pour les applications civiles et privées.


L'onglet __Transmitters__ permet de transmettre toutes ces fréquences. 

Quelques sigles que l'on va retrouver lors de l'utilisation du hackRF One couplé du Portapack : 

* __LNA :__ Amplificateur à faible bruit.
* __AMP :__ Amplificateur.
* __VGA :__ Gain.


Info sur les Leds :  

![Scan](https://user-images.githubusercontent.com/39098396/80587157-59e03180-8a16-11ea-8317-6111b7c383ff.jpg)  

Chaque led à une couleur differente :


* __3V3, 1V8, RF :__  lorsque ces leds sont allumé, cela signifie que le hackrf est allimenté (par une batterie externe par exemple)  
* __USB :__ connexion USB à un ordinateur, la communication est donc possible via les commandes __hackrf..__  
* __TX :__  le hackRF est en train de transmettre   
* __RX :__  le hackRF est en train de recevoir   

* Le bouton __Reset__ : permet une remise à zero du Microcontrôleur
* Le bouton __ISP / DFU__ : permet de modifier les données en cas de probleme sur le HackRF


# SSTV (Slow Scan Television) : 

SSTV est une activité qui merite d'etre creusé tellement elle est interressante, comme vu récemment c'est un mode qui permet de recevoir / émettre des images, 
exemple : 
![Scan](https://user-images.githubusercontent.com/39098396/121325763-2c043680-c912-11eb-87e1-9b087fbd3d8a.jpeg)  


