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

# Commande HackRF One : 

__Hackrf_info__ : lors du branchement du hackRF One, cette commande permet d'avoir des informations sur la versiion du hackRF One :  

![Scan](https://user-images.githubusercontent.com/39098396/79737911-53f29e00-82fc-11ea-8fd5-5651120f5441.png)  

__Hackrf_transfer__ : permet de capturer un transfere et de le restransmettre par la suite   
exemple photo

__hackrf_debug__ : permet de faire un debug du hackrf (à approfonfir) 

__hackrf_sweep__ : Commande qui va balayer un champ de frequence et l'analyser 

__hackrf_spiflash__ : 

__hackrf_cpldjtag__ : 
