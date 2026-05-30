# 🔍 Análisis Forense de Campaña de Phishing: Dominio `cionxs.com`

> **Informe Técnico de Inteligencia de Amenazas — OSINT Pasivo**
> **Autor:** José Trejo
> **Fecha:** Mayo 2026
> **Clasificación:** Uso educativo y profesional — Análisis pasivo sin intrusión

> ⚠️ *Este informe ha sido elaborado con fines educativos, de desarrollo profesional y demostración de capacidades técnicas en ciberseguridad. No contiene información privada de terceros identificables.*

---

## 1. Resumen Ejecutivo

El dominio `cionxs.com` fue identificado como infraestructura de phishing activa, registrada recientemente y operada mediante técnicas de ingeniería social que incluyeron la creación de un perfil falso en Facebook. El análisis fue completamente pasivo, basado en fuentes de inteligencia abierta (OSINT). Se identificaron múltiples indicadores de compromiso (IoC) con alta confianza, incluyendo un formulario de captura de contraseñas, meta-tags evasivos y ausencia total de configuraciones de seguridad estándar. El informe concluye con recomendaciones técnicas para usuarios individuales, organizaciones y equipos SOC.

---

## 2. Introducción y Contexto del Incidente

### 2.1 Vector de Ataque — Ingeniería Social vía Facebook

El ataque fue iniciado a través de un perfil de Facebook falso que contactó directamente a la víctima mediante mensajes directos. El perfil aparentaba pertenecer a una persona real con foto de perfil convincente (posiblemente generada por IA o extraída de otra red social), contenido coherente y nombre plausible. El atacante utilizó pretexting para generar confianza y enviar un enlace al dominio malicioso.

Facebook es la red más utilizada globalmente para distribución de phishing, según informes del **APWG eCrime** e **IBM X-Force Threat Intelligence Index**.

### 2.2 Ciclo de Vida del Ataque

| Fase | Descripción |
|------|-------------|
| 1. Reconocimiento | El atacante identifica a la víctima y selecciona Facebook como vector |
| 2. Preparación | Creación del perfil falso y registro del dominio `cionxs.com` |
| 3. Distribución | Contacto directo y envío del enlace malicioso |
| 4. Explotación | La víctima accede al sitio; el formulario captura credenciales |
| 5. Post-explotación | Uso de credenciales robadas o venta en dark web |

---

## 3. Análisis Técnico del Dominio — `cionxs.com`

### 3.1 Información WHOIS

| Campo | Valor |
|-------|-------|
| Dominio | `cionxs.com` |
| Registrador | Namecheap, Inc. |
| Fecha de creación | 2025-04-08 |
| Fecha de expiración | 2026-04-08 |
| Estado | `clientTransferProhibited` |
| Name Servers | `dns1.registrar-servers.com` / `dns2.registrar-servers.com` |
| País del registrador | Estados Unidos |
| Privacidad WHOIS | ✅ Habilitado — Withheld for Privacy ehf (Islandia) |

> 💡 La privacidad WHOIS es legítima por sí sola, pero en combinación con los demás indicadores amplifica significativamente el riesgo. Namecheap es uno de los registradores más utilizados en campañas de phishing por su política de bajo costo y privacidad por defecto.

### 3.2 Análisis DNS y DNSSEC

| Registro | Valor / Hallazgo |
|---------|-----------------|
| A Record | IP resuelta — posible reverse proxy (Cloudflare u otro CDN) |
| MX Records | ❌ Sin registros MX configurados |
| TXT Records | ❌ Sin registros SPF/DKIM |
| DNSSEC | ❌ Deshabilitado — sin validación criptográfica |
| TTL | Bajo — indicativo de infraestructura ágil o cambio frecuente |

> Los dominios legítimos con alto tráfico suelen tener DNSSEC configurado. La ausencia de MX indica que el dominio no está diseñado para comunicación empresarial, sino exclusivamente para captura de datos.

### 3.3 Infraestructura IP y Hosting

La IP asociada está posiblemente alojada detrás de Cloudflare u otro reverse proxy. Esta es una táctica común en operaciones de phishing para:

1. **Ocultar** la IP real del servidor malicioso
2. **Beneficiarse** de alta disponibilidad y latencia reducida
3. **Dificultar** el bloqueo por reputación de IP

---

## 4. Análisis en VirusTotal

### 4.1 Categorización del Dominio

| Atributo | Valor |
|---------|-------|
| Categoría asignada | `newly registered website` |
| Detecciones de motores AV | ✅ Positivas — Phishing/Malicious URL |
| Comunidad VT | Votos negativos de la comunidad |
| Primer análisis | Reciente (coherente con fecha de registro) |

> Los dominios nuevos tienen historial limpio de reputación, permitiéndoles evadir filtros basados en listas negras históricas. Este es un comportamiento clásico de **domain aging evasion**.

