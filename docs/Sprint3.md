## Introducció
En aquest sprint, descriurem com configurar en un entorn Ubuntu Linux un servidor LDAP per a la gestió centralitzada de dominis, usuaris, grups i polítiques de seguretat. El procés inclou la instal·lació i configuració d'un servidor LDAP, la creació de comptes d'usuari i la definició de polítiques de seguretat.

Instal·larem serveis essencials com servidors de fitxers, que s'integren amb LDAP per a l'autenticació centralitzada. Implementarem connexions remotes segures i gestionarem permisos d'accés mitjançant LDAP.

# Instal·lació domini LDAP
- Un domini LDAP (Lightweight Directory Access Protocol) és una estructura jeràrquica que s'utilitza per gestionar i organitzar informació sobre usuaris, grups, recursos i altres objectes en una xarxa. Serveix com a repositori centralitzat per a l'autenticació i la gestió d'accés, permetent que múltiples sistemes i aplicacions comparteixin la mateixa informació d'usuaris i permisos.

- Una Unitat Organitzativa (UO) és un contenidor dins de l'estructura LDAP que s'utilitza per organitzar objectes com usuaris, grups i recursos. Les UO permeten una gestió més fàcil i lògica dels objectes, ja que es poden agrupar segons criteris com departaments, ubicacions o funcions.

- Per crear un domini LDAP haurem de seguir els següents pasos:

- En primer lloc mirarem l'ip de la nostra màquina servidor, en aquest cas la configurarem manualment i comprovarem si aquesta fa ping.
![LDAP1](LDAP1.png)
![LDAP2](LDAP2.png)
![LDAP3](LDAP3.png)

- Per continuar canviarem el nom del host al nostre equip per a això editarem el fitxer ```etc/hostname``` i farem el mateix amb el fitxer ```etc/hosts```
![LDAP4](LDAP4.png)
![LDAP5](LDAP5.png)

- El següent pas ens implica si no tenim el paquet slapd, instalar-lo, un cop el tenim l'executem, això si, pot ser que hi hagin problemes fent la configuració, jo mateix en vaig viure un, si en aquest cas tenim algun impediment o contratemps podem tornar a reconfigurar el paquet amb la següent comanda, i llavors ja podrem procedir amb la configuració.
```
dpkg-reconfigure slapd
```
![LDAP6](LDAP6.png)

- Com podem veure ja sen's obre un menú de configuració que haurem d'anar emplenant tal i com es mostra a continuació, es important que s'elimini la base de dades que hi ha previa a l'instal·lació, simplement ens limitem a seguir els pasos que es mostren.

![LDAP7](LDAP7.png)
![LDAP8](LDAP8.png)
![LDAP9](LDAP9.png)
![LDAP10](LDAP10.png)
![LDAP11](LDAP11.png)
![LDAP12](LDAP12.png)
![LDAP13](LDAP13.png)
![LDAP14](LDAP14.png)
![LDAP15](LDAP15.png)

- Bàsicament el que hem fet es assignar una contrasenya i uns noms de domini. 

- Una vegada configurat el paquet per comprovar si ho hem fet correctament utilitzarem la comanda ```slapcat```

![LDAP16](LDAP16.png)

- Com es pot apreciar els valors coincideixen amb els de la configuració prèvia. Per continuar ara configurarem les "ou", grups i usuaris tal i com es mostra, també es veurà com aplicar els canvis al LDAP.
- Es important que als arxius es tingui en compte els noms de domini que hem configurat ja que podria ser una font d'errors. En primer lloc configurem les OU a l'arxiu ```ou.ldif```

![LDAP17](LDAP17.png)

- A continuació configurem els grups al fitxer ```group.ldif```

![LDAP18](LDAP18.png)

- Seguidament configurem els usuaris al fitxer ```usu.ldif```

![LDAP19](LDAP19.png)

- Com els fitxers anteriors no són per defecte, els hem fet nosaltres, ara els hem d'inserir al LDAP i afegir una nova entrada per tant els afegirem de la següent forma.

![LDAP20](LDAP20.png)

- Tal i com hem vist abans per confirmar si els canvis s'han aplicat podem utilitzar la comanda ```slapcat```.

![LDAP21](LDAP21.png)

## Instal·lació domini LDAP amb un server

