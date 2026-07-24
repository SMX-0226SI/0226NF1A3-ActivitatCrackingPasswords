# NF1AA3 Activitats Seguretat Lògica

## Presentació de l'activitat

En aquesta activitat haureu de fer un

## Descripció de l'activitat

Teniu a la Comuna una màquina OVA que conté la màquina virtual (Ubuntu Server) de la qual necessitem saber les contrasenyes dels usuaris que hi poden iniciar sessió. Per aconseguir aquest objectiu, caldrà fer un atac en dues fases:

1. Aconseguir accés a la màquina virtual com administrador (root) per poder descarregar-nos els arxius necessaris per poder fer l'atac: `/etc/shadow` i `/etc/passwd`.

2. Amb aquests dos arxius i usant la distribució de seguretat [Kali Linux](https://www.kali.org/), aconseguir les contrasenyes dels usuaris. Per fer-ho, usarem "John the Ripper", en concret amb l'eina gràfica "Johnny".

3. Un cop aconseguides les contrasenyes, verificarem que podem iniciar sessió amb els usuaris i les contrasenyes obtingudes.

### Configuració de l'escenari

El primer que caldrà fer, serà importar la màquina virtual OVA a la vostra màquina física, la interfície de xarxa ha d'estar en "xarxa NAT", d'aquesta manera podreu comunicar la màquina virtual objectiu amb la màquina Kali.

A continuació, cal que instal·leu Kali Linux en una màquina virtual, també amb la interfície de xarxa en "xarxa NAT".

> A un escenari real, no caldria que les dues màquines estiguessin en la mateixa xarxa, es podria usar un pendrive per copiar els arxius necessaris de la màquina víctima a Kali.

### Accés a la màquina virtual

1. Reinicia la màquina virtual Ubuntu Server i aconsegueix accedir com a root. Usa la guia que trobaràs al primer enllaç [1](https://waytoit.wordpress.com/2013/06/06/recuperando-password-en-ubuntu/) de la secció "Recursos" per aconseguir-ho.

2. Comprova si el servei ssh està actiu (`systemctl status ssh`), sinó instal·la'l amb la comanda `sudo apt install ssh`.

3. Copia usant la comanda `scp` els arxius `/etc/shadow` i `/etc/passwd` a la màquina Kali. Per exemple:

   ```bash
   scp /etc/shadow user@kali_ip:.
   scp /etc/passwd user@kali_ip:.
   ```

Assegura't de substituir `user` per l'usuari de Kali i `kali_ip` per la IP de la màquina Kali.

> 💡Fixeu-vos que teniu **control total** de la màquina, ja que esteu accedint com a `root`. És un bon recordatori de la importància de la seguretat en diverses capes, simplement protegir amb contrasenya no seria suficient. Caldria aplicar capes adicionals com:
>
> - Control d'accés físic a la màquina.
> - Protegir el GRUB amb contrasenya per evitar aquest atac tant senzill [2](https://soloconlinux.org.es/securizando-grub/).

### Preparar l'arxiu per l'atac

Ara ja tenim els arxius a la màquina Kali i es pot començar a preparar l'atac.

1. Convertim els dos arxius en un sol arxiu estàndard de contrasenyes amb la comanda:

   ```bash
   unshadow passwd shadow > mypasswd.txt
   ```

2. John the Ripper suporta diversos tipus d'atacs:

    - Bàsic: prova coses tant òbvies com com contrasenyes que coincidin amb el nom d'usuari, contrasenyes buides, etc.
    - Força bruta: prova totes les combinacions possibles de caràcters, definint un conjunt de caràcters i una longitud màxima.
    - Diccionari: prova amb una llista de paraules (diccionari) i les seves variants.

## Enllaços d'interès

1. [WaytoIT Blog: Recuperando password en Ubuntu](https://waytoit.wordpress.com/2013/06/06/recuperando-password-en-ubuntu/)

2. [SoloConLinux: Securizando GRUB](https://soloconlinux.org.es/securizando-grub/)