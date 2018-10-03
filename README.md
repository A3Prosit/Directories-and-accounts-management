
### Etape 1 : Mots clefs :

- Compte utilisateur : /
- Interface unique : /
- Attribution des droits utilisateurs : /
- Serveur de fichier : /
- Verrous : Une persomme, ou un PS accède à une ressource à un instant donné. (Eviter deux en même temps)
- Droits : / 
- Poste informatique : /
- Téléphonie IP : /
- Malware récalcitrant : Malware qui revient, difficile à retirer
- Partage réseau : / 
- Contrôler l’ensemble des machines : /
- Serveur intranet : Serveur à l'intérieur du réseau
- Liste des employés : /

### Etape 2 : Contexte

Qui ? Responsable informatique
Quoi ? Contrôler l’ensemble du réseau, nouvelle architecture
Où ? Dans une entreprise
Quand ? Maintenant
Pourquoi ? Pour sécuriser le réseau, faciliter le déploiement, faciliter la gestion des utilisateurs
Comment ? une nouvelle interface simple

### Etape 3 : Contraintes

(Principe du TOHE : Technique, Organisationnelle, Humaine, Économique)

- Ne pas mettre 200 verrous en se déplaçant de poste à poste

### Etape 4 : Problématique

- Comment sécuriser et manager les utilisateurs d’un réseau informatique riches en nouveau services ?

### Etape 5 : Généralisation

- MCO : maintenance en conditions opérationnelles

- Gestion des utilisateurs

### Etape 6 : Hypothèses

- Utiliser une base de données

- Utiliser un annuaire LDAP

- Utiliser des actives directory

- Utiliser des groupes utilisateurs

- Utiliser des logiciels de surveillance

- Centraliser la gestion des droits

- Modifier les pares-feux (ex : bloquer les clefs USB)

- Utiliser des GPO

- Utiliser des sous-réseaux

### Etape 7 : Plan d’action

- LDAP
- SGBD
- GPO
- Active directory (avantages, inconvénients)
- Services
- Historique des annuaires
- Equivalents Linux/MAC
-  Réalisation
- Workshop
- Corbeille

## 1 - Le LDAP :

### Présentation générale : 
LDAP (Lightweight Directory Access Protocol)
- Fondé en 1993 par University Of Michigan pour remplacer le DAP (vieux protocole annuaire)
- Fournit des outils (protocoles) pour **accéder** aux données sur le serveur au niveau du client (connexion, déconnexion, recherches infos, comparaison infos, insérer/modifier/supprimer entrées) càd grande partie authentification.
- Protocole standard permettant de gérer des annuaires via TCP IP en respectant les modèles suivants  :
	- Information : Définir le type d'information
	- Nommage : Conventions de nommage, organisation, et désignation
	- Fonctionnel : La manière d'accéder aux informations,  services offert 
	- Sécurité : Mécanismes d'authentifications, droits d'accès des utilisateurs
	
### Généralités
- Très bonnes performances en consultation
- **Lecture privilégiés** au détriment du maintien de la cohérence de l'information
- Aucune indépendance entre les programmes qui accèdent aux données et la structure de l'arbre
	- /!\ Toute modification dans la structure doit-être répercutée sur les programmes
- On lui associe des entrées pour chaque composante
- Plus rapide qu'un SGBD
	- Considéré comme un protocole réseau 
	- Pas de contrôle de cohérence

- Très bonnes performances en consultation
- **Lecture privilégiés** au détriment du maintien de la cohérence de l'information
- Impose de spécifier un chemin d'accès aux données en navigant (à cause langage "navigationel")
- Aucune indépendance entre les programmes qui accèdent aux données et la structure de l'arbre
	- /!\ Toute modification dans la structure doit-être répercutée sur les programmes
- Pour chaque composante qu'une personne essaye d'accéder, il faut lui associer une entrée (duplicats)
	- Ces duplicats alourdissent la gestion des droits des users et multiplie les risques d'erreurs 
		- ==> Penser à modifier l'info partout où elle apparaît 
- Plus rapide qu'un SGBD
	- Considéré comme un protocole réseau 
	- Pas de contrôle de cohérence

### Structure & Nommage
- Hiérarchique en arbre [DIT] (Directory Information Tree) :
	-  La racine est le point d'entrée de l'annuaire, contenant le suffixe
	-  Un noeud (DSE Directory Entry Service) est une **entrée**
		- Normaux (nom,prénom) : Caractérisant **l'objet**
		- Opérationnels : Seul le serveur peut accéder (Dates de modifications...)
	