A l'apartat anterior hem vist com instal·lar un domini LDAP amb una maquina Ubuntu amb interficie gràfica, en aquest cas ho instal·larem a un ubuntu server directament, un cop arribem a la configuració de paquets com al pas anterior pararem, aquí es mostraran les diferencies que hi ha al fer-ho sense interficie gràfica.

- En primer lloc configurarem les opcions de xarxa del nostre server, s'ha de modificar un arxiu netplan en concret. Allà modificarem la nostra ip.

```
sudo nano /etc/netplan/50-cloud-init.yaml
```
![server1](server1.png)
![server2](server2.png)

- Un cop hem fet aquesta configuració l'haurem d'aplicar i comprovar si s'ha fet correctament.

![server3](server3.png)

- Ara modifiquem els arxius de hosts i hostnames del directori etc.

![server4](server4.png)
![server5](server5.png)

- Per últim descarregem el paquet ldap i a ```partir d'aqui seguim els passos anteriors, ja que es el mateix!```
```
apt install slapd ldap-utils
```
![server6](server6.png)

# Entorns gràfics
# Unir equips al domini

En aquesta part veurem com connectar un equip client al domini que hem creat prèviament.

- El primer pas seria instal·lar el següent paquet al nostre equip client:
```
apt install libnss-ldap libpam-ldap nscd
```
![domini1](domini1.png)

- Un cop finalitzi l'instal·lació del paquet sen's obrirà una pestanya de configuració similar a l'anterior que hem vist durant la configuració del servidor, en aquest cas es important saber la IP de la màquina servidor ja que serà el primer que configurarem tal i com es mostra a la següent imatge.

![domini2](domini2.png)
![domini3](domini3.png)

- Un cop feta aquesta part seguirem amb la configuració respectant els noms de domini que hem donat prèviament tal com es mostra a continuació.

![domini4](domini4.png)
![domini5](domini5.png)
![domini6](domini6.png)
![domini7](domini7.png)
![domini8](domini8.png)
![domini9](domini9.png)
![domini10](domini10.png)

- Amb aquestos pasos ja tindrem el paquet configurat. Seguidament haurem de canviar alguns parametres del client ja que volem que detecti primer el servidor ldap, i ho farem modificant el fitxer ```etc/nsswitch.conf```.

![domini11](domini11.png)

- D'aquesta forma ens assegurem que els usuaris que hi han al LDAP siguin revisats pel nostre client, per això li indiquem els fitxers que ha de revisar, els d'usuari, grups i contrasenyes.

- A continuació veurem com configurar la sessió dels usuaris LDAP, per a fer això també necessitarem configurar el següent fitxer ```etc/pam.d/common-session```

![domini12](domini12.png)

- Aquesta modificació assegura que quan un usuari inicia sessió i no té un directori principal, se li crearà automàticament un nou directori principal amb el contingut de ```/etc/skel``` i amb els permisos especificats per umask=022.

- A continuació configurarem ajustant com es presenta la pantalla d'inici de sessió als usuaris, especificant la sessió per defecte i permetent l'accés manual amb el nom d'usuari.

![domini13](domini13.png)

- Amb aquesta configuració indiquem que el gestor de pantalla ha de mostrar una opció per permetre als usuaris introduir manualment el seu nom d'usuari en lloc de seleccionar-lo d'una llista. Això pot ser útil en entorns on hi ha molts usuaris o quan es vol permetre l'accés a usuaris que no apareixen a la llista predeterminada.

- Seguidament farem una prova per alu1 un usuari inserit i configurat prèviament, primerament ens assegurem de que aquest aparegui al fitxer passwd.

![domini14](domini14.png)

- Com aquest apariex reeiniciarem el sistema i a l'hora d'escollir usuari seleccionarem l'opció de no apareixeu llistat, llavors indicarem usuari i contrasenya. Una altra opció es fer-ho des del terminal, canviant d'usuari amb su alu1. La qüestió es poder utilitzar l'usuari alu1.

![domini15](domini15.png)
![domini16](domini16.png)

# Gestió del domini
Per crear l’esquema bàsic de l’organització i començar a treballar hi ha dues opcions: fitxers ldif, i utilitzar comandes. En aquest apartat veurem les comandes que hi han tips que hi ha com ara, "search", "add", "modify", i "delete".

