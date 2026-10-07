# NF1.UD3: Usuaris i grups — Empresa "TechData S.L."


**Assignatura:** Administració de Sistemes Operatius en Xarxa  
**Alumne/a:** [El teu nom i cognoms]  
**Grup / Num. Llista:** [Ex: ASIX1 - 12]  
**Data d'entrega:** [Data de lliurament]  
**Professor/a:** [Nom del professor/a]  

---

## Índex
1. [Enunciat i Presentació de l'Activitat](#enunciat-i-presentació-de-lactivitat)
2. [Objectius i Criteris d'Avaluació](#objectius-i-criteris-davaluació)
3. [Tasca 1. Creació de la jerarquia de grups](#tasca-1-creació-de-la-jerarquia-de-grups)
4. [Tasca 2. Gestió dels perfils d'usuari (/etc/skel)](#tasca-2-gestió-dels-perfils-dusuari-etcskel)
5. [Tasca 3. Creació i configuració d'usuaris](#tasca-3-creació-i-configuració-dusuaris)
6. [Tasca 4. Inspecció del sistema i fitxers de configuració](#tasca-4-inspecció-del-sistema-i-fitxers-de-configuració)
7. [Tasca 5. Manteniment, bloqueig i eliminació](#tasca-5-manteniment-bloqueig-i-eliminació)
8. [Tasca 6. Personalització de l'entorn d'usuari](#tasca-6-personalització-de-lentorn-dusuari)
9. [Conclusions i Valoració Personal](#conclusions-i-valoració-personal)

---

## Enunciat i Presentació de l'Activitat

L'empresa **"TechData S.L."** ha instal·lat un nou servidor Linux (Ubuntu Server) i necessita configurar l'estructura d'usuaris i directoris de treball per a dos departaments:
**Departament de Desenvolupament:** devs
**Departament de Sistemes:** sysadmin
**Auditors externs:** auditor

Com a tècnic/a informàtic/a de sistemes de l'empresa, la teva tasca és crear els usuaris, assignar-los als seus grups corresponents, configurar la seguretat inicial i organitzar l'arbre de directoris compartits.

---


## Objectius i Criteris d'Avaluació


Crear i gestionar grups principals i secundaris (groupadd, groupmod, gpasswd).
Crear i gestionar comptes d'usuari (adduser, useradd, usermod, passwd).
Comprendre la funció i estructura dels fitxers /etc/passwd, /etc/group i /etc/shadow.
Configurar directoris de treball compartits amb permisos de grup adients (mkdir, chown, chmod).
Bloquejar, desblocar i eliminar usuaris de manera segura.


---


### Tasca 1. Creació de la jerarquia de grups


**Objectiu de l'acció**  
Crear els grups del sistema sol·licitats (devs, sysadmin, auditor) i verificar la seva correcta creació consultant les últimes línies del fitxer /etc/group per identificar el GID de cadascun.


**Comanda o configuració utilitzada**
bash
# Creació dels tres grups requerits
sudo groupadd devs
sudo groupadd sysadmin
sudo groupadd auditor


# Comprovació de les últimes línies de /etc/group
tail -n 5 /etc/group


### Tasca 2. Gestió dels perfils d'usuari (`/etc/skel`)

**Objectiu de l'acció**  
Configurar el directori plantilla `/etc/skel` per incloure un fitxer de benvinguda, la carpeta `Documents` i l'àlies `ll` a `.bashrc`. Validar `/etc/adduser.conf`, provar el funcionament amb un usuari de prova `provaXX` i eliminar-lo posteriorment.

**Comanda o configuració utilitzada**
```bash
# Preparació de la plantilla /etc/skel
sudo mkdir /etc/skel/Documents
sudo touch /etc/skel/benvinguda.txt
echo "alias ll='ls -la --color=auto'" | sudo tee -a /etc/skel/.bashrc

# Verificació de la shell per defecte a /etc/adduser.conf
grep "DSHELL" /etc/adduser.conf

# Creació, comprovació i eliminació de l'usuari de prova
sudo adduser provaXX
su - provaXX
ll
exit
sudo deluser -r provaXX

### Tasca 3. Creació i configuració d'usuaris

**Objectiu de l''acció**  
Crear els usuaris `pau_dev` i `laura_dev` amb `adduser`, i `marc_sys` i `auditor` amb `useradd`. Assignar els seus grups principals/secundaris, establir la contrasenya inicial `Canviam2026!` a tots ells i forçar el canvi de contrasenya a `pau_dev` en el pròxim inici de sessió.

**Comanda o configuració utilitzada**
```bash
# Creació amb adduser
sudo adduser --ingroup devs pau_dev
sudo adduser --ingroup devs laura_dev

# Creació amb useradd
sudo useradd -m -g sysadmin -G devs -s /bin/bash -c "Marc Soler" marc_sys
sudo useradd -m -g auditor -s /bin/bash -c "Usuari Auditor" auditor

# Assignació de contrasenya inicial
echo "pau_dev:Canviam2026!" | sudo chpasswd
echo "laura_dev:Canviam2026!" | sudo chpasswd
echo "marc_sys:Canviam2026!" | sudo chpasswd
echo "auditor:Canviam2026!" | sudo chpasswd

# Obligar el canvi de contrasenya a pau_dev
sudo chage -d 0 pau_dev