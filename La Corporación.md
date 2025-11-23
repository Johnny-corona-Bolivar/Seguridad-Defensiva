# 🛡️ Write-Up de Análisis de Intrusión

## 1. Introducción
Al analizar los archivos **PCAP** proporcionados, se observó que la mayor parte de la actividad maliciosa ocurrió el día **domingo**.  
A continuación se detallan las respuestas a cada una de las preguntas planteadas, así como un análisis global del incidente.

---

## 2. ¿Cuántos intentos de conexión SSH realizó el atacante?
- El atacante realizó un total de **10 intentos de conexión SSH**.  
- Esto se observó aplicando un filtro por protocolo **SSH** en Wireshark.  
- También se confirma mediante la vista **Protocol Hierarchy**, donde se detalla la cantidad de paquetes asociados a cada protocolo.

---

## 3. ¿Qué puerto utilizó el atacante en el intento de intrusión?
- El atacante utilizó el **puerto 22**, correspondiente al protocolo **SSH**.

---

## 4. ¿Qué protocolo se utilizó para el intento de intrusión?
- Aplicando un filtro por la dirección IP maliciosa, se observó tráfico asociado al protocolo **HTTP**.  
- Se intentó interactuar con el servidor utilizando este servicio.

---

## 5. ¿Qué nombre tiene el archivo que el atacante intentó ejecutar?
- El archivo observado fue:  
  **`CVE-2021-41773.sh`**

### 📌 Descripción de la vulnerabilidad (resumen)
La vulnerabilidad **CVE-2021-41773** afecta a **Apache HTTP Server 2.4.49** y permite:
- Path traversal  
- Acceso a archivos fuera de los directorios permitidos  
- Ejecución remota de código (**RCE**) si los alias permiten ejecución de scripts  

**Fuente:** NVD (National Vulnerability Database)

---

## 6. ¿Qué día se registró el intento de intrusión?
- El intento ocurrió el día **domingo 2024-11-28 a las 13:54:28**, según la información obtenida de las propiedades del archivo y el tráfico de red.

---

## 7. ¿Cuál es la dirección IP desde donde provino el ataque?
- El ataque provino desde la dirección IP:  
  **192.168.1.100**

---

## 8. ¿Qué host utilizó el atacante en el tráfico HTTP malicioso?
- El host identificado fue:  
  **attacker.internal**

---

## 9. 📅 Línea de Tiempo – Análisis del Incidente
Se construyó el siguiente análisis a partir de tres archivos **PCAP** correspondientes a fechas entre **2024-11-26 (viernes)** y **2024-11-28 (domingo)**.

- 🕒 **12:54:28 – Actividad maliciosa detectada**  
  - Tráfico proveniente desde la IP **192.168.1.100**.  
  - Conexiones hacia múltiples destinos por el puerto **22 (SSH)**.  
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
