# TP 7 - Boot, services et processus / Tâches d’administration
## Kaplan Kubilay
# Exercice 1. Personnalisation de GRUB
GRUB est considérablement paramétrable : résolution, langue, fond d’écran, thème, disposition du clavier….
 GRUB se configure via un fichier de paramètres (/etc/default/grub), mais aussi par des
scripts situés dans /etc/grub.d ; ces scripts commencent tous par un numéro et sont traités dans
l’ordre.
 Evidemment, seuls les scripts exécutables sont pris en compte.
 Sous Ubuntu Server, GRUB prend aussi en compte les fichiers d’extension .cfg présents
dans /etc/default/grub.d. En particulier, sur les versions récentes, le fichier de configuration
50-curtin-settings.cfg donne à la variable GRUB_TERMINAL la valeur console, ce qui désactive
tous les paramètres liés aux fonds d’écran, thèmes, certaines résolutions, etc.
1. Commencez par changer l’extension du fichier /etc/default/grub.d/50-curtin-settings.cfg s’il
est présent dans votre environnement (vous pouvez aussi commenter son contenu).
```
Il n'est pas présent
```
2. Modifiez le fichier /etc/default/grub pour que le menu de GRUB s’affiche pendant 10 secondes ;
passé ce délai, le premier OS du menu doit être lancé automatiquement.
```
sudo nano /etc/default/grub

GRUB_TIMEOUT_STYLE=menu
GRUB_TIMEOUT=10
```
3. Lancez la commande update-grub
”final” de GRUB (/boot/grub/grub.cfg) à partir du fichier de paramètres et des scripts.
```
sudo update-grub
```
4. Redémarrez votre VM pour valider que les changements ont bien été pris en compte
 Pensez à lancer la commande update-grub après chaque modification de la configuration de
GRUB !
```
Changement validé 
```
5. On va augmenter la résolution de GRUB et de notre VM. Cherchez sur Internet le ou les paramètres
à rajouter au fichier grub.
```
GRUB_GFXMODE=1152x864
GRUB_GFXPAYLOAD_LINUX=keep
sudo update-grub
```
6. On va à présent ajouter un fond d’écran. Il existe un paquet en proposant quelques uns : grub2-splashimages
(après installation, celles-ci sont disponibles dans /usr/share/images/grub).
```
sudo apt install grub2-splashimages -y
sudo nano /etc/default/grub
GRUB_BACKGROUND="/usr/share/images/grub/Plasma-lamp.tga"
sudo update-grub
```
7. Il est également possible de configurer des thèmes. On en trouve quelques uns dans les dépôts (grub2-themes-*).
Installez-en un.
```
sudo apt install grub2-themes-ubuntu-mate -y
sudo nano /etc/default/grub
GRUB_THEME="/boot/grub/themes/ubuntu-mate/theme.txt"
sudo update-grub
```
8. Ajoutez une entrée permettant d’arrêter la machine, et une autre permettant de la redémarrer.
```
sudo nano /etc/grub.d/40_custom 
    menuentry 'Arrêt du système' {
        halt
    }
    menuentry 'Redémarrage du système' {
        reboot
    }
sudo update-grub
```
9. Configurez GRUB pour que le clavier soit en français ( n’a plus l’air de fonctionner sur Ubuntu ≥
19.04)
```
sudo mkdir /boot/grub/layouts
sudo grub-kbdcomp -o /boot/grub/layouts/fr.gkb fr
sudo nano /etc/default/grub
    GRUB_TERMINAL_INPUT=at_keyboard
sudo nano /etc/grub.d/40_custom
    # Clavier fr
    insmod keylayouts
    keymap fr
sudo update-grub
```
## Exercice 2. Noyau
Dans cet exercice, on va créer et installer un module pour le noyau.
1. Commencez par installer le paquet build-essential, qui contient tous les outils nécessaires (compilateurs,
bibliothèques) à la compilation de programmes en C (entre autres).
```
sudo apt install build-essential -y
```
2. Créez un fichier hello.c contenant le code suivant :
```c++
mkdir noyauC
touch hello.c

 #include <linux/module.h>
 #include <linux/kernel.h>

 MODULE_LICENSE("GPL");
 MODULE_AUTHOR("John Doe");
 MODULE_DESCRIPTION("Module hello world");
 MODULE_VERSION("Version 1.00");

 int init_module(void)
 {
 printk(KERN_INFO "[Hello world] - La fonction init_module() est appelée.\n");
 return 0;
 }

 void cleanup_module(void)
 {
 printk(KERN_INFO "[Hello world] - La fonction cleanup_module() est appelée.\n");
 }
```
3. Créez également un fichier Makefile :
```makefile
obj -m += hello.o

all:
    make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

clean:
    make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean

install:
    cp ./hello.ko /lib/modules/$(shell uname -r)/kernel/drivers/misc
```
 Les lignes 4, 7 et 10 doivent commencer par une tabulation.
4. Compilez le module à l’aide de la commande make, puis installez-le à l’aide de la commande make
install.
 Le module est installé dans le dossier spécifié à la ligne 10.
