# TP 1 - Installation d’Ubuntu Server et prise en main du shell
## Kaplan Kubilay

## Exercice 2. Prise en main de l’interpréteur de commandes ##
 A tout moment, vous pouvez sortir de la VM pour revenir dans le système hôte en appuyant sur la
touche CTRL (droite) du clavier.
- Manuel
1. A l’aide du manuel, identifiez le rôle de la commande which
```
il sert a retourner le chemin d'accès des commandes  
```
2. Quand on consulte une page du manuel, comment peut-on rechercher un terme (par exemple, chercher
le terme option dans la page de manuel de which ?
```
on utilise l'option "/" puis en mettant le terme voulu  
```
3. Comment quitte-t-on le manuel ?
```
Commande "q"  
```
4. Chaque section du manuel a une première page, qui présente le contenu de la section. Afficher la
première page de la section 6 ; de quoi parle cette section ?
```
man 6 intro  
```
- Navigation dans l’arborescence des fichiers
1. allez dans le dossier /var/log
```
cd /var/log  
```
2. remontez dans le dossier parent (/var) en utilisant un chemin relatif
```
cd ..
```  
3. retournez dans le dossier personnel
```
cd ~
```   
4. revenez au dossier précédent (/var) sans utiliser de chemin
```
cd -
```  
5. essayez d’accéder au dossier /root ; que se passe-t-il ?
```
Je n'ai pas la permission  
```
6. essayez la commande sudo cd /root ; que se passe-t-il ? Expliquez
```
Ne marche pas car "cd" n'est pas une commande( pas un executable)  
```
7. à partir de votre dossier personnel, créez l’arborescence suivante :
```
mkdir Dossier1 mkdir Dossier2 cd Dossier1 touch Fichier1 cd - cd Dossier2 mkdir Dossier2.1 mkdir Dossier2.2 cd Dossier2.2 touch Fichier2 touch Fichier3
```  
8. revenez dans votre dossier personnel ; à l’aide de la commande rm, essayez de supprimer Fichier1, puis
Dossier1 ; que se passe-t-il ?
```
cd ~  cd Dossier1 rm Fichier1  
On ne peut pas supprimer Dossier1 avec rm car c'est un dossier.  
```
9. quelle commande permet de supprimer un dossier ?
```
rm -r Dossier1
```  
10. que se passe-t-il quand on applique cette commande à Dossier2 ?
```
rm -r Dossier2  
Il est bien supprimer
```
11. comment supprimer en une seule commande Dossier2 et son contenu ?
```
rm -r  
```
- Commandes importantes
1. Quelle commande permet d’afficher l’heure ? A quoi sert la commande time ?
```
"date". time permet de donner le temps d'execution d'une certaine commande  
```
2. Dans votre dossier personnel, tapez successivement les commandes ls puis la ; que peut-on en déduire
sur les fichiers commençant par un point ?
```
La commande "la" affiche aussi les fichier cacher  
```
3. Où se situe le programme ls ?
```
/usr/bin/ls  
```
4. Essayez la commande ll. Existe-t-il une entrée de manuel pour cette commande ? Utilisez les commandes
alias ou alias pour en savoir plus sur la nature de cette commande.
```
La commance "ll" est un alias de "ls -alF" et n'a donc pas d'entrer dans le manuel  
```
5. Quelle commande permet d’afficher les fichiers contenus dans le dossier /bin ?
```
"ls"
```  
6. Que fait la commande ls .. ?
```
Elle affiche le contenu d'un repertoire 
``` 
7. Quelle commande donne le chemin complet du dossier courant ?
```
pwd
```   
8. Que fait la commande echo 'bip' > plop exécutée 2 fois ?
```
cat plop  
plop ne contient 'bip' qu'une seul fois 
``` 
9. Que fait la commande echo 'bip' >> plop exécutée 2 fois ?
```
cat plop  
plop contient bien 'bip' 2 fois  
```
10. Interprétez le comportement de la commande sleep 10 | echo 'toto' ?
```
Elle retourne 'toto' et suspend l'exécution du processus pendant 10 secondes  
```
11. A quoi sert la commande file ? Essayez la sur des fichiers de types différents.
```
détermine le type d’un fichier (indépendamment de son extension).  
```
12. Créez un fichier original qui contient la chaîne Hello Toto ! ; créer ensuite un lien lien_phy vers
ce fichier avec la commande ln original lien_phy. Modifiez à présent le contenu de original et
affichez le contenu de lien_phy : qu’observe-t-on ? Supprimez le fichier original ; quelle conséquence
cela a-t-il sur lien_phy ?
```
touch original   
echo 'Hello Toto !' > original  
cat original  
ln original lien_phy   
cat lien_phy 
nano original on change pour 'Hello emile !'  
cat lien_phy donne 'Hello emile !' Donc le contenu de lien_phy a changer automatiquement  
En suppriment 'original' 'lien_phy' n'a pas aussi été supprimer et a meme garder son contenu. 
```
13. Créez à présent un lien symbolique lien_sym sur lien_phy avec la commande ln -s lien_phy lien_sym.
Modifiez le contenu de lien_phy ; quelle conséquence pour lien_sym ? Et inversement ? Supprimez le
fichier lien_phy ; quelle conséquence cela a-t-il sur lien_sym ?
```
En modifiant lien_phy lien_sym a aussi été modifier et c'est pareil inversement.  
En supprimant 'lien_phy' le contenu de 'lien_sym' a été supprimer.
```
14. Affichez à l’écran le fichier /var/log/syslog. Quels raccourcis clavier permettent d’interrompre et
reprendre le défilement à l’écran ?
```
'ctrl s' pour l'interrompre et 'ctrl q' pour reprendre  
```
15. Affichez les 5 premières lignes du fichier /var/log/syslog, puis les 15 dernières, puis seulement les
lignes 10 à 20.
```
Pour 5 ligne: head -n 5 "syslog"  
Pour 15 derniere: tail -n 15 "syslog"
Ligne 10 a 20: sed -n '10,20p' syslog.
```
16. Que fait la commande dmesg | less ?
```
"dmesg" permet d'afficher les messages des buffer du kernel et "less" permet d'afficher du texte de manière successive.  
```
17. Affichez à l’écran le fichier /etc/passwd ; que contient-il ? Quelle commande permet d’afficher la page
de manuel de ce fichier ?
```
Le fichier /etc/passwd contient toutes les informations relatives aux utilisateurs (login, mots de passe, ...). Seul root doit pouvoir le modifier. man passwd 
``` 
18. Affichez seulement la première colonne triée par ordre alphabétique inverse
```
cut -d: -f1 passwd | sort -r  
```
19. Quelle commande nous donne le nombre d’utilisateurs ayant un compte sur cette machine (pas seulement
les utilisateurs connectés) ?
```
"ls" dans home affiche tout les utilisateurs  
```
20. Combien de pages de manuel comportent le mot-clé conversion dans leur description ?
```
```
21. A l’aide de la commande find, recherchez tous les fichiers se nommant passwd présents sur la machine
```
find . -name "passwd" a la racine  
```
22. Modifiez la commande précédente pour que la liste des fichiers trouvés soit enregistrée dans le fichier
~/list_passwd_files.txt et que les erreurs soient redirigées vers le fichier spécial /dev/null
```
find . -name "passwd" >> ~/list_passwd_files.txt 2>> /dev/null  
```
23. Dans votre dossier personnel, utilisez la commande grep pour chercher où est défini l’alias ll vu
précédemment
```
rgrep ll
```  
24. Utilisez la commande locate pour trouver le fichier history.log.
```
On doit d'abord installer mlocate "sudo apt install mlocate"  
locate history.log nous donne "/var/log/apt/history.log"  
```
25. Créer un fichier dans votre dossier personnel puis utilisez locate pour le trouver. Apparaît-il ? Pourquoi ?
```
mkdir dossier1  
locate dossier1 ne marche pas car c'est un dossier  
```
## Exercice 3. Découverte de l’éditeur de texte nano ##
Nano est un éditeur de texte rudimentaire, où toutes les commandes évidemment se font au clavier.
 Les raccourcis clavier les plus courants sont affichés en bas de l’écran, mais sous une forme peut-être
inhabituelle :
• ^G signifie Ctrl + G
• M-U signifie Alt + U
 Quelques raccourcis utiles :
F1 ou Ctrl + G Affichage de l’aide
Ctrl + X Quitter nano / Fermer une fenêtre / Exécuter une commande
Ctrl + R Ouvrir un fichier
Ctrl + O Enregistrer sous
Ctrl + S Enregistrer
Ctrl + K Couper
Ctrl + U Coller
Ctrl + W Rechercher
Ctrl + \ Remplacer
Ctrl + C Afficher des informations sur la position du curseur (numéro de ligne, de colonne)
Alt + U Annuler
Alt + E Refaire
1. Copiez le fichier /var/log/syslog dans votre dossier personnel sous le nom log.txt, puis ouvrez-le avec
nano
```
cp /var/log/syslog ~/log.txt  
nano log.txt  
```
2. Remplacez toutes les occurrences du mot kernel par le mot noyau
```
Ctrl + \ puis kernel pour remlacer par noyau puis All.  
```
3. Déplacer les 10 premières lignes à la fin du fichier
```
Ctrl Y pour aller en debut de fichier. Alt A puis prendre 10 ligne, Ctrl K puis avec Ctrl V pour descendre et enfin Ctrl U pour coller  
```
4. Annulez cette action
```
Alt U
```  
5. Enregistrez le fichier avant de quitter nano
```
Ctrl + X et yes pour save  
```
## Exercice 4. Personnalisation du shell ##
Le shell par défaut est plutôt austère, mais il existe de nombreux moyens de le personnaliser, en modifiant
le fichier ~/.bashrc.
1. Commencez par créer une copie de ce fichier, que vous appellerez .bashrc_bak
```
cp .bashrc ~/.bashrc_bak  
```
2. Editez le fichier .bashrc avec nano et décommentez la ligne force_color_prompt=yes pour activer
la couleur. Enregistrez le fichier et quittez nano.
```
nano .bashrc  
```
3.  Le fichier .bashrc est lu au démarrage du shell ; pour le recharger, il faudrait donc se déconnecter
puis se reconnecter ; mais il existe un autre moyen : la commande source .bashrc. Testez-la, l’invite
de commande devrait immédiatement passer en couleurs.
```
source .bashrc  
```
4.  Les couleurs par défauts (surtout celle du dossier courant) ne sont pas très visibles. Dans .bashrc,
cherchez les lignes commençant par PS1= ; elles indiquent la mise en forme de l’invite de commande
(selon que l’on est en couleurs ou non).
Sur cette ligne, on peut distinguer un certain nombre de raccourcis :
\u Nom de l’utilisateur
\h Nom de la machine (hôte)
\d Date
\t Heure avec les secondes
\A Heure sans les secondes
\w Chemin complet du dossier courant
Remarquez la séquence particulière : \[\033[1;32m]\u@\h\[\033[00m\] C’est cette instruction qui
indique d’affiche le nom de l’utilisateur et de la machine en vert clair. Plus précisément :
• un code couleur se place entre \[\033[ et \]
• on peut remplacer \033 par \e (ce n’est pas forcément toujours plus lisible…)
• un code couleur se termine toujours par la lettre m
• le code couleur 00 efface toute mise en forme
• on peut combiner différents attributs (couleur, souligné, clignotant, etc.) en les séparant par des
points virgules
 La page suivante vous donne les différents codes couleurs possibles : https://misc.flogisoft.com/
bash/tip_colors_and_formatting
Modifiez l’invite de commande pour qu’elle s’affiche sous la forme suivante :
[heure] - user@host:chemin_courant$
où l’heure est affichée en violet et entre crochets, et le chemin du dossier courant en cyan