### 4.2 Meta-Tags Evasivos Detectados

```html
<meta name="robots" content="noindex, nofollow">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
```

- `noindex, nofollow` → Impide indexación en motores de búsqueda, reduciendo exposición pública del sitio malicioso
- Un sitio legítimo nuevo querría ser encontrado por Google — **ocultarse es señal directa de actividad maliciosa**

### 4.3 ✅ Detección de Formulario de Contraseña (password-input)

VirusTotal identificó la presencia de `<input type="password">` en la página principal. Este hallazgo es **crítico** porque:

1. Confirma que el sitio contiene una interfaz de captura de credenciales
2. Un campo de contraseña en la landing page sin contexto de portal legítimo es el indicador más directo de phishing
3. Combinar este hallazgo con dominio nuevo + `noindex` es suficiente para clasificar como **High Confidence Phishing**

### 4.4 Análisis de HTTP Headers

| Header | Estado | Observación |
|--------|--------|-------------|
| `Server` | Cloudflare | Oculta tecnología backend real |
| `X-Frame-Options` | ❌ Ausente | Puede ser embebido en iframes |
| `Content-Security-Policy` | ❌ Ausente | Sin protección contra XSS |
| `Strict-Transport-Security (HSTS)` | ❌ Ausente | Sin forzado de HTTPS |
| `X-Content-Type-Options` | ❌ Ausente | Sin protección MIME sniffing |

> La ausencia total de headers de seguridad en un sitio que maneja contraseñas confirma que no fue construido con estándares de desarrollo seguro.

---

## 5. Clasificación de Indicadores de Compromiso (IoC)

| Indicador | Tipo | Confianza | Descripción |
|-----------|------|-----------|-------------|
| `cionxs.com` | Dominio | 🔴 Alta | Dominio de phishing activo |
| IP asociada al dominio | IP Address | 🟡 Media | Posible infraestructura compartida |
| Perfil de Facebook usado | Social Engineering IoC | 🔴 Alta | Vector de distribución del ataque |
| Formulario `<input type="password">` | HTML Artifact | 🔴 Alta | Captura de credenciales confirmada |
| Meta `noindex/nofollow` | HTML Artifact | 🔴 Alta | Evasión de rastreo |
| WHOIS con privacidad en Namecheap | WHOIS Indicator | 🟡 Media | Ocultamiento de identidad del operador |
| DNSSEC deshabilitado | DNS Indicator | 🟡 Media | Sin validación criptográfica |
| Sin registros MX | DNS Indicator | 🟡 Media | No diseñado para correo legítimo |

---

## 6. Análisis de Riesgo

### 6.1 Scoring de Riesgo

| Vector | Puntuación | Justificación |
|--------|-----------|---------------|
| Riesgo para el usuario individual | **9 / 10** | Alta probabilidad de robo de credenciales |
| Riesgo de persistencia de la infraestructura | **7 / 10** | Dominio activo, hosting posiblemente resiliente |
| Probabilidad de detección temprana | **4 / 10** | Múltiples capas de evasión dificultan detección automatizada |

### 6.2 Perfil del Actor de Amenaza (Threat Actor Profiling)

Basado en los TTPs observados, el perfil probable del actor es:

| Atributo | Evaluación |
|---------|-----------|
| Nivel de sofisticación | **Intermedio** — sin exploits de día cero, pero con múltiples capas de evasión |
| Motivación probable | **Financiera** — robo de credenciales para reventa o acceso no autorizado |
| Marco MITRE ATT&CK | T1566 (Phishing), T1598 (Identity Gathering), T1583.001 (Acquire Domains) |
| Geografía probable | **No determinada** — infraestructura distribuida con privacidad habilitada |

---

## 7. Hipótesis sobre el Perfil Falso de Facebook

> ⚠️ *Esta sección contiene hipótesis analíticas basadas en TTPs documentados. No son afirmaciones verificadas sobre individuos específicos.*

### 7.1 Técnicas de Creación de Perfil Falso

- **Foto de perfil:** Posiblemente generada con IA (GAN/Diffusion model) o extraída mediante scraping — no aparece en búsquedas de imagen inversa
- **Historial:** Publicaciones genéricas previas (frases motivacionales, naturaleza) para aparentar antigüedad
- **Red de contactos:** Posiblemente parte de una red coordinada (Coordinated Inauthentic Behavior — CIB)
- **Nombre:** Plausible en el contexto cultural/lingüístico objetivo (hispanohablante)

### 7.2 Señales de Alerta para Detectar Perfiles Falsos

1. Fecha de creación reciente con poca actividad orgánica
2. Foto de perfil con rasgos de IA: orejas asimétricas, fondo inconsistente, texto distorsionado
3. Sin publicaciones propias o solo contenido genérico
4. Sin etiquetas de amigos en fotos
5. Mensajes no solicitados con enlaces externos
6. URL del enlace no corresponde al servicio que supuestamente representa

