# UT2 - AE202: Análisis Forense de la Memoria RAM

## 1. Introducción y Objetivo
El objetivo es hacer una copia exacta de todo lo que había guardado en la memoria RAM de mi ordenador en un momento concreto y analizarla después con programas forenses gratuitos. El objetivo es descubrir qué programas estaban abiertos, qué conexiones a internet había activas y comprobar si existía algún proceso sospechoso oculto en el sistema.

---

Iniciamos el `.exe` del MemProcFS en la carpeta donde la tengamos ubicada.

<img width="665" height="1032" alt="Screenshot_1" src="https://github.com/user-attachments/assets/0e3f275e-ce90-49e3-8365-46c641b5addc" />

---

Ejecuté el comando `MemProcFS.exe -device PMEM`, pero me da un error y me pide que hay que instalar el **dokany**. Esto sirve para que Windows pueda mostrar la memoria RAM como si fuera un disco duro o un pendrive más.

<img width="683" height="106" alt="Screenshot_2" src="https://github.com/user-attachments/assets/b9a178d9-90f8-4e7d-bd91-b39cac6daec6" />

---

<img width="1572" height="919" alt="Screenshot_3" src="https://github.com/user-attachments/assets/be4396de-b909-4043-93e7-067c769bef6e" />

---

Cuando vuelvo a colocar el comando vemos que el error del dokany desaparece y me sale un error de python al no tenerlo instalado. Así que instalamos el python y ya el programa detectó mi sistema **(Windows 10.0.26100 de 64 bits)** y creó automáticamente una unidad virtual nueva llamada `M:\.`

<img width="736" height="712" alt="Screenshot_4" src="https://github.com/user-attachments/assets/43ef7bf1-7566-4e6b-bbb9-b4966652fb2f" />

---

## 2. Resultados del Análisis con Volatility

### 2.1. Identificación del perfil (`imageinfo`)
* **Función del comando:** Muestra los datos principales del sistema operativo analizado, como la versión exacta de Windows y la fecha y hora en la que se tomó la copia de la memoria.
* **Observaciones:** Se comprueba que el ordenador usa **Windows de 64 bits** (`Is64Bit: True`, versión `10.0.26100`), que tiene **20 procesadores** (`KeNumberProcessors: 20`), que Windows está instalado en la carpeta `C:\WINDOWS` y que saqué la copia de la memoria el **23 de noviembre de 2026 a las 10:28**.

<img width="1215" height="825" alt="Screenshot_6" src="https://github.com/user-attachments/assets/dd1cdabc-2058-4ba0-adac-48faa123e05c" />

---

### 2.2. Procesos activos (`pslist`)
* **Función del comando:** Muestra una lista con todos los programas y procesos del sistema que estaban funcionando en ese instante.
* **Observaciones:** Aparecen los procesos básicos que hacen falta para que Windows funcione, como `System` (el núcleo del sistema), `winlogon.exe` (el inicio de sesión), `services.exe` (los servicios de Windows), `dwm.exe` (el encargado de mostrar las ventanas en pantalla) o `NVDisplay.Container.exe` (el programa de la tarjeta gráfica NVIDIA). Todos llevan encendidos desde las **09:03**, que fue cuando encendí el ordenador, y ninguno se había cerrado todavía (`ExitTime: N/A`).
<img width="877" height="1000" alt="Screenshot_7" src="https://github.com/user-attachments/assets/02e730b6-92e5-4836-a5b2-a34b8386711b" />

---

### 2.3. Conexiones de red (`netscan`)
* **Función del comando:** Enseña qué programas estaban conectados a internet o a la red local, qué puertos estaban abiertos y con qué direcciones IP se comunicaban.
* **Observaciones:** Salen varias conexiones de tipo **TCP** y **UDP**. Muchas líneas ponen **`LISTENING`** (por ejemplo en el puerto `53153`), lo que significa que el ordenador tiene ese puerto abierto esperando recibir datos, mientras que otras conexiones aparecen como **`CLOSED`** porque ya se habían cerrado.

<img width="1116" height="1014" alt="Screenshot_8" src="https://github.com/user-attachments/assets/710d9d1d-2434-4a7f-a80f-5461482ef536" />

---

### 2.4. Bibliotecas cargadas (`dlllist`)
* **Función del comando:** Muestra los archivos auxiliares (llamados bibliotecas DLL) que cada programa tenía cargados en la memoria para poder funcionar.
* **Observaciones:** Se ve cómo los procesos principales de Windows (como `smss.exe` o `csrss.exe`) usan archivos oficiales guardados en `C:\WINDOWS\System32\`, como `kernel32.dll` (para las funciones básicas del sistema), `USER32.dll` (para mostrar botones y ventanas) o `WS2_32.dll` (para poder conectarse a la red).

<img width="1025" height="986" alt="Screenshot_9" src="https://github.com/user-attachments/assets/859a5261-1bed-44de-a6b0-ec88b918bafa" />

---

### 2.5. Argumentos de ejecución (`cmdline`)
* **Función del comando:** Muestra la ruta exacta de carpetas desde donde se lanzó cada programa y las instrucciones con las que se ejecutó.
* **Observaciones:** Compruebo que todos los procesos importantes de Windows se abrieron desde su carpeta oficial (`C:\WINDOWS\system32\`), lo cual es buena señal porque demuestra que son programas originales y no imitaciones escondidas en otras carpetas. También se ve qué tarea concreta está haciendo cada `svchost.exe` (por ejemplo, controlar la red con `Dhcp` o los temas visuales con `Themes`).

<img width="1087" height="963" alt="Screenshot_10" src="https://github.com/user-attachments/assets/a99d8977-c6cd-4620-9386-d0fe016aee81" />

---

### 2.6. Detección de código inyectado (`malfind`)
* **Función del comando:** Revisa la memoria buscando zonas sospechosas o código escondido que pueda pertenecer a un virus o programa malicioso.
* **Observaciones:** Al revisar el proceso `Secure` (PID 288), la tabla sale totalmente vacía sin ningún aviso, lo que confirma que ese espacio de memoria está limpio y no tiene código malicioso dentro.

<img width="803" height="368" alt="Screenshot_11" src="https://github.com/user-attachments/assets/34db96da-a747-4b89-a79f-b7ff793bc3ed" />

---

## 3. Conclusión
He entendido por qué analizar la memoria RAM es tan útil en informática forense. Aunque alguien cierre un programa o un virus intente funcionar sin guardar archivos en el disco duro, en la memoria RAM queda guardado todo lo que estaba pasando en ese momento: qué programas estaban abiertos, desde qué carpeta exacta se iniciaron, qué archivos internos usaban y qué puertos de red tenían abiertos. Con herramientas gratuitas como MemProcFS y Volatility Workbench podemos reconstruir toda esa actividad paso a paso.
