
# tp-3-Kaplan-Kubilay
## Exercice 1. Commandes de base
1. Commencez par mettre à jour votre système avec les commandes vues dans le cours.  
```
sudo apt-get update && sudo apt-get upgrade -y
```
2. Créez un alias “maj” de la ou des commande(s) de la question précédente. Où faut-il enregistrer cet
alias pour qu’il ne soit pas perdu au prochain redémarrage ?
```
alias maj="sudo apt-get update && sudo apt-get upgrade -y"  
```
On l'ajoute a la fin de ``.bashrc`` pour qu'il ne soit pas perdu au prochain redémarrage.  
Puis on utilise ``source ~/.bashrc`` pour mettre a jour le fichier.
3. Utilisez le fichier /var/log/dpkg.log pour obtenir les 5 derniers paquets installés sur votre machine.
```
kubilay@serveur:~$ tail -n 5 /var/log/dpkg.log
2021-09-27 21:56:44 status half-configured ca-certificates:all 20210119~20.04.2
2021-09-27 21:56:45 status installed ca-certificates:all 20210119~20.04.2
2021-09-27 21:56:45 trigproc initramfs-tools:all 0.136ubuntu6.6 <none>
2021-09-27 21:56:45 status half-configured initramfs-tools:all 0.136ubuntu6.6
2021-09-27 21:57:09 status installed initramfs-tools:all 0.136ubuntu6.6
```
4. Listez les derniers paquets qui ont été installés explicitement avec la commande apt install
```
apt list --installed 
```
5. Utilisez les commandes dpkg et apt pour compter de deux manières différentes le nombre de total de
paquets installés sur la machine (ne pas hésiter à consulter le manuel !). Comment explique-t-on la
(petite) différence de comptage ? Pourquoi ne peut-on pas utiliser directement le fichier dpkg.log ?
```
dpkg -l | grep "^ii" | wc -l
 Le resultat est 579
apt list --installed | wc -l
 Le resultat est 580
```
La difference est du au faite que pour apt on compte une ligne en plus qui n'a pas de rapport avec les paquets mais qui est pourtant presente.

