# 🛡️ Write-Up de Análisis de Trafico - LA CORPORACION

## 1. Introducción
Al analizar los archivos **PCAP** proporcionados, se observó que la mayor parte de la actividad maliciosa ocurrió el día **domingo**.  
A continuación se detallan las respuestas a cada una de las preguntas planteadas, así como un análisis global del incidente.

<img width="886" height="172" alt="image" src="https://github.com/user-attachments/assets/3f96e528-c203-4ecc-82d6-0da36cdc6e64" />


---

## 2. ¿Cuántos intentos de conexión SSH realizó el atacante?
- El atacante realizó un total de **10 intentos de conexión SSH**.  
- Esto se observó aplicando un filtro por protocolo **SSH** en Wireshark.
  
 <img width="886" height="174" alt="image" src="https://github.com/user-attachments/assets/cba4e6db-12fa-475d-9bf9-c2488fe99a60" />

- También se confirma mediante la vista **Protocol Hierarchy**, donde se detalla la cantidad de paquetes asociados a cada protocolo.
  
<img width="886" height="156" alt="image" src="https://github.com/user-attachments/assets/0473fd26-d1c5-4434-9bda-b0fe731493bf" />


---

## 3. ¿Qué puerto utilizó el atacante en el intento de intrusión?
- El atacante utilizó el **puerto 22**, correspondiente al protocolo **SSH**.
  
<img width="886" height="184" alt="image" src="https://github.com/user-attachments/assets/682ab500-5d67-49fa-b192-6d6555180b6d" />


---

## 4. ¿Qué protocolo se utilizó para el intento de intrusión?
- Aplicando un filtro por la dirección IP maliciosa, se observó tráfico asociado al protocolo **HTTP**.  
- Se intentó interactuar con el servidor utilizando este servicio.
<img width="886" height="305" alt="image" src="https://github.com/user-attachments/assets/d88980d7-4a2c-468b-b741-5cdcb18a551b" />


---

## 5. ¿Qué nombre tiene el archivo que el atacante intentó ejecutar?
- El archivo observado fue:  
  **`CVE-2021-41773.sh`**
 <img width="886" height="249" alt="image" src="https://github.com/user-attachments/assets/1f222f14-1922-40c6-b41f-991e4069f91b" />


### 📌 Descripción de la vulnerabilidad (resumen)
La vulnerabilidad **CVE-2021-41773** afecta a **Apache HTTP Server 2.4.49** y permite:
- Path traversal  
- Acceso a archivos fuera de los directorios permitidos  
- Ejecución remota de código (**RCE**) si los alias permiten ejecución de scripts  

**Fuente:** NVD (National Vulnerability Database)  --> https://nvd.nist.gov/vuln/detail/cve-2021-41773

---

## 6. ¿Qué día se registró el intento de intrusión?
- El intento ocurrió el día **domingo 2024-11-28 a las 13:54:28**, según la información obtenida de las propiedades del archivo y el tráfico de red.
  
<img width="886" height="634" alt="image" src="https://github.com/user-attachments/assets/c2c7a27c-61f9-4573-bf32-9001a2a192b0" />


---

## 7. ¿Cuál es la dirección IP desde donde provino el ataque?
- El ataque provino desde la dirección IP:  
  **192.168.1.100**
  
<img width="647" height="484" alt="image" src="https://github.com/user-attachments/assets/a8ccd3f4-993a-448c-93f4-a2ded7ab9ea0" />


---

## 8. ¿Qué host utilizó el atacante en el tráfico HTTP malicioso?
- El host identificado fue:  
  **attacker.internal**
 <img width="886" height="297" alt="image" src="https://github.com/user-attachments/assets/f71a4105-7828-400f-887f-bf20535cfc5d" />


---

## 9. 📅 Línea de Tiempo – Análisis del Incidente
Se construyó el siguiente análisis a partir de tres archivos **PCAP** correspondientes a fechas entre **2024-11-26 (viernes)** y **2024-11-28 (domingo)**.

- 🕒 **12:54:28 – Actividad maliciosa detectada**  
  - Tráfico proveniente desde la IP **192.168.1.100**.  
  - Conexiones hacia múltiples destinos por el puerto **22 (SSH)** --- **Fuerza Bruta**
  - Múltiples intentos de conexión en un periodo muy corto → **fuerza bruta SSH**.

- 🕒 **Tráfico HTTP malicioso detectado**  
  - Tráfico HTTP asociado al host **attacker.internal**.  
  - Intento de ejecutar el archivo **CVE-2021-41773.sh**, vinculado a la vulnerabilidad **Apache Path Traversal / RCE**, que permite al atacante obtener control del endpoint.

---

## 10. 🛠️ Medidas Tomadas
Las acciones de contención recomendadas y aplicadas fueron:

- Bloquear la dirección IP de origen (**192.168.1.100**).  
- Mejorar y reforzar la configuración del **firewall**, filtrando la comunicación por el puerto involucrado.  
- Revisión de reglas existentes para evitar accesos no autorizados.  
- Bloquear la descarga o ejecución de archivos de fuentes desconocidas.  
- Revisión adicional de **logs** para descartar movimientos laterales del atacante.

---
