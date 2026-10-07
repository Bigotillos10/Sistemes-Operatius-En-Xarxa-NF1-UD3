# NF1.UD3: Usuaris i grups — Empresa "TechData S.L."


**Assignatura:** Sistemes Operatius en Xarxa  
**Alumne/a:** Biel Clave   
**Data d'entrega:** 16/10/2026
**Professor/a:** Carles Fugaroles 

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

```bash
sudo groupadd devs
sudo groupadd sysadmin
sudo groupadd auditor
```

# Comprovació de les últimes línies de /etc/group
tail -n 5 /etc/group
![tail -n 5 /etc/group](img/Captura%20de%20pantalla%202026-10-07%20202941.png)

### Tasca 2. Gestió dels perfils d'usuari (`/etc/skel`)

**Objectiu de l'acció**  
Configurar el directori plantilla `/etc/skel` per incloure un fitxer de benvinguda, la carpeta `Documents` i l'àlies `ll` a `.bashrc`. Validar `/etc/adduser.conf`, provar el funcionament amb un usuari de prova `provaXX` i eliminar-lo posteriorment.

**Comanda o configuració utilitzada**
```bash
# Preparació de la plantilla /etc/skel
sudo mkdir /etc/skel/Documents
sudo touch /etc/skel/benvinguda.txt
echo "alias ll='ls -la --color=auto'" | sudo tee -a /etc/skel/.bashrc
```

# Verificació de la shell per defecte a /etc/adduser.conf
```bash
grep "DSHELL" /etc/adduser.conf
```

# Creació, comprovació i eliminació de l'usuari de prova
```bash
sudo adduser provaXX
su - provaXX
ll
exit
sudo deluser -r provaXX 
```

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
```

### Tasca 4. Inspecció del sistema i fitxers de configuració

**Objectiu de l'acció**  
Inspeccionar les dades de l'usuari `marc_sys` amb la comanda `id`, analitzar les diferències d'informació i permisos entre els fitxers `/etc/passwd` i `/etc/shadow` per a `pau_dev`, i identificar el directori plantilla i el fitxer que en defineix la configuració.

**Comanda o configuració utilitzada**
```bash
# Inspecció de marc_sys
id marc_sys

# Consulta de pau_dev a /etc/passwd i /etc/shadow

grep pau_dev /etc/passwd
sudo grep pau_dev /etc/shadow
ls -l /etc/shadow

# Comprovació de la configuració de la plantilla SKEL
grep SKEL /etc/adduser.conf
```

### Tasca 5. Manteniment, bloqueig i eliminació

**Objectiu de l'acció**  
Bloquejar temporalment el compte de `pau_dev`, comprovar la marca de bloqueig al fitxer `/etc/shadow`, desbloquejar-lo de nou i eliminar l'usuari `auditor` de manera definitiva juntament amb el seu directori personal i bústia de correu.

**Comanda o configuració utilitzada**
```bash
# Bloquejar l'usuari pau_dev
sudo usermod -L pau_dev

# Verificar el bloqueig a /etc/shadow
sudo grep pau_dev /etc/shadow

# Desbloquejar l'usuari pau_dev
sudo usermod -U pau_dev

# Eliminar l'usuari auditor, el seu directori personal i bústia
sudo deluser --remove-home auditor
```

### Tasca 6. Personalització de l'entorn d'usuari

**Objectiu de l'acció**  
Instal·lar el shell `fish`, modificar el shell per defecte de l'usuari `laura_dev` a `/usr/bin/fish`, verificar el canvi al fitxer `/etc/passwd` i iniciar sessió amb `laura_dev` per comprovar el funcionament de l'entorn `fish`.

**Comanda o configuració utilitzada**
```bash
# Instal·lació del paquet fish
sudo apt update && sudo apt install -y fish

# Canvi de shell per a laura_dev
sudo usermod -s /usr/bin/fish laura_dev

# Comprovació a /etc/passwd i verificació d'accés
grep laura_dev /etc/passwd
su - laura_dev
``` 

## Conclusions i Valoració Personal

**Objectiu de l'acció**  
Realitzar un resum global del treball realitzat, valorar les dificultats trobades durant la configuració d'usuaris i grups a Ubuntu Server i verificar el compliment dels objectius fixats per a TechData S.L.

**Explicació i reflexió final**
- **Resum del treball realitzat:** S'ha creat i configurat correctament la jerarquia de grups (`devs`, `sysadmin`, `auditor`) i la totalitat dels usuaris sol·licitats, aplicant les polítiques de seguretat, la plantilla de perfils `/etc/skel` i la personalització dels entorns de treball.
- **Dificultats trobades:** [Comenta breument quines dificultats has tingut durant la pràctica, com les diferències de paràmetres entre `adduser` i `useradd` o la gestió de permisos].
- **Comprovació de criteris d'avaluació:** Es confirma que el servidor de l'empresa disposa d'un entorn d'usuaris i grups plenament funcional, segur i documentat segons els requisits establerts.