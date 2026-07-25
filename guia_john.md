# Guia John the Ripper

John the Ripper és una de les eines d'auditoria de contrasenyes i Hacking Ètic més utilitzades. En entorns de proves de penetració o auditories internes, permet avaluar la robustesa de les claus d'accés utilitzant atacs de diccionari o força bruta sobre *hashes* extrets de sistemes destinataris.

## Preparació de l'Entorn a Kali Linux**

Abans d'iniciar el procés de craqueig, cal assegurar-se que els fitxers d'entrada i el diccionari estan en el format i la ubicació correctes.

### Descompressió del diccionari RockYou

A Kali Linux, el diccionari rockyou.txt ve integrat per defecte, però es troba comprimit en format .gz per estalviar espai. Cal descomprimir-lo abans del seu primer ús:

```Bash  
sudo gzip \-d /usr/share/wordlists/rockyou.txt.gz
```

## Execució de l'Atac de Diccionari

Amb el diccionari descomprimit i el fitxer de targetes/hashes a punt, s'executa l'atac indicant la ruta del diccionari i el fitxer objectiu.

Bash  
john \--wordlist=/usr/share/wordlists/rockyou.txt passwords.txt

### Opcions d'optimització tècnica

* **Autodetecció de format:** Per defecte, John the Ripper analitza la capçalera dels hashes ($6$ per a SHA-512, $1$ per a MD5, $y$ per a yescrypt, etc.) per determinar l'algorisme de xifratge.  
* **Forçar format específic (--format):** Si es coneix l'algorisme exacte, especificar-ho redueix el temps de processament i millora el rendiment:  
  Bash  
  john \--format=sha512crypt \--wordlist=/usr/share/wordlists/rockyou.txt passwords.txt

### Atac "Single Crack" (Detectar contrasenyes evidents derivades de l'usuari)

El mode **Single Crack** és la primera fase recomanada en qualsevol auditoria. No utilitza cap diccionari extern, sinó que agafa els noms d'usuari i la informació associada (com els camps del nom complet o directori de l'arxiu /etc/passwd) i hi aplica regles de permutació (*mangling rules*): majúscules/minúscules, addició de números, inversió de caràcters, etc. És un atac extremadament ràpid i molt efectiu per detectar negligències bàsiques de seguretat en els servidors

#### Execució de l'atac

```Bash  
john \--single passwords.txt
```

### Atac Numèric per Màscara / Incremental (4 a 8 dígits)

Quan es vol avaluar la resistència del sistema davant de claus d'accés tipus PIN o patrons només numèrics (com dates o codis de seguretat), la millor opció és limitar l'espai de cerca a caràcters numèrics (0-9) i definir una longitud mínima i màxima, en aquest cas 4 a 8 dígits. Provarà sistemàticament totes les combinacions des de 0000 fins a 99999999.  

```Bash  
john \--incremental=Digits \--min-len=4 \--max-len=8 passwords.txt
```

>**Complexitat computacional:** L'espai de cerca total per a aquesta configuració és de $10^4 \+ 10^5 \+ 10^6 \+ 10^7 \+ 10^8 \= 111.100.000$ combinacions possibles, un volum que qualsevol CPU o GPU moderna resol en pocs segons o minuts segons l'algorisme de xifratge.

## Gestió i Consulta de Resultats

Mentrestant l'eina s'executa, es pot prémer qualsevol tecla al terminal per veure l'estat del procés (percentatge completat, hash/segons i temperatura/ús de CPU).

### Visualització de les contrasenyes trobades

Un cop finalitzat el procés (o per revisar els encerts obtinguts fins al moment), s'utilitza el paràmetre `\--show`:

```Bash  
john \--show passwords.txt
```

Les contrasenyes desxifrades s'emmagatzemen automàticament a l'arxiu d'historial de l'eina (\~/.john/john.pot), evitant haver de tornar a calcular hashes ja resolts en el futur.
