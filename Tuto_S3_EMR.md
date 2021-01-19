# S3 et EMR : Création d'un compartiment de stockage S3 et instanciation d'un cluster EMR

## Etape 1 : Création d'une paire de clé

Une paire de clé est nécessaire pour s'identifier aux machines distantes d'AWS. Cette paire de clé sera notamment utile au moment de la connection au Master Node du cluster Spark créé via EMR. 

Pour créer une paire de clé, cela se passe dans l'onglet <font color="purple">***EC2***</font> ! 
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_1.png" width="600" />
</p>

Naviguez dans l'espace <font color="purple">***Paires de clés***</font> puis <font color="purple">***Créer une paire de clés***</font> :
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_2.png" width="600" />
</p>

Après avoir sélectionné le nom de fichier de votre choix et le format de fichier adéquat (*.pem* pour MAC OS et Linux et *.ppk* pour Windows), vous pouvez créer votre paire de clé. 
Cette dernière sera téléchargé après création, il est primordial de la conserver en sécurité (éviter de la perdre, cela évitera d'avoir à en récréer une nouvelle).
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_3.png" width="500" />
</p>

## Etape 2 : Création d'un groupe utilisateur et d'un utilisateur associé

Il est nécessaire de créer un groupe utilisateur ainsi qu'un utilisateur associé pour gérer les permissions d'accès à un projet. Ici nous créerons un unique utilisateur disposant des permissions Admin.

Pour créer le groupe utilsateur ainsi que l'utilisateur associé, cela se passe dans l'onglet <font color="purple">***IAM***</font> (Identify and Access Management) !
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_4.png" width=600" />
</p>

Naviguez dans l'espace <font color="purple">***Groupes***</font> puis <font color="purple">***Créer un groupe***</font>. Le processus de création de groupe est divisé en 3 étapes :
- ***Etape 1 :*** Définition du nom de groupe 
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_5.png" width="600" />
</p>

- ***Etape 2 :*** Définition de la/les stratégie(s) associée(s)   
On sélectionnera ici uniquement la stratégie <font color="purple">***AdministratorAccess***</font>.
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_6.png" width="600" />
</p>

- ***Etape 3 :*** Vérification et création   
Il vous suffit ici de vérifier les informations renseignées au cours des étapes 1 et 2 et de procéder à la création du groupe.

<font color="green">***Votre groupe est désormais créé !***</font> 

Rendez vous maintenant dans l'espace <font color="purple">***Utilisateurs***</font> : 
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_7.png" width="800" />
</p>

Naviguez à travers l'espace <font color="purple">***Ajouter un utilisateur***</font> pour procéder à la création d'un nouvel utilisateur. Cette création est composé de 5 étapes :
- ***Etape 1 :*** Définition des détails de l'utilisateur   
Définissez le nom de l'utilisateur et sélectionnez <font color="purple">***Accès par programmation***</font>.
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_8.png" width="600" />
</p>

- ***Etape 2 :*** Association de l'utilisateur au groupe utilisateur définit précédemment   
Selectionnez le groupe utilisateur précedemment créé pour associer l'utilisateur en cours de création au groupe.
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_9.png" width="600" />
</p>

- ***Etape 3 :*** Ajout de balises   
L'étape 3 est facultative et n'est pas nécessaire dans notre cas, passez directement à l'étape 4.

- ***Etape 4 :*** Verification et création du nouvel utilisateur   
Il vous suffit ici de vérifier les informations renseignées au cours des étapes 1 à 3 et de procéder à la création de l'utilisateur.

- ***Etape 5 :*** Téléchargement des clés publique et privée associées à l'utilisateur     
Cette étape est la plus importante puisqu'ici vous téléchargerez le fichier *.csv* répertoriant les clés publique et privée propre à l'utilisateur. <font color="red"> Attention: C'est le seul espace où vous avez accès à ce fichier. Une fois l'espace de création fermé il ne sera pas possible d'y accèder à nouveau. Assurez-vous donc de bien stocker ce fichier en lieu sûr.</font>
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_10.png" width="600" />
</p>

<font color="green">***Votre utilisateur est désormais créé et associé au groupe précédemment créé !***</font>


## Etape 3 : Création d'un compartiment S3

Cette étape consiste à créer un compartiment de stockage S3 qu'on laissera vide. Ce dernier sera par la suite rempli via un notebook Zeppelin instancié à travers le cluster Spark via EMR.   

Pour se faire, rendez vous dans l'espace <font color="purple">***S3***</font> puis dans <font color="purple">***Créer un compartiment***</font>. Il vous suffira de paramétrer le nom du compartiment S3. <font color="blue">Information :  le nom de compartiment choisi doit être unique à travers tous le service Amazon car il correspond à un espace de stockage dédié du cloud AWS</font>. Les autres paramétres seront conservés par défaut.
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_11.png" width="600" />
</p>

<font color="green">***Votre compartiment S3 est désormais créé !***</font>
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_12.png" width="800" />
</p>

Voyons maintenant comment accorder les accès à notre compartiment S3 à un autre compte AWS.
Pour ce faire, rendez vous dans l'onglet <font color="purple">***Autorisations***</font> de votre bucket S3 et modifiez la partie <font color="purple">***Liste de contrôle d'accès (ACL)***</font>. Il vous faut alors <font color="purple">***Ajouter un bénéficiaire***</font>.
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_13.png" width="700" />
</p>

Il vous suffit maintenant de renseigner l'ID canonique du compte AWS auquel donner les droits d'accès et cocher l'ensemble des cases avant d'<font color="purple">***enregistrer les modifications***</font>.

| <font color="blue">Mais comment connaitre son ID canonique ?</font> Rendez vous dans un compartiment S3 qui vous appartient, onglet <font color="purple">***Autorisations***</font>, rubrique  <font color="purple">***Liste de contrôle d'accès (ACL)***</font>. Votre ID canonique est alors visible : <p align="center"><img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_14.png" width="900" /></p>|
| --- |
## Etape 4 : Création de l'instance EMR

A travers cette étape on créera une instance EMR permettant d'instancier un cluster Spark. On verra également comment rediriger le port du Master Node dans le but de faire tourner un notebook Zeppelin à travers le cluster.

Pour créer votre cluster via EMR rendez vous dans l'espace <font color="purple">***EMR***</font> puis <font color="purple">***Créer un cluster***</font>. 
Dans un premier temps spécifiez le nom de votre cluster. Ensuite, dans la partie <font color="purple">***Configuration des logiciels***</font> précisez l'utilisation de la technologie Spark :
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_15.png" width="600" />
</p>

A la suite, configurez la composition du cluster. On optera ici pour 3 machines de types ***m4.large*** (1 master + 2 slave). C'est également ici qu'on précisera la paire de clé EC2 utilisée pour interroger notre cluster EMR.
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_16.png" width="600" />
</p>

<font color="green">***Votre cluster EMR est créer .. mais pas totalement prêt !***</font>

Une fois votre cluster démarré (cela peut prendre plusieurs minutes), il est nécessaire de paramétrer les règles de connection du Master Node pour autoriser les connections en SSH. Pour cela, depuis l'onglet <font color="purple">***Récapitulatif***</font> de votre cluster, rendez vous dans <font color="purple">***Groupes de sécurité pour le principal***</font> en bas de page. Sélectionnez ensuite le groupe <font color="purple">***ElasticMapReduce-master***</font> et <font color="purple">***Règles entrantes***</font> puis <font color="purple">***Modifier les règles entrantes***</font> comme suit :
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_17.png" width="700" />
</p>

Ajouter ainsi une n-ième règle de la façon suivante :
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_18.png" width="700" />
</p>

<font color="green">***Votre cluster EMR est fin prêt !***</font>

Vous pouvez désormais vous connecter au Master Node dans le but d'exécuter des programmes sur votre cluster.   
La commande suivante, exécutée dans votre terminal, permet de vous connnecter en SSH au Master Node mais également à rediriger le port 8890 du Master node, correspondant à Zeppelin, directement sur le port 8890 sur votre machine locale. 

```
ssh -L 8890:127.0.0.1:8890 -i ~/KeyPair.pem hadoop@DNS_public_principal_master_node
```

où :
- ***~/KeyPair.pem*** correspond au fichier obtenu lors de la création d'une paire de clé (Etape 1)
- ***DNS_public_principal_master_node*** correspond au DNS public du Master Node disponible directement depuis l'espace <font color="purple">***Récapitulatif***</font> de votre cluster 
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_19.png" width="700" />
</p>

Il est possible de rencontrer une erreur au moment de l'exécution de la commande précédente si le fichier de clé (*.pem*) possède des accès trop permissifs: 
```
Permissions 0664 for '~/KeyPair.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
```

Pour solutionner le problème, il est nécessaire de restreindre les droits du fichier à la lecture seule. Pour cela on effectue la commande suivante :
```
chmod 400 ~/KeyPair.pem
```

Une fois cette manipulation effectuée vous pouvez relancer la commande de connection SSH.
En lançant par la suite le port 8890 en local vous devriez accéder à l'application Zeppelin s'exécutant sur le Master Node du cluster EMR : 
<p align="center">
  <img src="https://github.com/MathiasNourry/Tutoriel_AWS/blob/main/S3_EMR_pictures/Tutoriel_20.png" width="700" />
</p>

<font color="green">***A vous de jouer !***</font>