6. Combien de paquets sont disponibles en téléchargement sur les dépôts Ubuntu ?
```
apt list | wc -l
 Le resultat est 66458
```
7. A quoi servent les paquets glances, tldr et hollywood ? Installez-les et testez-les.
```
sudo apt install glances tldr hollywood -y

Glances : est un logiciel de supervision système dont l'objectif est de nous offrir une vue synthétique mais néanmoins complète de l'état de la machines.
TLDR : fournit des résumés simplifiés de l’utilisation des commandes sous Linux.
Hollywood : conçu dans le but de faire comme les faux hacker à Hollywood en splittant la console en plusieurs panneaux.
```
8. Quels paquets proposent de jouer au sudoku ?
```
A l'aide de la commande apt list | grep "sudoku" on troue :  
gnome-sudoku
ksudoku
sudoku
```
## Exercice 2.
A partir de quel paquet est installée la commande ls ? Comment obtenir cette information en une
seule commande, pour n’importe quel programme ? Utilisez la réponse à cette question pour écrire un
script appelé origine-commande (sans l’extension .sh) prenant en argument le nom d’une commande, et
indiquant quel paquet l’a installée.
```
which -a ls | tail -1 | xargs dpkg -S
 Donne : coreutils: /bin/ls
```
Le Script :
```bash
#!/bin/bash
echo $(which -a ls | tail -1 | xargs dpkg -S)
```
## Exercice 3.
Ecrire une commande qui affiche “INSTALLÉ” ou “NON INSTALLÉ” selon le nom et le statut du package
spécifié dans cette commande.
```
dpkg -l "coreutils" | grep "ii" > /dev/null && echo "installé" || echo "non installé"
```
## Exercice 4.
Lister les programmes livrés avec coreutils. En particulier, on remarque que l’un deux se nomme [. De
quoi s’agit-il ?  
- Pour lister les programmes livré avec coreutils et voir leur emplacement:
```
dpkg -L coreutils
```
La commande ``[`` a le meme fontionnement que le programme ``test``. Il permet donc de fair un test des instruction entré entre crochets.
## Exercice 5. aptitude
Installez les paquets emacs et lynx à l’aide de la version graphique d’aptitude (et prenez deux minutes
pour vous renseigner et tester ces paquets).
```
apt install aptitude
```
## Exercice 6. Installation d’un paquet par PPA
2. Vérifiez qu’un nouveau fichier a été créé dans /etc/apt/sources.list.d. Que contient-il ?
```
linuxuprising-ubuntu-java-focal.list
```
C'est le depot
## Exercice 7. Installation d’un logiciel à partir du code source
1. Commencez par cloner le dépôt git suivant :
```
git clone https://gitlab.com/jallbrit/cbonsai
```
2. Rendez vous dans le dossier cbonsai. Un fichier README.md) est livré avec les sources, et vous explique
comment compiler le programme (vous pouvez installer un lecteur de Markdown pour Bash, comme
mdless pour vous faciliter la lecture de ce type de fichiers).
```
sudo apt install ruby
sudo gem install mdless
mdless README.md
```
Un fichier Makefile est également présent. Un Makefile est un fichier utilisé par l’outil make, et
contient toutes les directives de compilation d’un logiciel. Un Makefile définit un certain nombre de
règles permettant de construire des cibles. Les cibles les plus communes étant install (pour la compilation
et l’installation du logiciel) et clean (pour sa suppression).
En suivant les consignes du fichier README.md, et en installant les éventuels paquets manquants, compilez
ce programme et installez le en local.
```
sudo apt install libncursesw5-dev 
sudo apt install make gcc pkg-config scdoc
cd cbonsai
make install PREFIX=~/.local
```
3. Malheureusement, cette installation “à la main” fait qu’on ne dispose pas des bénéfices de la gestion
de paquets apportés par dpkg ou apt. Heureusement, il est possible de transformer un logiciel installé
“à la main” en un paquet, et de le gérer ensuite avec apt ; c’est ce que permet par exemple l’outil
checkinstall.
```
sudo checkinstall cbonsai
```
4. Recommencez la compilation à l’aide de checkinstall :
sudo checkinstall
Un paquet a été créé (fichier xxx.deb), et le logiciel est à présent installé (tapez cbonsai depuis n’importe
quel dossier pour vous en assurer) ; on peut vérifier par exemple avec aptitude qu’il provient bien du paquet
qu’on a créé avec checkinstall.  
Vous pouvez à présent profiter d’un instant de zenitude avant de passer au dernier exercice.
## Exercice 8. Création de dépôt personnalisé
1. Dans le dossier scripts créé lors du TP 2, créez un sous-dossier origine-commande où vous créerez un
sous-dossier DEBIAN, ainsi que l’arborescence usr/local/bin où vous placerez le script écrit à l’exercice
2
```
cd script
mkdir -p origine-commande/DEBIAN
cd origine-commande
mkdir -p usr/local/bin
cp origine-commande ~/script/origine-commande/usr/local/bin/
```
2. Dans le dossier DEBIAN, créez un fichier control avec les champs suivants :
3. Revenez dans le dossier parent de origine-commande (normalement, c’est votre $HOME) et tapez la
commande suivante pour construire le paquet :
```
dpkg-deb --build script/origine-commande
```
# Création du dépôt personnel avec reprepro

1. Dans votre dossier personnel, commencez par créer un dossier repo-cpe. Ce sera la racine de votre
dépôt
```
mkdir repo-cpe
```
2. Ajoutez-y deux sous-dossiers : conf (qui contiendra la configuration du dépôt) et packages (qui contiendra
nos paquets)
```
mkdir conf
mkdir packages
```
3. Dans conf, créez le fichier distributions suivant :
```
Origin: Un nom, une URL, ou tout texte expliquant la provenance du dépôt
Label: Nom du dépôt
// Suite: stable
Codename: focal #!! A MODIFIER selon la distribution cible !!
Architectures: i386 amd64 #(architectures cibles)
Components: universe #(correspond à notre cas)
Description: Une description du dépôt.
```
4. Dans le dossier repo-cpe, générez l’arborescence du dépôt avec la commande
```
sudo apt install reprepro
reprepro -b . export
```
5. Copiez le paquet origine-commande.deb créé précédemment dans le dossier packages du dépôt, puis,
à la racine du dépôt, exécutez la commande
```
cp origine-commande.deb ~/repo-cpe/packages/
cp origine-commande.deb ~/repo-cpe/
reprepro -b . includedeb focal origine-commande.deb
```
6. Il faut à présent indiquer à apt qu’il existe un nouveau dépôt dans lequel il peut trouver des logiciels.
Pour cela, créez (avec sudo) dans le dossier /etc/apt/sources.list.d le fichier repo-cpe.list
contenant :
```
sudo -i
echo deb file:/home/kubilay/repo-cpe focal multiverse > /etc/apt/sources.list.d/repo-cpe.list
logout
```
Signature du dépôt avec GPG
1. Commencez par créer une nouvelle paire de clés avec la commande
```
gpg --gen-key
```
2. Ajoutez à la configuration du dépôt (fichier distributions la ligne suivante :
```
SignWith: yes
```
3. Ajoutez la clé à votre dépôt :
```
reprepro --ask-passphrase -b . export
```
4. Ajoutez votre clé publique à votre dépôt avec la commande
```
gpg --export -a "auteur" > public.key
```
5. Enfin, ajoutez cette clé à la liste des clés fiables connues de apt :
```
sudo apt-key add public.key
```