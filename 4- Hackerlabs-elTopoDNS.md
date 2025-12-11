# 🛡️ Write-Up de Análisis DNS- EL TOPO DNS

## 1. Introducción
El laboratorio resulta especialmente interesante por la gran cantidad de peticiones registradas, lo que obliga a trabajar de manera meticulosa y a mantener una atención constante para evitar que cualquier detalle pase desapercibido.

### Descripción + ¿Qué aprenderás en este laboratorio?
---

- **Análisis de logs web** — correlación de GET/POST para identificar punto de entrada (`upload.php`)
- **Detección de beaconing y exfiltración por DNS** — consultas `1.beacon.c2.eltopo.thl` y subdominios `*.data.eltopo.thl`
- **Movimiento lateral / pivoting vía FTP** — autenticación `devuser:developer123` contra `10.0.0.50`
- **Exfiltración de archivo interno** — robo de `client_database_backup.zip` por FTP

---

## 2. ¿Qué dirección IP externa sirvió el _stager_ `p.sh` al servidor web?

Lo primero que se hace es buscar en `access.log`, ¿pero por qué?  

Porque allí es donde se almacenan los **logs web** con peticiones **GET** y **POST** en sistemas Linux.

Si utilizas únicamente:

```bash
cat access.log
````

aparecerán todas las peticiones hechas al servidor, lo que dificultaría encontrar la respuesta rápidamente.

🔹 Consejo: Sin embargo, si solo nos enfocamos en la respuesta final, perderíamos la oportunidad de analizar y aprender a interpretar todas las solicitudes del servidor, lo cual es fundamental para mejorar nuestras habilidades de análisis.

<img width="500" height="457" alt="image" src="https://github.com/user-attachments/assets/20c20e84-c1ca-4dd1-a9f1-cdb67b54650c" />

Ahora en mi caso particular lo analicé lo mejor que pude, pero al ver que es una mucha cantidad decidí filtrar con grep.

```bash
Cat Access.log | more | grep “p.sh”
````

<img width="700" height="108" alt="image" src="https://github.com/user-attachments/assets/95d4b773-d412-49ac-982d-bf7249b92bf4" />

Y obtenemos que el servidor desde la IP **192.168.1.10** hizo una solicitud atraves del método GET a la IP **162.248.1.100**

---

## 3. ¿Qué fichero PHP (solo nombre) fue el punto de entrada más probable de la explotación inicial?

Tras analizar todo el archivo durante un largo periodo de tiempo, se encuentra finalmente una petición **POST**.  
Sin embargo, si analizamos la pregunta, nos piden identificar **el punto de entrada más probable**. Yo lo interpreto como el lugar por donde se envió la explotación inicial.

Al aplicar el filtrado correspondiente, aparece la información del fichero involucrado: **`upload.php`**.

<img width="700" height="122" alt="image" src="https://github.com/user-attachments/assets/0e7f5107-8d38-4d4d-83d8-4f056cd73cc5" />

🔹 Recordatorio: Hay que recordar que el método post no muestra la información en la URL lo cual lo hace ideal para ocultar información o introducir información al servidor.

## 4. ¿Cuál es el FQDN de la primera consulta de _beaconing_ de C2 observada en los logs?

### FQDN — ¿Qué es?

El **FQDN** (Fully Qualified Domain Name) es el nombre completo de un dominio en Internet, incluyendo todas sus partes.

**Estructura:**
`host + subdominio + dominio + TLD`

---

#### Ejemplo 1
**www.google.com**

**Desglose:**
- **www** → nombre del host  
- **google** → dominio  
- **com** → TLD  



#### Ejemplo 2
**mail.empresa.interna.local**

**Desglose:**
- **mail** → host  
- **empresa.interna** → subdominios  
- **local** → dominio principal

Ahora, al revisar la información de la máquina, vemos que existe un archivo llamado **dns.log**.  
Por lo tanto, si estamos buscando un dominio, es allí donde lo encontraremos.

Para analizarlo, usamos el mismo procedimiento:

```bash
cat dns.log | more
```
<img width="500" height="277" alt="image" src="https://github.com/user-attachments/assets/f7bdb257-5fb5-4af2-9c67-b09b425d8326" />

- El que nos concierne es el siguiente:

<img width="500" height="113" alt="image" src="https://github.com/user-attachments/assets/abe585de-cfa6-42b0-9a67-ad40edb03b08" />

**1.beacon.c2.eltopo.thl Por lo tanto, al analizarlo vemos que es un Command and Control**

---

## 5. ¿Cuál es el dominio (solo el dominio, sin subdominios de datos) usado para exfiltrar el fichero shadow?

Esta pregunta se me complico debido a que el domingo usado fue: **1.beacon.c2.eltopo.thl** pero piden sin subdominio si buscamos más información en el dns.log observamos que hay otro dominio 

<img width="700" height="144" alt="image" src="https://github.com/user-attachments/assets/299a6502-85d5-451f-b53e-e4e8759a7a2f" />

Ya que solo nos pide el dominio por lo tanto es **data.eltopo.thl**

---

## 6. ¿Qué servicio de red (protocolo) usó el atacante para pivotar al servidor interno 10?0.0.50?

El protocolo usado es FTP, al observar los archivos vemos que hay uno llamado **ftp.log** al analizarlo observaremos la demás respuesta del CTF

<img width="700" height="295" alt="image" src="https://github.com/user-attachments/assets/c0f983f3-3ac4-4a87-a1d8-16c836a2ded8" />


---

## 7. ¿Qué nombre de usuario se utilizó para autenticarse en el servidor interno?

El usuario usado es **USER: devuser**, el cual la información se encuentra en **ftp.log**

<img width="700" height="295" alt="image" src="https://github.com/user-attachments/assets/5eef3a83-8a39-405b-8ca7-76a3789d342b" />


---

## 8. ¿Qué contraseña se utilizó para el movimiento lateral exitoso?

**PASS: developer123**

---

##9. ¿Cuál es el nombre de fichero exacto que el atacante robó del servidor interno?

**Client_database_backup.zip**