```
kubilay@serveur:~/noyauC$ make
make -C /lib/modules/5.4.0-88-generic/build M=/home/kubilay/noyauC modules
make[1]: Entering directory '/usr/src/linux-headers-5.4.0-88-generic'
  CC [M]  /home/kubilay/noyauC/hello.o
  Building modules, stage 2.
  MODPOST 1 modules
  CC [M]  /home/kubilay/noyauC/hello.mod.o
  LD [M]  /home/kubilay/noyauC/hello.ko
make[1]: Leaving directory '/usr/src/linux-headers-5.4.0-88-generic'
kubilay@serveur:~/noyauC$ sudo make install
[sudo] password for kubilay: 
cp ./hello.ko /lib/modules/5.4.0-88-generic/kernel/drivers/misc
```
5. Chargez le module ; vérifiez dans le journal du noyau que le message “La fonction init_module() est
appelée” a bien été inscrit, synonyme que le module a été chargé ; confirmez avec la commande lsmod.
```
Avec dépendances :
    sudo depmod -a
    sudo modprobe -a hello
    sudo modprobe -r hello
    grep Hello /var/log/syslog
Sans dépendances :
    insmod
```
6. Utilisez la commande modinfo pour obtenir des informations sur le module hello.ko ; vous devriez
notamment voir les informations figurant dans le fichier C.
```
kubilay@serveur:~$ modinfo hello
filename:       /lib/modules/5.4.0-88-generic/kernel/drivers/misc/hello.ko
version:        Version 1.00
description:    Module hello world
author:         John Doe
license:        GPL
srcversion:     4398A2271F215E3A6F58078
depends:
retpoline:      Y
name:           hello
vermagic:       5.4.0-88-generic SMP mod_unload modversions
```
7. Déchargez le module ; vérifiez dans le journal du noyau que le message “La fonction cleanup_module()
est appelée” a bien été inscrit, synonyme que le module a été déchargé ; confirmez avec la commande
lsmod.
```
sudo modprobe -r hello
grep Hello /var/log/syslog
```
8. Pour que le module soit chargé automatiquement au démarrage du système, il faut l’inscrire dans le
fichier /etc/modules. Essayez, et vérifiez avec la commande lsmod après redémarrage de la machine.
```
sudo nano /etc/modules
On ajoute :
    hello
```
## Exercice 3. Interception de signaux
La commande interne trap permet de redéfinir des gestionnaires pour les signaux reçus par un processus.
Un cas d’utilisation typique est la suppression des fichiers temporaires créés par un script lorsque celui-ci est
interrompu.
1. Commencez par écrire un script qui recopie dans un fichier tmp.txt chaque ligne saisie au clavier par
l’utilisateur
```sh
nano scriptTMP.sh
    while :; do
    read text
    echo $text >> tmp.txt
    done

chmod u+x scriptTMP.sh
```
2. Lancez votre script et appuyez sur CTRL+Z. Que se passe-t-il ? Comment faire pour que le script poursuive
son exécution ?
```
./scriptTMP.sh
^Z
[3]+  Stopped                 ./scriptTMP.sh

kubilay@serveur:~$ bg
[4]+ ./scriptTMP.sh &
```
3. Toujours pendant l’exécution du script, appuyez sur CTRL+C. Que se passe-t-il ?
```
Le processus s'arrete.
```
4. Modifiez votre script pour redéfinir les actions à effectuer quand le script reçoit les signaux SIGTSTP
(= CTRL+Z) et SIGINT (= CTRL+C) : dans le premier cas, il doit afficher “Impossible de me placer en
arrière-plan”, et dans le second cas, il doit afficher “OK, je fais un peu de ménage avant” avant de
supprimer le fichier temporaire et terminer le script.
```
nano scriptTMP.sh
    trap 'echo "impossible de me placer en arrière-plan "' SIGTSTP
    trap 'echo "OK, je fais juste un peu de ménage avant"; exit' SIGINT
    trap 'rm tmp.txt' EXIT
```
5. Testez le nouveau comportement de votre script en utilisant d’une part les raccourcis clavier, d’autre
part la commande kill
```
pgrep scriptTMP.sh
    3356
    3595
kill 3356
```
6. Relancez votre script et faites immédiatement un CTRL+C : vous obtenez un message d’erreur vous
indiquant que le fichier tmp.txt n’existe pas. A l’aide de la commande interne test, corrigez votre
script pour que ce message n’apparaisse plus.
```

```
## Exercice 4. Surveillance de l’activité du système
1. Lancez la commande htop, puis ouvrez un second terminal (avec Alt + F2, ou Alt + F3...) et tapez la
commande w dans tty2. Qu’affiche cette commande ?
```
htop => Visualiser et gérer les processus lancés
w
```
2. Comment afficher l’historique des dernières connexions à la machine ?
```
last
```
3. Quelle commande permet d’obtenir la version du noyau ?
```
uname -r 
    5.4.0-88-generic
```
4. Comment récupérer toutes les informations sur le processeur, au format JSON ?
```
lshw -class Processor -json
```
5. Comment obtenir la liste des derniers démarrages de la machine avec la commande journalctl ?
Comment afficher tout ce qu’il s’est passé sur la machine lors de l’avant-dernier boot ?
```
journalctl --list-boots
journalctl -b -1
```
6. Faites en sortes que lors d’une connexion à la machine, les utilisateurs soient prévenus par un message
à l’écran d’une maintenance le 26 mars à minuit.
```
sudo nano /etc/motd :
    "maintenance le 26 mars à minuit"
```
7. Ecrivez un script bash qui permet de calculer le k-ième nombre de Fibonacci : Fk = Fk−1 + Fk−2,
avec F0 = F1 = 1. Lancez le calcul de F100 puis lancez la commande tload depuis un autre terminal
virtuel. Que constatez-vous ? Interrompez ensuite le calcul avec CTRL+C et observez la conséquence sur
l’affichage de tload.
```bash
nano fibonacci.sh

    #!/bin/bash
    N=6

    a=0

    b=1

    read -p "Entrer un nombre : " N

    for (( i=0; i<N; i++ ))
    do
        echo -n "$a "
        fn=$((a + b))
        a=$b
        b=$fn
    done

chmod u+x fibonacci.sh

On voit que la charge augmente notamment grace au graphique générer par tload.
```