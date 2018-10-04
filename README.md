
# Etape 1 : Mots clefs :

- Annuaire: système de stockage de données dériv des BDD hiérarchisées permetant en particulier de conserver les données pérennes (peu mise à jour). Ex: coordonnées
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

# Etape 2 : Contexte

Qui ? Responsable informatique
Quoi ? Contrôler l’ensemble du réseau, nouvelle architecture
Où ? Dans une entreprise
Quand ? Maintenant
Pourquoi ? Pour sécuriser le réseau, faciliter le déploiement, faciliter la gestion des utilisateurs
Comment ? une nouvelle interface simple

# Etape 3 : Contraintes

(Principe du TOHE : Technique, Organisationnelle, Humaine, Économique)

- Ne pas mettre 200 verrous en se déplaçant de poste à poste

# Etape 4 : Problématique

- Comment sécuriser et manager les utilisateurs d’un réseau informatique riches en nouveaux services ?

# Etape 5 : Généralisation

- MCO : maintenance en conditions opérationnelles

- Gestion des utilisateurs

# Etape 6 : Hypothèses

- Utiliser une base de données

- Utiliser un annuaire LDAP

- Utiliser des actives directory

- Utiliser des groupes utilisateurs

- Utiliser des logiciels de surveillance

- Centraliser la gestion des droits

- Modifier les pares-feux (ex : bloquer les clefs USB)

- Utiliser des GPO

- Utiliser des sous-réseaux

# Etape 7 : Plan d’action

## Etudes
### LDAP

**LDAP** (Lightweight Directory Access Protocol) est un protocol standard permettant de gérer des annuaires (directories) càd accéder à des bzses d'info sur les utilisateur d'un réseau par l'intermédiaire de protocoles TCP/IP

Les bases d'informations sont généralement relatives à des utilisateurs, mais pas uniquement. ex: gérer du matériel dans une entreprise.

LDAP a été dev 1993 par l'université du Michigan. Son but était de supplanter DAP qui sert à accéder au services d'annuaires X500 en l'intégrant à la suite TCP/IP. A partir de 1995 LDAP est devenu natif (standalone) pour ne plus servir uniquement à accéder à des annuaires X500

Le protocole LDAP définit la méthode d'accès aux données sur le serveur au niveau du client, et non la manière de laquelle les informations sont stockées.
Il est actuellement à la version 3 et a été normalisé par l'IETF

