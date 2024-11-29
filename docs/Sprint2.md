## Introducció
En aquest sprint veurem com es gestionen els usuaris, grups  i permisos d'un sistema en Linux. També configurarem les polítiques de seguretat per a comptes d'usuaris i per al sistema. Instal·larem i configurarem serveis i processos essencials del sistema operatiu, crearem i administrarem sistesmes de fitxers i noces particions. Implementarem un sistema de còpies de seguretat i per últim farem proves per comprovar el funcionament del sistema. 
# Gestió de processos


# Gestió d'usuaris grups i permisos
## - Commandes terminal i accessos als directoris
Per a accedir al terminal normalment ho faremt amb un crtl + alt + t, així obrim el pseudo terminal. El terminal com a tal l'obrirem al el ctrl dret + F3, aquest es un terminal TTY. Un pseudo terminal es com un emulador on les comandes que posem son interpretades per algún arxiu al que fan referencia i aquest fa els procediments, en canvi amb un terminal si que estem amb contacte directe amb el sistema.

- En primer lloc per comprovar tots els usuaris que conte el nostre sistema ho farem amb la següent comanda. Els usuaris que es poden controlar per interfície gràfica es troben a partir del número 1000.
```
nano /etc/passwd
```
![gestiousu1](gestiousu1.png)
- A continuació podem veure els grups dels usuaris i l'identificador de qui es el seu administrador.
```
nano /etc/group
```
![gestiousu2](gestiousu2.png)
- A més a més també podem consultar les contrasenyes dels usuaris, això si encriptades i la seva caducitat.
```
nano /etc/shadow
```
![gestiousu3](gestiousu3.png)
- Per últim tenim una comanda que ensenya les contrasenyes del grup i la seva administració, també encriptat.
```
nano /etc/gshadow
```
![gestiousu4](gestiousu4.png)

## - Creació  d'usuaris
Per crear usuaris tenim diverses formes de fer-ho, el més important es tenir clar que amb certes comandes no es creen directoris sol usuaris. A continuació es mostrarà com crear usuaris.

- En primer lloc utilitzarem la següent comanda, aquesta ens creara un usuari pero fins que no fessim un log in amb aquest no ens crearà els directoris.
```
adduser usuari
```
![gestiousu5](gestiousu5.png)
- A continuació si volem crear l'usuari amb els seus directoris podem crear-lo amb la segûent comanda.
```
useradd usuari
```
![gestiousu6](gestiousu6.png)
- Per afegir els directoris haurem d'utilitzar la següent comanda.
```
mkdir usuari
```
![gestiousu7](gestiousu7.png)
![gestiousu8](gestiousu8.png)
- Si volem canviar el nom de l'usuari podem utilitzar la següent comanda. EN el cas que hi ha a continuació volem canviar el nom d'usuari de porva2, ja que hauria de ser prova2.
```
usermod -l usernou user
```
![gestiousu15](gestiousu15.png)
![gestiousu16](gestiousu16.png)
![gestiousu17](gestiousu17.png)
- En cas de voler eliminar accés a un usari ho podem fer bloquejant-lo, la comanda seria la següent. Una forma de comprovar que s'ha bloquejat correctament es entrar a la carpeta de passwd i veurem que la contrasenya del usuari al davant té un signe d'exclamació "!"
```
usermod -L usuari
```
![gestiousu9](gestiousu9.png)
![gestiousu10](gestiousu10.png)
- Seguint amb la creació d'usuaris, si tenim un usuari bloquejat i el volem recuperar utilitzarem aquesta comanda. A l'hora de comprovar-ho ens fixarem que ja no te l'exclamació.
```
usermod -U usuari
```
![gestiousu13](gestiousu13.png)
- Si realment el que volem es eliminar l'usuari de forma més permanent ho farem amb la següent comanda. Per comprovar si l'hem eliminat correctament podem entrar a la carpeta passwd i veurem que l'usuari ja no hi es.
```
deluser usuari
```
![gestiousu11](gestiousu11.png)
![gestiousu12](gestiousu12.png)

- Per complementar una mica les comandes que tenim, aquí en tenim algunes que ens poden ajudar també, la següent elimina la "home" de l'usuari.
```
rm -r usuari
```
- Un dels problemes habituals es que no eliminem les homes i directoris dels usuaris, amb la següent comanda ho podem fer tot a la vegada.
```
userdel -r usuari
```
- En cas de voler consultar algun tipus d'informació sobre algun usuari, podem fer-ho així.
```
id usuari
```
![gestiousu18](gestiousu18.png)

