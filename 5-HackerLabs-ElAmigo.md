# 🛡️ Write-Up de Análisis - EL AMIGO

## Introducción


Este laboratorio está orientado a la revisión y análisis de seguridad en sistemas Linux, con un enfoque práctico en la detección de cuentas, servicios y configuraciones sospechosas que podrían indicar la presencia de un atacante.

---

# ¿Qué aprenderás en este laboratorio?

- **Revisión de cuentas de usuario** en sistemas Linux (`/etc/passwd`)
- **Identificación de cuentas ocultas y masivas** creadas por un atacante
- **Análisis de privilegios** mediante UID/GID y pertenencia a grupos (sudo, grupos ocultos)
- **Detección de servicios maliciosos** configurados en *systemd*
- **Revisión de scripts ocultos** en directorios no estándar
- **Análisis de reglas de red** (`iptables`) para identificar redirecciones sospechosas
- **Investigación de persistencia** del atacante en el sistema


------

## 1.  ¿Nombre del servicio malicioso? (Ej; algo.algo)

Análisis de servicios en Linux: **/etc/systemd/system**

Respuesta correcta: **che.service**


### ¿Por qué revisar esta carpeta?

- `/etc` → Contiene la **configuración del sistema**.  
- `systemd/system` → Define las **unidades (servicios)** creadas o modificadas por el administrador.  
- Tiene **prioridad más alta** frente a `/lib` o `/usr/lib`.  

### Implicaciones de seguridad

- Los servicios en esta carpeta **sobrescriben** configuraciones de otros directorios.  
- Se **cargan automáticamente al arrancar** el sistema.  
- Son un punto clave para la **persistencia**, tanto legítima como maliciosa.  

<img width="600" height="181" alt="image" src="https://github.com/user-attachments/assets/d428a4cf-5d32-4dcd-91aa-deec1f75e9b2" />


---


## 2. ¿Cuántos usuarios ha creado el atacante? (Ej: 20)

Eso lo podemos observar de dos maneras:

<img width="400" height="64" alt="image" src="https://github.com/user-attachments/assets/a3273322-366e-47bc-a787-6db3396da1e0" />

En el directorio etc cat passwd

<img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/a537fd15-b68e-45c6-80d8-ad421fb92b21" />
<img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/e6932731-54fa-43d5-b5ca-8d96449e4083" />

Observamos que hay **51 usuarios** que han sido creados.  
La segunda forma sería más fácil: solo tenemos que ubicarnos en el directorio `/home` y hacer un simple listado con:

 ls -lisa /home

De esta manera veremos el listado completo de directorios de usuario. 

---

## 3.  ¿Cuál es el UID/GID de la cuenta creada por el atacante con privilegios? (EJ: 00000)
Para conocer el UID/GID de cada cuenta, revisamos el archivo /etc/passwd:

**UID/GID detectado: 65000**
Este valor es anómalo y debe considerarse un indicador de compromiso, ya que se encuentra fuera del rango habitual de usuarios legítimos.

---

## 4. ¿Desde qué script se redirige el tráfico? (Solo nombre, no ruta. Ej: algo.py)

Este script lo encontramos oculto **.hidden_challague10**
Lo podemos ver nuevamente de dos maneras una es fácil solo hay que ir al usuario Jony y hacemos un ls -lisa y vemo archivos ocultos y vemos que hay un **.bashrc** lo podemos catear o hacer nano

**Respuesta: redirect.sh**

<img width="600" height="130" alt="image" src="https://github.com/user-attachments/assets/1694af94-545d-4e57-9eb1-b7d9728748b9" />


La otra opción es ir directo al usuario challangue10 pero al momento de tratar de ingresar pedirá que hay que dar permisos se hace con chmod y se tiene que dar luego permisos de lectura también con chmod y se podrá observar y acceder al contenido de ese usuario:

sudo chmod +x /home/challangue10
sudo chmod +r /home/challangue10

y luego se observará el mismo script : .bashrc

---

## 5. ¿A qué IP se redirige el tráfico?

Esta pregunta hay que resolverla observando las tables de iptables donde podemos observar el tráfico entrante y saliendo y las ip a las cuales se han conectado, en el script no se observara ninguna ip y lo sé ya que después de 1 hora analizando el script, analizando que hacía y demás me di cuenta de que la ip correcta era: 0.0.0.0
Usamos sudo iptable -t nat -L -n -v, introducimos la contraseña jony2025 y nos da la información

<img width="600" height="287" alt="image" src="https://github.com/user-attachments/assets/6477f81a-d0f6-4845-b58e-7f17fef5b24f" />


Este comando muestra las reglas de la tabla NAT de iptables, que se usa para redirección de tráfico. Aquí te explico cada parte:
Sudo iptable -t nat -L -n -v
Iptables: herramientas para gestionar reglas firewall
-t nat especifica la table nat, usada para redireccion de IPs(DNAT/SNAT)
- L lista todas las reglas activas en la tabla
-n muestra IPs y puertos en formato números (sin resolver DNS)
-v muestra información detallada: número de paquetes byte y reglas.

¿Qué muestra?
•	Las cadenas de la tabla NAT: PREROUTING, POSTROUTING, OUTPUT
•	Las reglas activas en cada cadena
•	Cuántos paquetes y bytes han coincidido con cada regla
•	Qué IP de destino se está usando en redirecciones (DNAT)
•	Qué IP de origen se está enmascarando (SNAT, MASQUERADE)
