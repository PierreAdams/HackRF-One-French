# HackRF-One
Présentation et démonstration des fonctionnalités du HackRF One
# SDR 
Tout d'abord il est intéressant de savoir ce qu'est le SDR : 
Software Defined Radio c'est pour faire simple, une radio (recepteur/emeteur) remplacée logicielement  
__avantage__ : la puissance du pc permet de traiter les signaux  
 
# HackRF One  
le HackRF est un outil de SDR, crée par [Great Scott Gadget](https://greatscottgadgets.com)  
![Scan](https://user-images.githubusercontent.com/39098396/79736639-6d92e600-82fa-11ea-8e76-a5e6a3ea749a.jpeg)  
__prix__ : environ 300€ (Antenne comprise)  
__Reception et emission__ : de 10Mhz à 6Ghz

# PortaPack  
Le HackRf peut être integré dans un boite nomée un "PortaPack" [lien github](https://github.com/sharebraind/portapack-hackrf)  
![Scan](https://user-images.githubusercontent.com/39098396/79736658-72579a00-82fa-11ea-8692-116d2b8a5461.jpeg)  
__prix__ : 200 €  
le portapack permet d'avoir une interface graphique directement et d'effectuer des manips direct sur le hackrf One.  
Havoc est un firmware pour le portapack développé par le grand [furtek](https://github.com/furrtek/portapack-havoc)


--------------------------------------------------------------------


# Premier Branchement + Mise à jour du Firmware :  

La première chose à faire une fois le HackRF reçu, il faut mettre à jour le firmware et installer les paquets HackRf:   

``` apt install hackrf ``` 

Pour cela, se rendre sur le github de [Michael Ossmann](https://github.com/mossmann), l'un des menbres de Great Scott Gadget et le createur du Hackrf One.  
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

# Commande HackRF One : 

__Hackrf_info__ : lors du branchement du hackRF One, cette commande permet d'avoir des informations sur la version du hackRF One :  

__Hackrf_transfer__ : permet de capturer un transfère et de le restransmettre par la suite (exemple à venir)

__hackrf_debug__ : Commande de debug du hackRF One 

__hackrf_sweep__ : Commande qui va balayer un champ de fréquences et l'analyser 

__hackrf_spiflash__ : Mettre à jour le firmware 

__hackrf_cpldjtag__ : Mettre à jour le cpld


# Petit rappel utile - convertion MHz - Ghz 
![Scan](https://user-images.githubusercontent.com/39098396/79741752-109b2e00-8302-11ea-80d2-f7132bdcff0b.png)


# Pratique  

## Portail 

Passons maintenant à la pratique,   
pour se faire je vous conseile quelque logiciels de scan fréquence (sous linux bien sur) pas de Windob ici !  
[sdr angel](https://github.com/f4exb/sdrangel)  
[Spectrum Analyzer](https://github.com/pavsa/hackrf-spectrum-analyzer)  
[gqrx](https://gqrx.dk/)  

J'utilise gqrx que je trouve très pratique, simple d'utilisation et surtout francais (Cocorico).
Nous allons pour commencer essayer d'intercepter un signal d'une telecommand de portail :
(Photo Telecomand)
la première choses a faire est de trouver sur quelle fréquence la telecommande va communiquer avec le portail (en géneral 433 Mhz)
souvent indiquer soit sur la telecommande, soit sur le site du constructeur si c'est pas indiqué :
> internet est votre ami 

Avec gqrx on va pouvoir verifier et ajuster la fréquence (Assurez vous que le logiciel se base sur votre hackRF dans les parametres de gqrx)  
Une fois sur notre fréquence lorsque nous activons la télecommand nous appercevons bien un signal   

![Scan](https://user-images.githubusercontent.com/39098396/79851629-e0b55e80-83c5-11ea-8a63-2675dcfc38d9.png)

Nous avons donc maintenant une fréquence précise. (433.910000 Mhz)
Passons ensuite à l'enregistrement :   
``` hackrf_transfer -s 2 -f 433910000 -r open  ```   
provoquer le signal pendant l'enregistrement   
``` hackrf_transfer -s 2 -f 433910000 -t open -a 1 -x 24  ```   
puis réinvoquer le signal devant le portail pour l'ouvrir  
options  utilisées 
- s : 
- f : 
- r : Nom du fichier ou va etre stocké notre signal 
- t : Nom du fichier ou va etre lu notre signal (afin de le retransmettre)
- a : Amplifier le signal
- x : le gain entre 0 et 47 dB (en Décibel)

On peut crée un script pour plus de proffesionalisme : 

```
figlet Sesame, Ouvre toi ! 
hackrf_transfer -s 2 -f 433910000 -t open -a 1 -x 24
figlet C'est ouvert !  
```   
![Regarder la video](https://www.youtube.com/watch?v=HM1JgZUscY0)

## Talkie-Walkie : 

C'est exactement le même procédé nous trouvons la fréquence, capturons le signal, le stockons et le rediffusons à notre guise. 

Dans mon cas, je capte le signal sur la fréquence 446 Mhz 
je capture une séquence d'apell par exemple  : 
```hackrf_transfer -s 2 -f 446000000 -r HackyWalkie```  
et je la redifusse !     
```hackrf_transfer -s 2 -f 446000000 -r HackyWalkie```   

![Regarder la Vidéo](https://www.youtube.com/watch?v=4-qAzXwfjRY)