-   [RFC 1777](http://www.ietf.org/rfc/rfc1777.txt)  pour LDAP v.2 standard
-   [RFC 2251](http://www.ietf.org/rfc/rfc2251.txt)  pour LDAP v.3 standard

LDAP fournit un ensemble d'outils.

1.  un protocole permettant d'accéder à l'information contenue dans l'annuaire,
    
2.  un modèle d'information définissant l'organisation et le type des données contenues dans l'annuaire,
    
3.  un modèle de nommage définissant comment l'information est organisée et référencée
    
4.  un modèle fonctionnel qui définit comment accéder à l'information,
    
5.  un modèle de sécurité qui définit comment accéder aux données et comment elles sont protégées

6.  un modèle de duplication qui définit comment la base est répartie entre serveurs,
    
2.  des APIs pour développer des applications clientes,
    
3.  LDIF, (Ldap Data Interchange Format) un format d'échange de données.


LDAP fournit à l'utilisateur des méthodes lui permettant de :

-   se connecter
-   se déconnecter
-   rechercher des informations
-   comparer des informations
-   insérer des entrées
-   modifier des entrées
-   supprimer des entrées
Il propose également des mécanisme de chiffrement (SSL) et d'authentification (SASL)

##### L'arborescence d'informations (DIT)

LDAP présente les informations sous forme d'une arborescence d'informations hiérarchique appelée **DIT** (_Directory Information Tree_)
Les informations appelées entrées ou Directory Service Entry (DSE) sont représentées sous forme de branches (les noeuds). Une branche qui se situe à la racine d'une ramifiation est appelée racine ou suffixe (root entry).

Chaque entrée de l'annuaire LDAP corespond à un objet abstrait ou réel (une personne, un objet, des paramètres,...)

Chaque entrée est constituée d'un ensemble de paires clés/valeurs appelées attributs
![Directory Information Tree de LDAP](https://img-19.ccm2.net/isd8MNjwnRXh8aDT1kAUT--Vg0E=/bec9fdd7f96e4f54b7fdc64057c6e87d/ccm-encyclopedia/internet-images-ldaptree.gif)

Les données sont stockées sur un format de base de données hiérarchique de type "dbm". Ce format est différent des bases de données relationnelles, conçues pour supporter de multiples mises à jour. DBM est conçu pour supporter peu de mises à jour, mais de nombreuses consultations.

 #### Les attributs des entrées
 chaque entrée est constituée d'un ensemble d'attributs (paire clé/valeur) permettant de caractériser l'objet que l'entré définit. Il existe 2 types d'attributs:
 - normaux : caractérisant l'objet. ex: nom, prénom
 - opérationnels : attriuts auquels le serv peut accéder afin de manipuler les données de l'annuaire

Chaque objet est composé d'attributs en fonction des types d'attributs décrits dans les classes d'objets. Un attribut est généralement un couple clé/valeur, mais peut être caractérisé par un nom, un OID, s'il est mono ou multi-évalué, un indicateur d'usage (facultatif/obligatoire), un format (voir par exemple pour les images).

Les OID sont normalisés par la RFC2256 et sont issus d'un schéma X500. Les OID sont tenus à jour par l'IANA Internet Assigned Numbers Authority. Un OID est une séquence de chiffres séparés par un "." point (Exemple 1.2.3.4 ) qui permet d'identifier de façon unique un élément du schéma LDAP.

Une entrée est indexée par un **nom distinct** (**DN**, _distinguished name_) permettant d'identifier de manière unique un élément de l'arborescence.

Un DN se construit en prenant le nom de l'élément, appelé _Relative Distinguished Name_càd le chemin de l'entrée par rapport à un de ses parents et en lui ajoutant l'ensemble des noms des entrées parentes

Il s'agit d'utiliser une série de paires clé/valeur permettant de repérer une entrée de manière unique. 
Série de clés généralement utilisées :

-   **uid**  (_userid_), identifiant unique obligatoire
-   **cn**  (_common name_), nom de la personne
-   **givenname**, prénom de la personne
-   **sn**  (_surname_), surnom de la personne
-   **o**  (_organization_), entreprise de la personne
-   **u**  (_organizational unit_), service de l'entreprise dans laquelle la personne travaille
-   **mail**, mail de la personne

Ainsi un _Distinguished Name_ sera de la forme :  

``` 
uid=jeapil,cn=pillou,givenname=jean-francois 
```
Le _Relative Distinguished Name_ étant ici "_uid=jeapil_".

On appelle **schéma** l'ensemble des définitions d'objets et d'attributs qu'un serveur LDAP peut gérer. On peut par exemple de définir si un attribut peut posséder une ou plusieurs valeurs.
note: un objet nomé objectclass permet de définir les attributs étant obligés ou facultatifs


#### Consulter les données

LDAP fournit un ensemble de fonctions (procédures) pour effectuer des reqpetes sur les donées pour les modif, effacer, faire des recherches.
Les principales opérations sont :


| opération | Description | 
|---|---|
| abandon | abandonne l'opération précédemment envoyées au serveur  |
| add | ajoute une entrée au répertoire |
| bind | initie une nouvelle session sur le serveur LDAP |
| compare | compare les entrée d'un répertoire selon des critères |
| delete | supprime une entrée d'un répertoire |
| extend | effectue des opérations  étendues|
| rename | Modifie le nom d'une entrée |
| serch | Recherche des entrées d'un répertoire |
| unbind | termine une session sur le serveur LDAP |

#### Le format d'échange de données LDIF

LDAP fournit un format d'échange (**LDIF**, _Lightweight Data Interchange Format_) permettant d'importer et d'exporter les données d'un annuaire avec un simple fichier texte. La majorité des serveurs LDAP supportent ce format, ce qui permet une grande interopérabilité entre eux.

La syntaxe de ce format est la suivante :  
```
[<id>] dn: <distinguished name> <attribut> : <valeur> <attribut> : <valeur> ...
 ```
Dans ce fichier, _id_ est facultatif, il s'agit d'un entier positif permettant d'identifier l'entrée dans la base de données.

-   chaque nouvelle entrée doit être séparée de la définition de l'entrée précédente à l'aide d'un saut de ligne
-   Il est possible de définir un attribut sur plusieurs lignes en commençant les lignes suivantes par un espace ou un tabulation
-   Il est possible de définir plusieurs valeurs pour un attribut en répétant la chaîne  _nom:valeur_  sur des lignes séparées
-   lorsque la valeur contient un caractère spécial (non imprimable, un espace ou  _:_), l'attribut doit être suivi de  _:_  puis de la valeur encodée en base64

### Backends

HDB backend est le backend utilisé pour une bdd slapd, il utilise le package Oracle Berkeley DB (BDB) pour stocker les données. Il utilise fortement l'indexation et le caching pour accélérer l'accès aux données

HDB est une variante du backend BDB, hbd utilise un layout de bdd hiérarchique supportant le renomage des sous-arbres. Il est sinon identique à BDB et les mêmes config s'appliquent

note : la BDD HDB a besoin d'une large idlcachesize pour avoir de bonnes perf, généralement 3 fois la cachesize ou +
note 2:  le backend HDB a supplanté le backend BDB et tous deux vont être dépréciés en faveur du backend MDB

### SGBD
### GPO
### Active directory (avantages, inconvénients)

Active Directory est un annuaire LDAP pour les systèmes d’exploitation Windows

intérêts d'active directory:

- administration centralisée et simplifiée: la gestion des objets, comptes utilisateurs et ordi est simplifiée, car tout est centralisé dans l'annuaire active directory
- unifier l'authentification: un utilisateur authentifié sur une machine, elle-même authentifiée, pourra accéder aux ressources stockées sur d’autres serveurs ou ordinateurs enregistrés dans l’annuaire. De nombreuses applications peuvent s'appuyer sur Active directory pour l'anthentification, on peut donc accéder à tout le système d'information avec un seul compte
- identifier les objets sur le réseau :  chaque objet enregistré dans l’annuaire est unique, ce qui permet d’identifier facilement un objet sur le réseau et de le retrouver ensuite dans l’annuaire.
- Référencer les utilisateurs et les ordinateurs: l'annuaire est similaire à une énorme BDD référençant les utilisateurs, groupes et ordinateurs. On s’appuie sur cette base de données pour réaliser de nombreuses opérations : authentification, identification, stratégie de groupe, déploiement de logiciels, etc.

#### Structure de l'active directory

**Classes et attributs** : classes: objets disposant de mêmes attributs, certains objets peuvent être des containers d'autres objets (ex: un groupe contient des personnes). Les unité d'organisation sont des containers, sans elles l'annuaire ne pourrait pas être trié correctement

**Le schéma** 
par défaut tout annuaire Active Directory dispose de classes prédéfinies ayant des attributs spécifiques, ces attributs sont définis grâce à un schéma. Il contient la définition de toutes les classes et de tous les attributs disponibles et autorisés au sein de l'annuaire.
Note: le schéma et évolutif, pas fixe il peut évoluer selon le besoin

**Partitions d'annuaire**
la bdd active directory est divisée en 3 partitions logiques appelé « Naming Context ». Il s'agi de la partition de schéma, la partition de configuration et celle de domaine.

- **La partition de schéma**  : elle contient l'ensemble des définitions des classes et attributs d’objets, qu’il est possible de créer au sein de l'annuaire Active Directory. Elle est unique au sein d’une forêt.

-  **La partition de configuration**  : elle contient la topologie de la forêt : informations sur les domaines, les liens entre les contrôleurs de domaines, les sites, etc. Elle est également unique au sein d’une forêt.

-  **La partition de domaine**  : elle contient les informations de tous les objets d'un domaine : ordinateur, groupe, utilisateur, etc. Elle est unique au sein d’un domaine, il y en aura donc autant que de domaines.

**Les groupes de travail**  permettent de mettre en relation des machines windows pour partager des fichiers entre autre

**modèle groupe de travail**

- **Une base d’utilisateurs par machine**  : appelée « base SAM », elle est unique sur chaque machine et non partagée, ainsi, chaque machine contient sa propre base d’utilisateurs indépendante les unes des  autres.

-  **Très vite inadapté dès que le nombre de postes et d’utilisateurs augmente**, car cela devient lourd en administration et les besoins différents.

-  **Création des comptes utilisateurs en masse**, car chaque utilisateur doit disposer d’un compte sur chaque machine, les comptes étant propres à chaque machine.

-  **Simplicité de mise en œuvre et ne nécessite pas de compétences particulières**  en comparaison à la gestion d’un annuaire Active Directory.

**Modèle « Domaine »**

-  **Base d’utilisateurs, de groupes et d’ordinateurs centralisée.**  Un seul compte utilisateur est nécessaire pour accéder à l’ensemble des machines du domaine.

-  **L’annuaire contient toutes les informations relatives aux objets**, tout est centralisé sur le contrôleur de domaine, il n’y a pas d’éparpillement sur les machines au niveau des comptes utilisateurs.

-  **Ouverture de session unique par utilisateur**, notamment pour l’accès aux ressources situées sur un autre ordinateur ou serveur.

-  **Chaque contrôleur de domaine contient une copie de l’annuaire**, qui est maintenue à jour et qui permet d’assurer la disponibilité du service et des données qu’il contient. Les contrôleurs de domaine se répliquent entre eux pour assurer cela.

-  **Administration et gestion de la sécurité centralisée**.

#### Les contrôleurs de domaine

A la création d'un domaine, le serveur depuis lequel on le créé est promu "contrôleur de domaine" du domaine. Càd qu'il sera le coeur des requêtes et devra donc vérifier les identifications des objets, traiter les dmeande d'authentification veiller à l'application 	

Un contrôleur de domaine est indispensable au bon fonctionnement du domaine, si l’on éteint le contrôleur de domaine ou qu’il est corrompu, le domaine devient inutilisable.

Lorsque qu'on créé le premier contrôleur de domaine dans notre organisation, on créé également le premier domaine, la première forêt et que le premier site.

Sur chaque contrôleur de domaine on a un copie de la BDD de l'annuaire Active Directory, il s'agitdu fichier **NTDS.dit** qui contient toutes les données de l'annuaire. On peut faire des snapshot de ce fichier pour le consulter en mde hors ligne avec des outils.

##### **Réplaction des contrôleurs**
Une redondance est nécessaire pour assurer la continuité des services d'annuaire. Cela permet également d'assurer la pérénnité de la base
  
 Sur les anciennes version Windows server (dont 2000 et 2003) le mécanisme **FRS** (File Replication Service) était utilisé. Depuis Windows Server 2008 on utilise **DFSR** (Distributed File System Replication) qui est plus fiable et performant
 Les contrôleurs de domaine répliquent les informations entre eux à intervalle régulier, afin de disposer d’un annuaire Active Directory identique. Un numéro de version est géré par les contrôleurs de domaine, ce qui permet à un contrôleur de domaine de savoir s’il est à jour ou non par rapport à la version la plus récente de l’annuaire.

![](http://www.it-connect.fr/wp-content-itc/uploads/2015/06/cours-active-directory-3.png)

Les contrôleurs de domaine partage également le fichier SYSVOL utilisé pour distribuer les stratégies de groupe et scripts de cnnexion
### Services
### Historique des annuaires

Les premiers annuaires électroniques sont apparus avec les premiers ordinateurs. Ils avaient des tâches ciblées : authentification, contrôle des accès, information de type contacts.
Iln'existait aucun standard et chaque système avait sa façon de gérer les utilisateurs

au milieu des 70's l'utilisation de réseaux se généralise, les systèmes deviennet distribuéset l'utilisation massive des LAN fait apparaître de nouveaux types de services qu'on apparente à des annuaires : les DNS et le protocole Whois
fin 80's et dan les 90's on assiste à un multiplication d'annuaires spécifiques : les annuires inclus dans des logiciels ou suites de logiciels (mails, etc) Lotus Notes, Novell Groupwise Directory, Microsoft Exchange Directory, Sendmail UNIX et son /etc/aliases. Il y a aussi les Annuaires Internets comme Yahoo, ou Bigfoot.

fin 90's on voit apparaitre des network operating systems: app fournissant des services à des clients e des serv à travers un réseau, ils permettent le partage de ressources commes des fichiers ou imprimantes t incluent des annuaires électroniques (Novell NDS, MS Active Directory).

**Normes :** 2 organismes se sont mis a travailler en parallèe sur la standardistion après l'apparition das les 80's des annuaires généraux et multi-usage: 
Le CCITT (International Telegraph and Telephone Consultative Committee) devenu ITU (International Telecommunications Unions) depuis
et l'ISO (International Organization for Standardization)

les deux projets de standardisation ont fusionnés pour créer la norme X500 : première version 1988, 2eme 1993
la norme devait fournir de nombreuses possibilités de recherches, permettre la distribution d'annuaires à grand échelle. Un des objectifs était de créé un service de page blanches mondiale


La norme X500 est constituée de plusieurs standards:

X500: Vue d'ensemble des concepts, des modèles et des services

X501: Modèles associés aux annuaires X500.

X509: Procédures d'identification et d'authentification.

X511: Définition des services (recherche, création, suppression)

X518: Description du fonctionnement distribué.

X519: Protocoles de communication entre serveurs, et entre serveurs et clients clients/serveurs.

X520: Attributs des annuaires X500 prédéfinis

X521: Classes d'objets des annuaires X500 prédéfinies.

X525: Description de la réplication sur les serveurs

Les standards X520 et X519 définisse un langage commun minimum pour l'échange d'informations et sont repris en partie dans la norme LDAP.

La norme X500 a introduit un certain nombre de termes techniques:

Directory User Agent (DUA): Poste ou logiciel client accédant à un annuaire.

Directory Access Protocol (DAP): Protocole de communication entre un client et un serveur annuaire.

Directory System Agent (DSA): Un serveur annuaire. Ce terme est encore utilisé dans la norme  LDAP.

Directory System Protocol (DSP): Protocole de communication entre deux serveurs. Proche du  DAP.

Directory Information Shadowing Protocol: Protocole pour la réplication entre  DSA  maitre et  DSA  miroir.


La norme a vite été jugée comme trop riche et complexe à mettre en oeuvre et des problèmes de performances ont été constatés à cause du modèle OSI. LDAP est donc venu la remplacer 

### Equivalents Linux/MAC
##  Réalisation
### Workshop
### Corbeille