**On peut caractériser un objet de deux façons :**
	- Nom relatif (**RDN** - relative distinguished name )
	- Nom absolu (**DN** - _Distinguished Name_)

1 - **Le RDN** :
Un ou plusieurs paire(s) clé/valeur (attributs) : `ex : cn=Pillou`
- Un objet doit posséder un et un seul RDN
- nom unique dans la branche de l'objet
- peut-être composé d'un ensemble d'attributs, càd, multivalué : `ex : cn=Pillou+sn=jeff`

2 - **Le DN** :
RDN + ajouter des noms relatifs des entrées parentes (càd ascendants hiérarchique)
	- Chaque **entrée** est indexée par un DN (distinguished name), qui permet d'être unique.
		- uid : userid
		- cn : nom de la personne (common name)
		- givenname : prénom
		- sn : surname
		- o : organization
		- u : service
		- mail : email

`ex : uid=jeapil,cn=pillou,givenname=jean-francois // Le RDN est jeapil ici`
	
![](https://img-19.ccm2.net/isd8MNjwnRXh8aDT1kAUT--Vg0E=/bec9fdd7f96e4f54b7fdc64057c6e87d/ccm-encyclopedia/internet-images-ldaptree.gif)


![](http://www.linux-france.org/prj/edu/archinet/systeme/images/dit.png)


### **Opérations d'un LDAP de base** : 

- Abandonner 
- Ajouter 
-  Bind / Unbind
-  Comparer 
-  Supprimer 
-  Etendre 
-  Rename 
-  Rechercher 

### **Format d'échange de données LDIF** :

Utilisation du LDIF (LDAP Data Interchange Format), permet d'alimenter (exporter/importer/ajouter) simplement un annuaire avec des scripts.
- Alimenter un LDAP via SGBD 
	- Mode de synchro unidirectionnelle (Ne jamais modifier via le LDAP en lui même)
	- Objets ajoutés dans l'annuaire sans soucis de règles d'accès (s'assurer de la validité)
- Exporter/Importer les données d'un annuaire avec un simple fichier texte

Exemple LDIF pour importer  des données :
`[<id>] dn: <distinguished name> <attribut> : <valeur> <attribut> : <valeur> ...`
⇒ Nb : Ici l'ID est facultatif, il permet d'identifier l'entrée dans la BDD
⇒ On saute une ligne vide à chaque nouvelle entrée
⇒ Tabulation pour poursuivre si débordement sur nouvelle ligne
⇒ si caractère spécial, mettre '::' puis valeur encodée en base64

**LDAP V3** :
- Opérations étendues :  Rajouter une opération aux 9 originales ?
- contrôle LDAP : Associer des paramètres supplémentaires à une opération pour en modifier le comportement
- SASL (Simple Authentification and Security Layer), couche supplémentaire pour d'autres méthodes d'authentifications externes de façon modulaire. 	

**Normes :**
-  Fondés par Le CCITT (International Telegraph and Telephone Consultative Committee) devenu ITU (International Telecommunications Unions) depuis; et l'ISO (International Organization for Standardization).
	- Norme annuaire : X500 (1988 puis 1993)
	- Pouvoir être utilisé par tout type d'application, être un standard de tout système ou fabricant.
	- Elle découle en plusieurs sous-standards : X5xx (où xx étant des chiffres) qui régissent modèle, procédures, attributs, classes...
	- Introduit plusieurs termes techniques comme DUA (Directory User Agent) {...}

## Protocole LDAP

- Communications sur le port 389
- LDAPS (avec SSL)
- 

## 2 - Le SGBD :
- Utilisé pour la gestion de la données
- **Modèle logique** des données (relationnel la plupart du temps)
	- Les données accèdent via ce modèle logique
	- Indépendance données-programmes ⇒ Permet modifier BDD sans impacter logiciel
	- On écrit les données sous forme de tables (relations)
	- Utilisation de langage relatifs pour communiquer avec (SQL...)
	- Contraintes d'intégrités {...}
	- Contrôle des transactions
	- Gestion sécurité et confidentialité
- N'offre pas de protocole de réseau standard avec équipements hétérogènes (différent de nature), mais il existe des tentatives de standardisations tq :
	- ODBC (Open DataBade Connectivity, Microsotf) : Standart qui permet la communication des applications entre les SGBD qui ont une interface ODBC. [Microsoft Windows]
	- JDBC (Java DataBase Connectivity) : Idem, a connu un succès à condition que équipements peuvent exécuter du Java.
- SGBD moins rapide qu'un LDAP (Utilisent un modèle, jointures, vérifications, langage (SQL))

## 3 - Cohabitation  :
**SGBD + LDAP ? Cohabitation** ? :

- LDAP : Authentification
- SGBD : Gestion données

**Passerele SGBD/LDAP** :  
- Représentation de l'annuaire dans une BDD : 
	- OpenLDap 
		- /!\Heurte le hiérarchique et le relationnel
		- Pas de multivalué
		- /!\ Hiérarchie de classes 
		- Performances impensable de base sur les recherches (obliger de rajouter des relations)

**Cohabitation ⇒ Oracle Internet Directory** : 
- Implémente une passerelle entre les deux, mais faible performance.

**Méta-Directory** : 

Permet une vue unifiée de sources de provenances variées (LDAP,SGBD, AD...). 

A la réception d'une requête, il interroge la bonne source d'information. Il répondra ensuite via le protocole LDAP.
- Problèmes de prix (15$ par utilisateur)
- Complexité de configuration

==> Solution : Découpage de l'information
- Utilisation de règles simples :
	- Minimiser le nombre d'infos communes au SGBD et à l'annuaire
	- Éviter de multiplier les arborescence (Simplifiant ainsi les ACL)
	

## 5 - Active Directory  :

L'Active Directory est un LDAP pour windows par Microsoft. 
- Identification & Authentification dans un S.I
- Administration centralisée et simplifiée 
- Objets uniques sur le réseau
- Référencement (exploiter comme une grande base de données)
- Compatibilité avec les autres annuaires
- Applications tierces qui prennent en compte ces protocoles peuvent accéder au service
- Adorer s'appuyer sur le DNS

## Configurer des postes ?

L'AD est un LDAP, on retrouvera donc le principe d'objet, avec des noeuds (entrées) {...}.
Il existe plusieurs moyens de configurer les postes, nous retiendrons le modèle "Domaine", c'est celui qu'adopte l'AD.

- Modèle "Domaine" 
	- Base utilisateur, de groupes et d'ordinateurs centralisés. Un seul compte pour toutes les machines du domaine
	- Annuaire contient toutes les infos relatives aux objets
	- Ouverture de session unique par utilisateur
	- Chaque contrôleur de domaine contient une copie de l'annuaire (réplication entre contrôleur)
	- Administration et gestion de la sécurité centralisée".

- "Groupe de travail" ==> Met en relation les machines d'un même groupe de travail (partage fichier...)
	- Une base utilisateur par machine (SAM), unique, non partagée
	- Très vite inadapté dès que le nombre de postes augmente (trop lourd)
	- Création des comptes utilisateurs en nombre, un compte par machine, comptes propres aux machines
	- Simplicité de mise en oeuvre (no brain, no skill)


**DNS & Service Kerberos  :**
- A la différence du DNS (résolution des noms), l'AD est un service d'annuaire. Il résout et renvoi des enregistrements d'objets.
- Les zones DNS peuvent-être enregistrés dans l'AD, pour être répliqués vers d'autre contrôleur de domaine AD.
- Kerberos est un protocole en V5 qui assure authentification avec distribution de tickets (gestion de sessions)
	- Authentification Service (AS) distribue des tickets (TGT) - Se renouvelle x temps
	- Le client doit les retourner auprès des (TGS), disposant ensuite d'un TGS (Granting Service)
	- Ticket TGS composé du nom, mdp, identité, durée...
	- Les deux tickets contiennent un clé de session qui chiffre les communications

### Structure de l'AD

![](https://www.it-connect.fr/wp-content-itc/uploads/2015/06/cours-active-directory-2.png)

La base de données de l'AD est divisé en trois partitions de répertoire appelé *'Naming Context'* :
- **Partition de schéma :** Ensemble des définitions des classes et attributs d'objets au sein de l'AD. Elle est unique.
	- Classes/Objet {Ordinateur} ==> Attributs {PC1=...} --> AD : Classes prédéfinies {Schéma Évolutif}
	- Certains objets peuvent-être des containers d'autres objets
	- On appelle GUID l'ID d'un objet unique au sein d'une forêt, ne change jamais.
- **Partition de configuration :**  Topologie (domaines, site, contrôleurs de domaine). Elle est unique
- **Partition de domaine :** Informations de tous les objets d'un domaine (ordi, groupe, utilisateur...). Elle est unique au sein d'un domaine. Il y en a donc le même nombre que de domaines.

### Principe du contrôleur de domaine

Lors de la création de domaine -> serveur utilisé --> "Contrôleur de domaine"
- Cœur des requêtes
- Identification des objets
- Traiter les demandes d'authentification
- On retrouve une copie de la BDD dans le fichier **"NTDS.dit"** (Très important)
- Important d'en avoir 2, pour assurer une pérennité en cas plantage
- DFSR (Distributed FIle System Replication) ==> Copie + Dossier **SYSVOL** pour distribuer les stratégie de groupe & de connexion
	- Sert à stocker des données spécifiques qui doivent-être répliqués entre contrôleur, ou accéssible par ordi client.
	- Scripts de connexions
	- Stratégie de groupe (GPO)
	- c\Windows\SYSVOL
	- Ecris en BATCH
	- Dupliqué pour appliquer partout
	- Dossier "Policies" dans "Domain" 

- LE PREMIER QUE L'ON CREE EST : 
- **Premier contrôleur de domaine** = "Serveur catalogue global", c'est un maître d’opération
	- Va contenir le catalogue global (cf : Arbre & forêt)
	- Seul un maître d'opération peut accepter certaines modifications
	- 5 rôles :
		- ADDS (Active Directory Domain Services) : Rôle principal de l'AD, annuaires, utilisateurs, ordinateurs, groupes...
		- ADCS (Active Directory Certificate Services) : Couche supplémentaire au SI, crée des clés et des certificats, pour la sécurité
		- ADFS (Active Directory Federation Services) : Intégration du SSO (Authentification unique), avec tokens
		- ADRMS (Active Directory Rights Management Services) : Dans l'OS, Autorisations sur fichiers users, c'est une application client/server.
		- ADLDS (Active Directory Lightweight Directory Services) : Comme ADDS, mais n'implique pas la création d'un domaine. Utilisé pour magasin d'authentification avec base user (Comme prestataire externe)

- Il disposera de 5 rôles FSMO, qu'on peut re-distribuer entre contrôleurs
		- Contrôleur de schéma (MAJ...)
		- Affectation de nom de domaine (add/supp domaines)
		- RID (Attribuer un Secure ID lors de connexion)
		- Emulateur PDC (Traite échange password, mauvais password...)
		- Infrastructure (MAJ références croisés de domaines, déplacement objet, références noms entre contrôleur...)
		- 
###  Principe de l'arbre et de la forêt
 
![](http://image.noelshack.com/fichiers/2018/40/3/1538566758-43146479-274483406526460-2572044313420627968-n.jpg)

![](https://www.it-connect.fr/wp-content-itc/uploads/2015/06/cours-active-directory-7.png)

![](https://www.it-connect.fr/wp-content-itc/uploads/2015/06/cours-active-directory-9.png)

Ici, on a un domaine, et deux sous-domaines (domaines enfants). On peut appeler ça un arbre.
- Si l'on vient greffer un arbre à un autre arbre, on appelle ça une forêt.

Les deux arbres ne partagent pas le même nom, ni la même structure : 
- Tous les arbres partagent un **"catalogue global"** (du premier contrôleur de domaine)
	- C'est une BDD conservée sur un ou plusieurs contrôleurs de domaines
	- Contient la liste de tous les objets dans une forêt d'un AD
	- Crée automatiquement sur le premier contrôleur
	- Valide les références objets dans une forêt
	- Peut contacter les autres contrôleur pour récupérer les infos du user qui essaye de se connecter sur un autre domaine
	- On peut en affecter sur chaque contrôleur que l'on souhaite
	- Permet de gagner du temps pour les recherches (pas besoin de parcourir tous les domaines)
	- Chaque forêt doit en avoir un, répond aux requêtes sans impacter le réseau
	- A mettre sur chaque site également
	- Permet de terminer le processus d'authentification de session (détermine le groupe)
	- Permet d'ouvrir une session en déterminant leur groupe
	- Il est conseillé d'avoir deux contrôleurs en catalogue global
- Les domaines fonctionnent indépendamment, mais la forêt facilite les communications entre les domaines.
- Simplification de l'administration et flexibilité. paris, peut accéder à rennes si les autorisations se permettent.

**Principe de portée de groupe**

- Domaine local : le groupe reste dans le domaine où il est crée
- Globale : Local + approuvé par le domaine de base. Pourra contenir d'autres objets du domaine,contrôler l'accès à ses ressources
- Universel : Accessible à l'ensemble de la forêt

![]( https://www.it-connect.fr/wp-content-itc/uploads/2015/06/cours-active-directory-15.png)

Ici : 
- _Comptabilité_ : étendue « domaine local » sur « paris.it-connect.local »  
- _Direction_ : étendue « globale » sur « learn-online.local » qui approuve tous les sous-domaines  
- _Informatique_ : étendue « universelle » sur la forêt

On retrouve aussi :
- Groupe sécurité : Donner des autorisations aux ressources, contrôle accès
- Distribution : Liste d'@email en ajoutant des contacts (diffusion, ex : messagerie)
- Intégrés : Par défaut (Admin...) ⇒ 'Built-in'

**Relations d'approbations** : Lien entre deux AD ou deux forets AD. ça peut être unidirectionnelle ou bi-directionelle (Principe de la transitivité)


### Annexes : 

**Le niveau fonctionnel :**
A la création d'un domaine, il correspond à la version de l'OS. (Ex : Windows Server 2012).
- Lors d'une MAJ, ceci déclenche un processus de migration.
- Plus le niveau fonctionnel est haut + les dernières nouveautés AD et sa structure
- Il est important d'adapter la version pour les contrôleur
- Impossible de passer à un niveau inférieur
- Il en existe sur le domaine, et sur la forêt. La forêt est le plus critique, car il doit correspondre au minimum de l'ensemble des domaines.

**Pour de l'AD** 
- Intégration avec DNS
- Flexibilité des requêtes (recherches)
- Capacité d'extension (Ajouter classes objets / Attributs)
- Administration par stratégie de groupe (GPO : Acès aux objets d'annuaire, et aux ressource de domaine)
- Adaptabilité (Un à plusieurs contrôleur, ce qui permet d'adapter l'architecture réseau, arbres / forêt)
- Réplication d'information (Tolérance de panne - réplication de l'annuaire entre contrôleur)
- Sécurité des informations (Centralisation de l'authentification, stratégie de sécurité)
- Intéropérabilité (S'appuie sur le LDAP et ses protocoles, donc peut fonctionner avec d'autres comme ADSI)

**Contre de l'AD**
- Révelation des mots de passes windows depuis la mémoire (2011) #Failles générales
- Si le serveur AD indisponible, les comptes ne peuvent pas être authentifiés 
- Sécurité assurée par l'admin AD et non l'admin du groupe PS Series


## 6 - Politique de sécurité :

GPO : Group Policy Object
- Configurer les ordis et les paramètres utilisateurs facilement basé sur l'AD
- Le réseau doit-être basé sur l'Active Directory Domain Services (pour centraliser la sécurité)
- Les ordinateurs doivent-être sur le domaine 

**Utiliser GMPC, ou GPO depuis Windows Server :**
- Installer des sécurités sur un compte
- Installer des sécurités sur les domaines
- Installer des sécurités sur les utilisateurs du domaine
- Manage les GPOs avec GPMC et éditer avec le GPME
**Sécu**
- Créer des organisation dans les domaines (Ou), qui ont des enfants, ce qui permet d'appliquer les droits que si on les lies. (Domaines, users...)
-  Les droits héritent automatiquement du père (écrasant les paramètres des fils)
- Si on a deux droits, on peut changer la priorité (Link order) : Qui s'exécute en premier
- On a un dossier/fichier qui contient les paramètres à appliquer à un utilisateurs ou à un ordinateurs
	- Polices : Règles de police qui paramètre les GPO
	- Préférences : On peut déterminer des applications spécifiques, caractéristiques de windows, administrer {...]
- Les règles se rafraîchisse par défaut toutes les 90 minutes, ou redémarrer le PC. (On peut le changer)


## Historique des annuaires
- Nécessité de gérer association DNS/IP + nécessité répartition responsabilité des domaines
- x500 --> Interconnecter tous les équipements, définissant le protocole de communication
- LDAP : Protocole plus souple, ne définit que l'échange entre les applications et les annuaires
## Équivalents Linux / Mac
- CentOS --> Grande distribution Linux
- OSx --> Mac
## Corbeille d'exo

