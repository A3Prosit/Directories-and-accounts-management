
### Etape 1 : Mots clefs :

- Compte utilisateur :
- Interface unique :
- Attribution des droits utilisateurs :
- Serveur de fichier :
- Verrous :
- Droits :
- Poste informatique :
- Téléphonie IP :
- Malware récalcitrant :
- Partage réseau :
- Contrôler l’ensemble des machines :
- Serveur intranet :
- Liste des employés :

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
- Fournit des outils pour **accéder** aux données sur le serveur au niveau du client (connexion, déconnexion, recherches infos, comparaison infos, insérer/modifier/supprimer entrées) càd grande partie authentification.
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

Utilisation du LDIF (LDAP Data Interchange Format), permet d'alimenter simplement un annuaire avec des scripts.
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
	- Eviter de multiplier les arborescence (Simplifiant ainsi les ACL)
	

## 5 - Active Directory  :

L'Active Directory est un LDAP pour windows par Microsoft.
- Identification & Authentification dans un S.I
- Administration centralisée et simplifiée sur un seul logiciel
- Authentification unifiée (un seul compte pour tout le SI)
- Objets uniques sur le réseau
- Référencement (exploiter comme une grande base de données)

**I - Structure :**
- Classes/Objet {Ordinateur} ==> Attributs {PC1=...} --> AD : Classes prédéfinies
- Certains objets peuvent-être des containers d'autres objets

AD a ses propres classes grâce à un schéma (évolutif)
![](https://www.it-connect.fr/wp-content-itc/uploads/2015/06/cours-active-directory-2.png)

La base de données de l'AD est divisé en trois partitions de répertoire appelé 'Naming Context' :
- **Partition de schéma :** Ensemble des définitions des classes et attributs d'objets au sein de l'AD. Elle est unique.
- **Partition de configuration :**  Topologie (domaines, site, contrôleurs de domaine). Elle est unique
- **Partition de domaine :** Informations de tous les objets d'un domaine (ordi, groupe, utilisateur...). Elle est unique au sein d'un domaine. Il y en a donc le même nombre que de domaines.
- 
**II - Contrôleur de domaine & domaine**

I - Groupe de travail au domaine : 
Il existe plusieurs moyens de configurer les postes pour rejoindre le domaine :  

A - Modèle "Groupe de travail" ==> Met en relation les machines d'un même groupe de travail (partage fichier...)
- Une base utilisateur par machine (SAM), unique, non partagée
- Très vite inadapté dès que le nombre de postes augmente (trop lourd)
- Création des comptes utilisateurs en nombre, un compte par machine, comptes propres aux machines
- Simplicité de mise en oeuvre (no brain, no skill)

B - Modèle "Domaine" 
- Base utilisateur, de groupes et d'ordinateurs centralisés. Un seul compte pour toutes les machines du domaine
- Annuaire contient toutes les infos relatives aux objets
- Ouverture de session unique par utilisateur
- Chaque contrôleur de domaine contient une copie de l'annuaire (réplication entre contrôleur)
- Administration et gestion de la sécurité centralisée

II - Les contrôleur de domaine :

Lors de la création de domaine -> serveur utilisé --> "Controleur de domaine"
- Coeur des requêtes
- Identification des objets
- Traiter les demandes d'authentification
- On retrouve une copie de la BDD dans le fichier "NTDS.dit" (Très important)
- Important d'en avoir 2, pour assurer une pérennité en cas plantage
- DFSR (Distributed FIle System Replication) ==> Copie + Dossier **SYSVOL** pour distribuer les stratégie de groupe & de connexion

**III Domaine, arbre, forêt**

![](https://www.it-connect.fr/wp-content-itc/uploads/2015/06/cours-active-directory-7.png)

Ici, on a un domaine, et deux sous-domaines (domaines enfants). On peut appeller ça un arbre.
Un ensemble d'arbre, est appelé une forêt. 

![](https://www.it-connect.fr/wp-content-itc/uploads/2015/06/cours-active-directory-9.png)

Les deux forêts ne partagent pas le même nom, ni la même structure. 
- Tous les arbres d'une forêt partagent un schéma **d'annuaire commun**
- Tous les forêts partagent un "catalogue global"
- Les domaines fonctionnent indépendamment, mais la forêt facilite les communications entre les domaines.
- Simplification de l'administration et flexibilité. paris, peut accéder à rennes si les autorisations se permettent.


**Le niveau fonctionnel :**
A la création d'un domaine, il correspond à la version de l'OS. (Ex : Windows Server 2012).
- Lors d'une MAJ, ceci déclenche un processus de migration.
- Plus le niveau fonctionnel est haut + les dernières nouveautés AD et sa structure
- Il est important d'adapter la version pour les contrôleur
- Impossible de passer à un niveau inférieur
- Il en existe sur le domaine, et sur la forêt. La forêt est le plus critique, car il doit correspondre au minimum de l'ensemble des domaines.

ARCHITECTURE DE L'AD



