# UT2 - AE202: Análisis Forense de la Memoria RAM

## 1. Introducción y Objetivo
El objetivo de esta práctica es adquirir una imagen de la memoria RAM en un sistema Windows mediante **MemProcFS** y analizarla con **Volatility Workbench** para identificar procesos activos, conexiones de red, bibliotecas cargadas y posibles indicios de actividad maliciosa.

---

## 2. Resultados del Análisis con Volatility

### 2.1. Identificación del perfil (`imageinfo`)
* **Función del comando:** Analiza el volcado de memoria para identificar la versión exacta y arquitectura del sistema operativo y sugerir el perfil adecuado para el resto del análisis.
* **Observaciones:** Se seleccionó el perfil `Win10x64_...` tras revisar las sugerencias.

![Resultado de imageinfo](img/01_imageinfo.png)

### 2.2. Procesos activos (`pslist`)
* **Función del comando:** Lista los procesos que se encontraban en ejecución en el sistema en el momento de realizar la captura de memoria.
* **Observaciones:** *(Indica aquí los procesos principales observados y, si hiciste la actividad opcional, destaca `notepad.exe`, `calc.exe` o tu navegador).*

![Resultado de pslist](img/02_pslist.png)

### 2.3. Conexiones de red (`netscan`)
* **Función del comando:** Muestra las conexiones TCP/UDP activas, puertos en escucha y direcciones IP remotas asociadas a cada proceso.
* **Observaciones:** *(Describe brevemente qué procesos tenían conexiones abiertas).*

![Resultado de netscan](img/03_netscan.png)

### 2.4. Bibliotecas cargadas (`dlllist`)
* **Función del comando:** Muestra las bibliotecas dinámicas (DLL) cargadas en memoria por cada proceso en ejecución.
* **Observaciones:** *(Comenta brevemente alguna DLL relevante cargada por un proceso concreto).*

![Resultado de dlllist](img/04_dlllist.png)

### 2.5. Argumentos de ejecución (`cmdline`)
* **Función del comando:** Extrae la línea de comandos exacta y los parámetros con los que se lanzó cada proceso.
* **Observaciones:** *(Indica las rutas de ejecución observadas, incluyendo los programas abiertos en la actividad opcional).*

![Resultado de cmdline](img/05_cmdline.png)

### 2.6. Detección de código inyectado (`malfind`)
* **Función del comando:** Busca segmentos de memoria con permisos sospechosos o inyección de código para detectar posibles procesos maliciosos.
* **Observaciones:** *(Resume si se detectaron segmentos sospechosos o falsos positivos habituales del sistema).*

![Resultado de malfind](img/06_malfind.png)

---

## 3. Conclusión
*(Redacta aquí en un párrafo qué información has podido reconstruir a partir del volcado y por qué el análisis de la memoria RAM es clave en informática forense frente al análisis tradicional en disco duro).*
