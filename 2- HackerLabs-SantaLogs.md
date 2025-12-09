# 🛡️ Write-Up  visor de eventos para analizar logs del sistema (SSH, advertencias sobre el disco)- Santa Logs

## 1. Introducción
Accederemos al visor de eventos para poder realizar el análisis, buscaremos los logs con nombre: Santalogs, al analizar todos los logs podemos sacar mucha información.

<img width="500" height="490" alt="image" src="https://github.com/user-attachments/assets/1ef6b0c9-f0d0-4b25-8a3c-19c9ebd88f03" />

---

## 2. ¿Qué IP realizó la conexión SSH exitosa?
Después de hacer el análisis observamos que la conexión exitosa se hizo desde la IP: 192.168.1.101, también observamos el id y la hora.
**Id: 1003 SSH_Login Successful SSH connection from 192.168.1.101: 11/12/2024 10:56:30**

<img width="500" height="434" alt="image" src="https://github.com/user-attachments/assets/acf49ce0-4565-4383-81c0-ac557689db04" />

---

## 3. ¿Cuántos intentos fallidos de acceso FTP están registrados?
Para poder observar la cantidad de intentos podemos agrupar por origen lo cual facilitaría observar la cantidad, vemos que hay una cantidad total de eventos de 216, y de **FTP_attack(50).**

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/f7f430bc-ef5e-4f21-9f3c-c6a6e66b85fa" />

---

## 4. ¿Qué mensaje de advertencia relacionado con el espacio en disco se registró en los logs?

Para esta información la encontraremos en los procesos simulados de información ya que es un mensaje informativo.
La advertencia es: Low Disk Space el cual tiene las características:
**Id: 5035 Process_Simulated 11/12/2024 10:55:47**

<img width="500" height="489" alt="image" src="https://github.com/user-attachments/assets/19a94572-b502-4caf-a2cc-fbc02cb7e4e9" />


<img width="500" height="462" alt="image" src="https://github.com/user-attachments/assets/a39be31a-5811-4564-b637-8e26e6d646bc" />

---

## 5. ¿Cuál es la clave para desencriptar el script malicious_script.py?

La clave es: **Id: 1005 Admin Note: The temporary access key is ‘FTP25_SMB192.168.1.101’ time: 11/12/2024 11:04:14**

<img width="500" height="279" alt="image" src="https://github.com/user-attachments/assets/b83e4605-fcce-4733-ad1b-e1aea8d22667" />

<img width="500" height="279" alt="image" src="https://github.com/user-attachments/assets/d9ac8086-ff30-41aa-a048-d03f11b4cf03" />

--- 

## 6. Flag

Para encontrar el flag necesitamos localizar donde esta, primeramente, al observar los eventos encontramos:

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/cea9ca34-6dd7-4a9e-9c28-5af0875e7873" />

Lo cual nos indica que hay un script que fue cargado en la carpeta **c:\tmp** llamado **malicious_Script.py**

<img width="500" height="400" alt="image" src="https://github.com/user-attachments/assets/2e1ed25e-d488-4bd2-9648-3c76bdb1eb8a" />

Al intentar abrirlo nos pide la clave AES:


<img width="500" height="123" alt="image" src="https://github.com/user-attachments/assets/fb429e6f-9fec-4c4c-90ee-dbf0c3d265de" />

La cual ya tenemos: **‘FTP25_SMB192.168.1.101’**
Al editarlo para ver su código fuente podemos ver:


<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/26e3e113-6d2a-4e34-ba2b-0a2ad4c2fab7" />

Que pide una clave y usa esa clave para intentar desencriptar un mensaje oculto.
Si la clave es buena → muestra el texto.
Si no → te dice que hay un error.
-	Ahora ejecutaremos el script desde PowerShell y lo descifraremos.

<img width="600" height="163" alt="image" src="https://github.com/user-attachments/assets/349b6168-be01-4f08-b365-f3d63165cdf8" />


Como se observa el mensaje oculto o la **FLAG: oscar_feliz_navidad**

**Extra: si observamos los LOGS de ftp podemos ver cómo se hicieron múltiples intenten con diferentes usuarios**

<img width="600" height="182" alt="image" src="https://github.com/user-attachments/assets/b62b332d-ce2b-45f2-a34c-f86762247145" />

---

## 7. 📅 Línea de Tiempo – Análisis de los eventos

Time Line: una vez analizados los logs, podemos determinar que se hicieron múltiples intentos de inicio de sesión de fuerza bruta, tanto como FTP-SMB-SSH,
se observa que Id: 1003 SSH_Login Successful SSH connection from 192.168.1.101: 11/12/2024 10:56:30 hubo un acceso exitoso, 
luego ocurrió un evento: Id: 1005 Admin Note: the temporary Access key is  ‘FTP25_SMB192.168.1.101’ time: 11/12/2024 11:04:15 y se observa que se cargó el script malicioso luego con el evento: Elf Alert:
Suspicious script ‘malicious_script.py’ detected in C:\tmp. It seems to require a special key for decryption! Id: 1007: 11/12/2024 11:22:39, el cual se ejecuta introduciendo la clave que se cargó anteriormente.


