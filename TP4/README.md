# tp-4-Kaplan Kubilay

## TP 4 - Utilisateurs, groupes et permissions
## Exercice 1. Gestion des utilisateurs et des groupes
1. Utilisez la commande groupadd pour créer deux groupes dev et infra
```
sudo groupadd dev
sudo groupadd infra
```
2. Créez ensuite 4 utilisateurs alice, bob, charlie, dave avec la commande useradd, en demandant la
création de leur dossier personnel et avec bash pour shell
```
sudo useradd alice -m --shell /bin/bash
sudo useradd bob -m --shell /bin/bash
sudo useradd dave -m --shell /bin/bash
sudo useradd charlie -m --shell /bin/bash
```
3. Ajoutez les utilisateurs dans les groupes créés :
- alice, bob, dave dans dev
- bob, charlie, dave dans infra
```
sudo usermod -a -G dev alice | sudo usermod -a -G dev,infra bob |  sudo usermod -a -G dev,infra dave | sudo usermod -a -G infra charlie 
```
4. Donnez deux moyens d’afficher les membres de infra
```
grep infra /etc/group | cut -d: -f4
cat /etc/group | grep infra  | cut -d: -f4
```
5. Faites de dev le groupe propriétaire des répertoires /home/alice et /home/bob et de infra le groupe
propriétaire de /home/charlie et /home/dave
```
sudo chgrp -R infra /home/charlie
sudo chgrp -R infra /home/dave
sudo chgrp -R dev /home/bob
sudo chgrp -R dev /home/alice
```
6. Remplacez le groupe primaire des utilisateurs :
- dev pour alice et bob
- infra pour charlie et dave
```
sudo usermod -g dev alice
sudo usermod -g dev bob
sudo usermod -g infra dave
sudo usermod -g infra charlie
```
7. Créez deux répertoires /home/dev et /home/infra pour le contenu commun aux membres de chaque
groupe, et mettez en place les permissions leur permettant d’écrire dans ces dossiers.
```
cd /home
sudo mkdir dev
sudo mkdir infra
----
sudo chgrp -R infra /home/infra
sudo chgrp -R dev /home/dev
sudo chmod -R g+w /home/dev
sudo chmod -R g+w /home/infra
```
8. Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer
ou supprimer ce fichier ?
```
sudo chmod +t /home/dev
sudo chmod +t /home/infra
```
9. Pouvez-vous ouvrir une session en tant que alice ? Pourquoi ?
```
 Il est impossible d'ouvire une session en tant que Alice vu qu'elle ne possède pas de mot de passe. L'utilisateur est inactif

kubilay@serveur:/home$ su alice
Password:
su: Authentication failure
```
10. Activez le compte de l’utilisateur alice et vérifiez que vous pouvez désormais vous connecter avec son
compte.
```
sudo passwd alice

kubilay@serveur:/home$ sudo passwd alice
New password:
Retype new password:
passwd: password updated successfully
kubilay@serveur:/home$ su alice
Password:
alice@serveur:/home$
```
11. Comment obtenir l’uid et le gid de alice ?
```
id u alice && id -g charlie
```
12. Quelle commande permet de retrouver l’utilisateur dont l’uid est 1003 ?
```
cat /etc/passwd | cut -d: -f1,3 | grep 1003
```
13. Quel est l’id du groupe dev ?
```
cat /etc/group | cut -d: -f1,3 | grep -w dev
```
14. Quel groupe a pour gid 1002 ? ( Rien n’empêche d’avoir un groupe dont le nom serait 1002...)
```
cat /etc/group | cut -d: -f1,3 | grep  1002
```
15. Retirez l’utilisateur charlie du groupe infra. Que se passe-t-il ? Expliquez.
```
sudo gpasswd -d charlie infra

Sa ne marche pas car on peut voir que charlie est toujours dans le groupe infra car c'est son unique groupe et un utilisateur doit au moins appartenir à un groupe.
```
16. Modifiez le compte de dave de sorte que :
— il expire au 1er juin 2021
— il faut changer de mot de passe avant 90 jours
— il faut attendre 5 jours pour modifier un mot de passe
— l’utilisateur est averti 14 jours avant l’expiration de son mot de passe
— le compte sera bloqué 30 jours après expiration du mot de passe
```
sudo chage -E 2021/06/01 dave
sudo chage -M 90 dave
sudo chage -m 5 dave
sudo chage -W 14 dave
sudo chage -I 30 dave

Donne :
kubilay@serveur:~$ sudo chage -l dave
Last password change                                    : Sep 29, 2021
Password expires                                        : Dec 28, 2021
Password inactive                                       : Jan 27, 2022
Account expires                                         : Jun 01, 2021
Minimum number of days between password change          : 5
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 14

```
17. Quel est l’interpréteur de commandes (Shell) de l’utilisateur root ?
```
cut -d: -f1,7 /etc/passwd | grep -w "root"

Donne :
root:/bin/bash
Donc c'est bash
```
18. Si vous regardez la liste des comptes présents sur la machine, vous verrez qu’il en existe un nommé
nobody. A quoi correspond-il ?
```
nobody est un compte d'utilisateur à qui aucun fichier n'appartient, qui n'est dans aucun groupe ayant des privilèges et dont les seules possibilités sont celles que tous les "autres utilisateurs" ont.
```
19. Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ?
Quelle commande permet de forcer sudo à oublier votre mot de passe ?
```
Après son exécution la commande sudo , le mot de passe durera 15 minutes par defaut en mémoire.

Pour l'oublier : sudo -k
```
## Exercice 2. Gestion des permissions
1. Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques
lignes de texte. Quels sont les droits sur test et fichier ?
```
mkdir test
cd test
touch fichier

ll test
Donne :
drwxrwxr-x  2 kubilay kubilay 4096 Oct  3 20:46 ./
drwxr-xr-x 15 kubilay kubilay 4096 Oct  3 20:45 ../
-rw-rw-r--  1 kubilay kubilay   16 Oct  3 20:46 fichier
```
2. Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en
tant que root. Conclusion ?
```
chmod 000 test/fichier

sudo cat fichier 
sudo nano fichier 

On arrive bien a l'afficher et le modifier.
```
3. Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo
Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un
fichier s’il existe déjà. Que peut-on dire au sujet des droits ?
```
sudo chmod 300 test/fichier
echo "echo Hello" > fichier

root a tout les droit sur tout les fichiers et dossiers.
```
4. Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez.
```
kubilay@serveur:~/test$ ./fichier
bash: ./fichier: Permission denied

Avec sudo :

kubilay@serveur:~/test$ sudo ./fichier
Hello

Je n'ai pas le droit de lecture donc je ne peux pas l'éxecuter.
```
5. Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le
contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ?
Rétablissez le droit en lecture sur test.
```
chmod -R u-r test

Je n'ai pas le droit de lecture donc je ne peux pas lister ou éxecuter.

chmod -R u+r test
```
6. Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au
répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit
en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. Que pouvezvous
déduire de toutes ces manipulations ?
```
chmod u-w nouveau
chmod u-w test

Donne :
 [ File 'nouveau' is unwritable ]

chmod u+w test
Donne :
 [ File 'nouveau' is unwritable ]
Mais nouveau est bien suprimable. Le droit d'écriture n'est pas nécessaire pour supprimer.
```
7. Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test.
Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en
lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?
```
chmod u-x test

Pour tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en lister le contenu. La permission est denied.

Il faut le droit d'éxecution pour pouvoir rentrer dans un repertoire. Sans celui-ci on ne peut donc rien faire.
```
8. Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui
à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire
test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des
droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd
..” ? Pouvez-vous donner une explication ?
```
chmod u+x test
chmod u-x ~/test

On ne peut ni modifier ni créér ni supprimer un fichier ni se deplacer dans ssrep ou de lister son contenu.

On est rester dans le dossier sur lequel on a plus les droits mais on ne peut plus le manipuler et si on sort de celui-ci on ne peut plus y retourner.
```
9. Rétablissez le droit en exécution du répertoire test. Attribuez au fichier fichier les droits suffisants
pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture.
```
chmod u+x test
chmod g+r fichier
```
10. Définissez un umask très restrictif qui interdit à quiconque à part vous l’accès en lecture ou en écriture,
ainsi que la traversée de vos répertoires. Testez sur un nouveau fichier et un nouveau répertoire.
```
umask 077
mkdir testUmask
touch testumask
-rw-------  1 kubilay kubilay    0 Oct  4 09:48 testumask
drwx------  2 kubilay kubilay    4096 Oct  4 09:48 testUmask/
```
11. Définissez un umask très permissif qui autorise tout le monde à lire vos fichiers et traverser vos répertoires,
mais n’autorise que vous à écrire. Testez sur un nouveau fichier et un nouveau répertoire.
```
umask 022
mkdir testUmask2
touch testumask2

-rw-r--r--  1 kubilay kubilay    0 Oct  4 10:10 testumask2
drwxr-xr-x  2 kubilay kubilay    4096 Oct  4 10:06 testUmask2/
```
12. Définissez un umask équilibré qui vous autorise un accès complet et autorise un accès en lecture aux
membres de votre groupe. Testez sur un nouveau fichier et un nouveau répertoire.
```
umask 037

drwxr-----  2 kubilay kubilay    4096 Oct  4 10:15 testUmask3/
-rw-r-----  1 kubilay kubilay    0 Oct  4 10:16 testumask
```
13. Transcrivez les commandes suivantes de la notation classique à la notation octale ou vice-versa (vous
pourrez vous aider de la commande stat pour valider vos réponses) :
- chmod u=rx,g=wx,o=r fic
```
chmod 534 fic
```
- chmod uo+w,g-rx fic en sachant que les droits initiaux de fic sont r--r-x---
```
chmod 602 fic
```
- chmod 653 fic en sachant que les droits initiaux de fic sont 711
```
chmod u-x,g+r,o+w fic
```
- chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x---
```
chmod 520 fic
```
14. Affichez les droits sur le programme passwd. Que remarquez-vous ? En affichant les droits du fichier
/etc/passwd, pouvez-vous justifier les permissions sur le programme passwd ?
```
stat -c %A /usr/bin/passwd

Donne :
-rwsr-xr-x

Ce s indique que ce programme est exécuté avec les droits de son propriétaire.

stat -c %A /etc/passwd

Donne :
-rw-r--r--

Seul le proprietaire du fichier peut modifier ces informations.
```
