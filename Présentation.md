# HackRF-One
Présentation et démonstration des fonctionnalité du HackRF One
# SDR 
Tout d'abord il est intéressant de savoir ce qu'est le SDR : 
Software Defined Radio c'est pour faire simple une radio (recepteur/emeteur) remplacé logicielement  
__avantage__ : la puissance du pc permet de traiter les signaux  
 
# HackRF One  
le HackRF est un outils de SDR, crée par [Great Scott Gadget](https://greatscottgadgets.com)  
![Scan](https://user-images.githubusercontent.com/39098396/79736639-6d92e600-82fa-11ea-8e76-a5e6a3ea749a.jpeg)  
__prix__ : environ 300€ (Antenne comprise)  
__Reception et emission__ : de 10Mhz à 6Ghz

# PortaPack  
Le HackRf peut etre integré dans un boite nomée un "PortaPack" [lien github](https://github.com/sharebraind/portapack-hackrf)  
![Scan](https://user-images.githubusercontent.com/39098396/79736658-72579a00-82fa-11ea-8692-116d2b8a5461.jpeg)  
__prix__ : 200 €  
le portapack permet d'avoir une interface graphique directement et d'effectuer des manip direct sur le hackrf One  
Havoc est un firmware pour le portapack devellopé par le grand [furtek](https://github.com/furrtek/portapack-havoc)


--------------------------------------------------------------------


# Premier Branchement + Mise à jour du Firmware :  

La première chose à faire une fois le HackRF reçu, il faut mettre à jour le firmware :   

Pour cela, se rendre sur le github de [Michael Ossmann](https://github.com/mossmann), l'un des menbres de Great Scott Gadget.  
Télecharger son [Repo HackRF](https://github.com/mossmann/hackrf)  

pour installer l'image standard du firmware :  
__hackrf_spiflash -w hackrf_one_usb.bin__

Pour mettre a jour le CPLD (Complex Programmable Logic Device)   
__hackrf_cpldjtag -x sgpio_if/default.xsvf__

pour verifier si la mise à jour à été appliqué :    
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

__Hackrf_transfer__ : permet de capturer un transfere et de le restransmettre par la suite (exemple à venir)

__hackrf_debug__ : Commande de debug du hackRF One 

__hackrf_sweep__ : Commande qui va balayer un champ de fréquences et l'analyser 

__hackrf_spiflash__ : Mettre à jour le firmware 

__hackrf_cpldjtag__ : Mettre à jour le cpld


# Petit rappel utile - convertion MHz - Ghz 
![Scan](https://user-images.githubusercontent.com/39098396/79741752-109b2e00-8302-11ea-80d2-f7132bdcff0b.png)


# Pratique  
Passons maintenant à la pratique,   
pour se faire je vous conseile quelque logiciels de scan fréquence (sous linux bien sur) pas de Windob ici !  
[sdr angel](https://github.com/f4exb/sdrangel)  
[Spectrum Analyzer](https://github.com/pavsa/hackrf-spectrum-analyzer)  
[gqrx](https://gqrx.dk/)  
