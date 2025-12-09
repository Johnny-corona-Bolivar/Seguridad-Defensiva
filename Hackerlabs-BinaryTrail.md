# 🛡️ Write-Up de Análisis  - Binary Trail


  ## 1. Introducción

  Un binary trail es el conjunto de rastros, evidencias y cambios asociados a un archivo binario —tanto en reposo como durante su ejecución— que permiten su análisis y detección.

- **Una vez sabiendo lo que es, hay que saber en dónde es más probable que se pueda guardar la información de un binario:**

### 🗂️ Archivos de logs  
*(Es la ubicación más común donde se puede encontrar información útil sobre la actividad del sistema.)*

**Ruta principal:** `/var/log/`  
Aquí se almacenan la mayoría de los eventos del sistema:

- `/var/log/syslog` → Eventos generales del sistema  
- `/var/log/auth.log` → Autenticaciones, uso de sudo, escaladas de privilegios  
- `/var/log/kern.log` → Actividad del kernel  
- `/var/log/dpkg.log` → Instalación o modificación de paquetes  
- `/var/log/apt/history.log` → Histórico de software instalado o actualizado  


  
 ---- 

 ## 2 🔍. ¿Cuál es el nombre del binario sospechoso?

 - Para saber donde se encuentra tenemos que ir al directorio /opt/ debido que en Linux se usa para instalar software opcional, adicional o de terceros, que no forma parte del sistema base de Linux.


<img width="400" height="152" alt="image" src="https://github.com/user-attachments/assets/d961eb35-c8bb-4d4f-a0cb-d3f0d8c47a4f" />

Observamos que el binario es **auth_proxy**, al abrir el archivo

- Contenido del Archivo

<img width="600" height="231" alt="image" src="https://github.com/user-attachments/assets/dad868cb-f480-4a9b-8ab4-66bccbb95f37" />

- Contenido del Directorio Lab

<img width="400" height="243" alt="image" src="https://github.com/user-attachments/assets/e3508fc9-e290-4f7f-b03c-5c59b1cbde11" />

---

## 3🔍. ¿Qué archivo oculta el binario en el sistema?

Al descifrar el *string* del código de **auth_proxy**, obtenemos el siguiente comando: **touch /etc/.shadow_auth**

---

## 4🔍. ¿Qué comando dejó rastros el binario en los logs del sistema?


<img width="500" height="92" alt="image" src="https://github.com/user-attachments/assets/b423633d-af2f-41f4-8a71-47031000c702" />

`Cat /var/log/.auth.log.1`

<img width="684" height="397" alt="image" src="https://github.com/user-attachments/assets/cf98449f-fe89-4fc8-bbb3-401c32c4982a" />

Pero nos enfocamos en: **shadow_auth** , se observa que el comando es **Touch **

---

## 5🔍 . ¿En qué archivo de logs se encontraron los rastros? (RUTA)

- `/var/log/auth.log`
- `/var/log/auth.log.1`

---

## 6🔍. ¿Qué permisos tiene el archivo oculto /etc/.shadow_auth? (Numérico)

<img width="700" height="73" alt="image" src="https://github.com/user-attachments/assets/0b5766eb-ee2e-4186-8443-f3ca61f89263" />

-rw------ es igual a 600




 

 

  