## - Creació de grups
- En aquest apartat veurem com funcionen els grups d'usuaris, com hem vist anteriorment podem consultar els grups i les seves contrasenyes. Important dir que quan es crea un usuari també es crea un grup amb el nom d'aquest. 
- Per crear un grup nou ho podem fer amb una senzilla comanda.
```
addgroup grup
```
![gestiogrup](gestiogrup.png)
![gestiogrup1](gestiogrup1.png)

- D'altra banda si el que volem es afegir un usuari a un grup existent ho podem fer d'aquesta manera.
```
adduser usuari grup
```
![gestiogrup2](gestiogrup2.png)
![gestiogrup3](gestiogrup3.png)

- L'operació anterior també es pot fer amb aquesta comanda.
```
gpasswd -a usuari grup
```
- Per fer a un usuari administrador d'aquell grup ho podem fer així. Dins de la carpeta gshadow podem veure els usuaris i administradors dels grups, per reconeixels veurem la posició que tenen entre els ":", l'alumne 2 es administrador d'asix1 i membre d'asix2.
```
gpasswd -A usuari grup
```
![gestiogrup4](gestiogrup4.png)
![gestiogrup5](gestiogrup5.png)

- Un dels majors problemes es que quan afegim usuaris a grups amb les comandes anteriors els treu del grup on estaven, per evitar-ho hem d'utilitzar la següent comanda. Com comprovarem l'usuari alumne2 forma part dels dos grups d'asix i es administrador del primer.
```
usermod -a -G grup usuari 
```
![gestiogrup6](gestiogrup6.png)
![gestiogrup7](gestiogrup7.png)

- Per eliminar un usuari d'un grup ho farem com veurem a continuació. En aquest cas l'alumne2 ja te prous responsabilitats i l'eliminarem del grup d'asix1.
```
gpasswd -d usuari grup
```
![gestiogrups8](gestiogrups8.png)
![gestiogrup9](gestiogrup9.png)
- L'anterior comanda també es pot fer amb aquesta altra
```
deluser usuari grup
```
![gestiogrup10](gestiogrup10.png)

- Per fer un usuari el principal d'un grup en concret utilitzarem el següent.
```
usermod -g grup usuari
```
- En cas de voler canviar el nom d'algun grup ho farem amb la següent comanda.
```
groupmod -n grupnou grupvell
```
![gestiogrup11](gestiogrup11.png)
![gestiogrup12](gestiogrup12.png)




# Gestió de permisos
En els casos d'un sistema multiusuari on vulguem que diferents usuaris tinguin certs permisos però no els mateixos, és important fer una bona gestió d'aquestos. Hi han vaires maneres de gestionar-ho, i les veurem a conitnuació.

## Permisos estandars
- Els permisos estandars son una serie de permisos bàsics que es poden donar a tots els usuaris i grups. Els grups poden tenir diferents permisos respecte als usuaris, una forma de comprovar això ho podem fer de la següent forma.
```
ls -l 
```
![permis](permisbasic.png)
- En aquest cas podem observar que el primer root que apareix és de l’usuari i el segon és del grup principal. També surt la data de creació i noms de directori, però la part important és al principi.
![permis1](permisbas1.png)
-  Aquí es on podem apreciar els permisos que hi han dins dels directoris. Després de la lletra d, els primers permisos són els d'usuari "rwx" en aquest cas, això vol dir que por llegir, escriure i executar, bàsicament te tots els permisos. Després els següents permisos son els de grup equivalents a les 3 lletres següents: "r-x", en aquest cas com podem veure no te permisos per escriure. I per últim tenim els ultims 3 caràcters que equivalen a altres, usuaris que no són ni usuari principal ni formen part del grup principal, en aquest cas: "r-x", la mateixa situació que abans no poden escriure però si llegir i executar. Aquest exemple es amb "root".
- A continuació veurem com nosaltres podem agregar permisos als usuaris i grups. 
```
chmod -R
```
- Opcions de fitxer/carpeta
```
chrgp -R
```
- Grup propietari fitxer/carpeta
```
chown - R
```
- Propietari fitxer/carpeta.
## Permisos especials
el suid - es fa un script per a que els usuaris que no siguin root facin una comanda unica amb permisos únics
sticky
sgid  

# Sistemes de fitxers i particions

# Copia de seguretat i automatització de tasques

# Quotes de disc
