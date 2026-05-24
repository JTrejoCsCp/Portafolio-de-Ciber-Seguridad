======================================================================
BITÁCORA DE LABORATORIO - DÍA 1: OPERADOR LINUX
======================================================================
Fecha de Ejecución : 24 de Mayo, 2026
Plataforma : Kali Linux (Entorno de Pruebas)
Objetivo : Dominio del Sistema de Archivos, Procesos y Logs

----------------------------------------------------------------------
1. NAVEGACIÓN Y MAPEO FÍSICO DEL ENTORNO
----------------------------------------------------------------------
* Estructuras Analizadas:
  - / : Raíz del sistema. Se identificaron enlaces simbólicos 
                críticos (bin -> usr/bin, sbin -> usr/sbin).
  - /etc : Configuración global del sistema (passwd, shadow).
  - /var/log : Registros de auditoría de servicios.
  - /usr/share: Datos estáticos de herramientas (wordlists, nmap).

* Nota de Configuración de Teclado (Eficiencia):
  - El símbolo "~" (Home) se ubica físicamente presionando la 
    combinación [Shift] + [tecla en la esquina superior izquierda, 
    antes del número 1] en esta distribución de hardware.

    - El símbolo "|" (pipe) se ubica físicamente presionando la 
    combinación [Shift] + [la tecla en la esquina entre enter y shift] en esta distribución de hardware. 

----------------------------------------------------------------------
2. ADMINISTRACIÓN DE PROCESOS (BLOQUE 2)
----------------------------------------------------------------------
* Comando Ejecutado: sleep 1000 &
* Acción : Envío de proceso fantasma a segundo plano.
* Auditoría : Localización de PID mediante "ps aux | grep sleep".
* Resolución : Liquidación quirúrgica del proceso.
* Confirmación : [1] + terminated sleep 1000

----------------------------------------------------------------------
3. AUDITORÍA DE SERVICIOS Y ANÁLISIS DE INCIDENTES (LOGS)
----------------------------------------------------------------------
* Incidente 1: Comando Unificado (Error de Sintaxis)
  - Comportamiento: Al ejecutar los comandos de forma continua sin 
    espacios, el sistema interpretó el binario como "cupsjournalctl".
  - Diagnóstico: "Unit cupsjournalctl.service not found."
  - Mitigación: Se aplicó la interrupción del teclado [Ctrl + C] para 
    recuperar el prompt de la terminal y separar las instrucciones.

* Incidente 2: Ausencia del Servicio de Impresión
  - Comportamiento: "Failed to start cups.service: Unit not found."
  - Diagnóstico: La imagen actual de Kali Linux no cuenta con el 
    paquete CUPS instalado por defecto para optimizar recursos.
  - Mitigación: Se redirigió la auditoría hacia el servicio activo 
    de red global ("NetworkManager") para la recolección de evidencias.

* Comando de Extracción de Logs Seguro (Evita Bloqueos):
  $ journalctl -u NetworkManager -n 10 --no-pager
  Resultados: Extracción exitosa en texto plano de los últimos 10 
  eventos de inicialización y asignación de interfaz de red.
======================================================================
