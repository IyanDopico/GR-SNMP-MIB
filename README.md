# Gestión de Redes de Telecomunicación: Monitorización SNMP de Servidor FTP

## Descripción del Proyecto
El objetivo principal es la monitorización de un servidor FTP monohilo mediante el protocolo SNMP. Dado que el servidor no dispone de un agente nativo, el proyecto requiere el diseño de un MIB (Management Information Base) específico, la implementación del agente mediante Agent Builder y la monitorización final a través de Zabbix.

## Componentes del Sistema
* **Servidor FTP:** Proporcionado en formato `.jar`, implementa los comandos `DIR`, `GET` y `PUT`.
* **Archivos de Configuración:** * `rootdir.txt`: Contiene la ruta de la carpeta raíz del servidor.
    * `numerodirs.txt`: Almacena el conteo de comandos `DIR` recibidos.
* **Herramientas de Gestión:** SNMP Agent Builder para el agente, Mib Browser para pruebas y Zabbix como Manager.

---

## Especificaciones del MIB
[cite_start]El MIB se organiza bajo el nodo raíz OID `1.3.6.1.4.1.47454`.

### Objetos de Gestión Requeridos
| Objeto | Descripción | Acceso | Restricciones |
| :--- | :--- | :--- | :--- |
| **Carpeta Root** | Ruta completa de la carpeta de archivos del servidor. | Lectura/Escritura  | Máximo 32 caracteres. |
| **Contador DIR** | Número de peticiones DIR procesadas por el servidor. | Lectura  | N/A |
| **Número de Ficheros** | Cantidad de archivos en la carpeta root. | Lectura  | N/A |
| **Borrado de Ficheros** | Permite eliminar todos los archivos de la carpeta root. | Escritura  | N/A |
| **TestAndIncr** | Objeto para asegurar exclusión mutua en cambios. | Lectura/Escritura | N/A |
| **Umbral de Ficheros** | Límite máximo de ficheros permitidos en el servidor[cite: . | Lectura/Escritura ] | Valor entre 1 y 200 (Defecto: 5). |
| **Frecuencia Sondeo** | Intervalo en el que el agente comprueba condiciones de eventos. | Lectura/Escritura  | Entre 1 y 120 segundos (Defecto: 2). |
| **Control de Eventos** | Activa o desactiva el envío de notificaciones SNMP. | Lectura/Escritura  | N/A |
| **Mensaje de Notificación** | Texto en lenguaje natural adjunto a los eventos. | accessible-for-notify | Máximo 32 caracteres. |

---

## Sistema de Notificaciones (Eventos)
El agente debe enviar eventos (traps/notifications) ante las siguientes situaciones:
1. **Cambio de Carpeta Root:** Se envía siempre que se modifique la ruta, ya sea vía SNMP o directamente en el archivo `rootdir.txt`. Adjunta la nueva ruta y el mensaje descriptivo.
2. **Carpeta Inválida:** Se envía si el manager intenta cambiar la carpeta root a una ruta que no existe (solo vía SNMP). El cambio no se ejecuta.
3. **Over Quota:** Se activa si el número de ficheros supera el umbral fijado. Adjunta el número de ficheros actual, el umbral y el mensaje descriptivo.

---

## Configuración de Monitorización (Zabbix)
La monitorización en Zabbix requiere las siguientes métricas periódicas:
* **Número de Ficheros:** Sondeo cada 1 minuto.
* **Frecuencia de Sondeo Interno:** Sondeo cada 1 hora.
* **Alertas:** Configurar un disparador (trigger) en el Dashboard que indique incidencia cuando el número de ficheros sea superior a 5.

---

## Requisitos de Entrega
* Archivo de definición del MIB validado con nivel de severidad 6 en Simpleweb.
* Código fuente del proyecto Agent Builder.
* Memoria técnica incluyendo diseño del MIB, implementación de getters/setters, lógica de eventos y capturas de la configuración en Zabbix.
* Fecha límite: 3 de Mayo de 2026.