---

## 8. Marco Legal y Limitaciones Éticas

### 8.1 Alcance del Análisis — Solo OSINT Pasivo

Todo el análisis fue realizado mediante técnicas de inteligencia de fuentes abiertas (OSINT) de carácter **estrictamente pasivo**:

- ✅ No se realizó ningún tipo de acceso no autorizado a sistemas
- ✅ No se ejecutaron herramientas de escaneo activo (nmap, nikto, dirbusting, etc.)
- ✅ No se enviaron solicitudes HTTP al dominio malicioso con intención de interacción
- ✅ Toda la información fue obtenida de registros públicos (WHOIS, DNS público, VirusTotal)

### 8.2 Marco Normativo Aplicable

| Marco | Aplicación |
|-------|-----------|
| Criminal Code of Canada — s.342.1 | El acceso no autorizado a sistemas es delito federal |
| Computer Fraud and Abuse Act (CFAA) | Aplica si la infraestructura está en jurisdicción estadounidense |
| Directiva NIS2 — Europa | Marco europeo de ciberseguridad para incidentes con infraestructura EU |
| GDPR | Explica la política de privacidad del registrador en WHOIS |

### 8.3 Reporte Responsable

- 📧 Reporte a VirusTotal Community
- 🇨🇦 Reporte al CCCS — Canadian Centre for Cyber Security ([cyber.gc.ca](https://cyber.gc.ca))
- 📧 Abuse contact de Namecheap: `abuse@namecheap.com`
- 🚩 Reporte del perfil falso mediante el mecanismo oficial de Facebook

---

## 9. Conclusiones

1. **El dominio `cionxs.com` es una infraestructura de phishing activa** con múltiples capas de evasión confirmadas mediante análisis OSINT pasivo.

2. **El vector vía Facebook representa una amenaza subestimada** por usuarios que confían excesivamente en la autenticidad de contactos en redes sociales.

3. **La combinación dominio nuevo + privacidad WHOIS + `noindex` + `password-input`** constituye un patrón de alta confianza para clasificación automática como phishing.

4. **Los actores de amenaza de nivel intermedio son especialmente peligrosos** por su relación costo-beneficio: técnicas accesibles con impacto potencialmente devastador.

5. **El OSINT pasivo es una herramienta poderosa y legalmente segura** para la investigación de amenazas, accesible a analistas sin infraestructura empresarial.

6. **La educación del usuario final sigue siendo la defensa más efectiva** contra ataques de ingeniería social, independientemente de las soluciones tecnológicas implementadas.

---

## 10. Recomendaciones

### Para Usuarios Individuales

1. Verificar siempre la autenticidad de perfiles antes de interactuar con desconocidos
2. No hacer clic en enlaces enviados por mensajes directos no solicitados
3. Usar un gestor de contraseñas que detecte dominios no reconocidos
4. Activar autenticación de dos factores (2FA/MFA) en todas las cuentas
5. Buscar la foto de perfil en Google Images o TinEye antes de responder
6. Verificar la URL en la barra de direcciones antes de introducir cualquier dato

### Para Organizaciones

1. Implementar filtrado DNS (DNS RPZ / Cisco Umbrella / Cloudflare Gateway)
2. Exigir MFA en todos los accesos corporativos
3. Integrar feeds de threat intelligence (VirusTotal Intelligence, AlienVault OTX)
4. Configurar reglas SIEM para detección de dominios newly registered
5. Capacitar periódicamente al personal mediante simulaciones de phishing
6. Implementar DMARC + DKIM + SPF para proteger el dominio corporativo

### Para Equipos SOC/CSIRT

1. Crear playbook específico para phishing vía redes sociales
2. Suscribirse a feeds de IoC con categoría `newly_registered_domain`
3. Integrar reglas Sigma/YARA para detección de patrones de meta-tags evasivos
4. Establecer proceso de reporte coordinado con CCCS y Namecheap abuse team

---

## 11. Referencias y Fuentes

| Fuente | URL |
|--------|-----|
| VirusTotal | https://virustotal.com |
| ICANN WHOIS | https://lookup.icann.org |
| MITRE ATT&CK Framework | https://attack.mitre.org |
| APWG eCrime Reports | https://apwg.org/resources/apwg-reports |
| IBM X-Force Threat Intelligence | https://exchange.xforce.ibmcloud.com |
| Canadian Centre for Cyber Security | https://cyber.gc.ca |
| NCSC UK Phishing Guidance | https://ncsc.gov.uk |
| Namecheap Abuse | abuse@namecheap.com |

---

*Análisis realizado con fines educativos y de desarrollo profesional en ciberseguridad. José Trejo — Toronto, Ontario, Canadá — Mayo 2026*