- Abans de començar comprovarem com he, vist anteriorment que tenim el domini ben creat i després veurem que a la carpeta d'escriptori hi ha uns arxius preparats amb dades d'usuaris.

![GD1](GD1.png)
![GD2](GD2.png)
![GD3](GD3.png)

- Com es pot veure el fitxer esta ple de dades d'usuaris que voldrem agregar, i ho farem amb un add.

## ldapadd

- Amb la comanda ```ldapadd``` podrem afegir els usuaris que hem vist abans per fer-ho utilitzarem la següent comanda.

```
ldapadd -x -D "cn=admin,dc=sabate,dc=cat" -w marc -f dades.ldif
```
![GD4](GD4.png)

- Com podem veure s'han afegit els usuaris que tenim al fitxer.

- També podem crear usuaris nous creant un fitxer ldif nou, i agregant-lo com es mostra.

![GD5](GD5.png)
![GD6](GD6.png)

## ldapsearch

- Com hem creat diferents usuaris ara voldrem comprovar la seva existancia i ho farem amb un ```ldapsearch```

```
ldapsearch -xLLL -b "dc=sabate,dc=cat"
```

- Després d'això podem afegir diferents filtres per buscar segons uid, objectclass, email, el que ens interessi.

![GD7](GD7.png)
![GD8](GD8.png)

## ldapmodify

- Per modificar alguna entrada podem utilitzar un modify, podem modificar qualsevol informació que vulguem en aquest cas canviarem el cognom de l'usuari que hem creat.

![GD9](GD9.png)

```
ldapmodify -x -D "cn=admin,dc=sabate,dc=cat" -w marc -f fitxer.ldif
```
![GD10](GD10.png)

- Com es pot veure l'entrada dn: del nostre usuari no es correspont amb la modificació feta prèviament, per tant, també l'haurem de modificar, i ho farem de la següent forma.

![GD11](GD11.png)
![GD12](GD12.png)

- Finalment veurem com esborrar informació o bé es pot fer a través d'un ditxer ldif amb el modify o amb un ```ldapdelete```

## ldapdelete

- Finalment eliminarem l'usuari que hem creat, ja que no seguirà entre nosaltres.

```
ldapdelete -x -D "cn=admin,dc=sabate,dc=cat" -w contrasenya "cn=Reynolds,ou=users,dc=sabate,dc=cat"
```
![GD13](GD13.png)

# Servidors NFS
El NFS és un protocol que permet compartir fitxers i directoris RECURSOS a través d'una xarxa. Amb NFS, un servidor pot exportar directoris i els clients poden muntar aquests directoris com si fossin locals. Això és útil en entorns on múltiples màquines necessiten accedir als mateixos fitxers, com en entorns de treball en equip o en sistemes distribuïts.
En un entorn NFS, l'autenticació es fa a nivell de host, no a nivell d'usuari. Això vol dir que el servidor NFS confia en les màquines clients que tenen permís per accedir als directoris exportats.
Els usuaris poden tenir un directori home centralitzat que es munta automàticament en qualsevol màquina de la xarxa, gràcies a la integració de NFS i LDAP.

### Instal·lació part server

En aquesta part veurem con instal·lar NFS a la part del servidor, simplement obrim un terminal fem un update i un cop acabat procedim amb les següents comandes:

```
apt install nfs-kernel-server
```

![NFS1](NFS1.png)

Un cop instal·lat verifiquem que s'hagi fet correctament.

```
systemctl status nfs-server
```

![NFS2](NFS2.png)

Amb això ja tindrem el paquet NFS instal·lat.

### Instal·lació part client Ubuntu

A continuació veurem com instalar la part del client. Igual que amb el server primer fem un apt update i després instal·lem el següent paquet.
```
apt install nfs-common rpcbind
```

![NFS3](NFS3.png)

Amb aquest pas ja tenim la part del client ubunutu instal·lada.

### Instal·lació part client Windows

Per instal·lar la part del NFS a Windows ens hem de dirigir a la part del panell de control i un cop allà buscar programes i caracteristiques, a la part esquerra podrem activar les caracteristiques i activarem les que veurem a continuació.

![NFS4](NFS4.png)
![NFS5](NFS5.png)

# Servidor Samba
