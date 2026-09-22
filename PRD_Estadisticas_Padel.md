# PRD – Aplicación de Estadísticas de Pádel

| Campo | Valor |
|---|---|
| Versión | 0.3 |
| Fecha | 22/09/2026 |
| Autor | Eduardo |
| Estado | En revisión |

**Historial de cambios**

| Versión | Fecha | Cambio |
|---|---|---|
| 0.1 | 14/09/2026 | Borrador inicial con sugerencias. |
| 0.2 | 14/09/2026 | Se aplican las sugerencias y las respuestas a las preguntas abiertas. |
| 0.3 | 22/09/2026 | Correcciones de la evaluación: alcance del MVP recortado, rating de equipo con invitado, K por jugador y redondeo, riesgo de recálculo retroactivo, metas numéricas, CA-15 separado y stack definido. |

> Los puntos marcados **(Supuesto)** no fueron confirmados explícitamente y deben validarse (ver sección 9).

**Prioridad:**
- **Alta** = entra en el MVP (Entrega 1).
- **Media** = Entrega 2, se suma si hay tiempo.
- **Baja** = deseable.

---

## 1. Contexto y objetivo

La aplicación registra partidos de pádel y calcula estadísticas **individuales y por pareja**. Cada jugador se registra, se identifica, carga los partidos que jugó (fecha, compañero, rivales y resultado) y consulta estadísticas y rankings.

**Objetivos del producto:**
1. Ranking de parejas más ganadoras. _(Entrega 2)_
2. Ranking individual de jugadores. _(MVP)_
3. Evolución de cada jugador a lo largo del historial, con gráfico de efectividad en el tiempo. _(Entrega 2)_

**Métricas de éxito** (medidas a los **3 meses** del lanzamiento) **(Supuesto:** metas a validar**)**

| Métrica | Meta |
|---|---|
| % de jugadores registrados que cargan al menos 1 partido por mes | **≥ 60%** |
| Partidos cargados por mes | **≥ 80** |
| % de partidos eliminados por error o duplicado sobre el total cargado | **≤ 5%** |
| Mediana del tiempo de carga de un partido | **≤ 30 segundos** |

## 2. Alcance del MVP

El MVP se concentra en el núcleo demostrable: **motor de validación de resultados + ELO individual**.

| Entrega | Contenido |
|---|---|
| **Entrega 1 – MVP** (prioridad Alta) | Registro e inicio de sesión · Carga de partido con todas sus validaciones (incluye invitados y detección de duplicados) · Listado y eliminación de los propios partidos · ELO individual con recálculo cronológico · Ranking individual |
| **Entrega 2** (prioridad Media) | Grupos · ELO y ranking de parejas · Edición de partidos · Fichas de jugador y pareja · Head-to-head · Gráfico de evolución · Filtros de ranking · Recuperación de contraseña · Pantalla explicativa del ELO |
| **Posterior** (prioridad Baja) | Comparación de jugadores · Exportación y compartir por WhatsApp · Apodo y foto |

## 3. Actores / Roles

| Rol | Descripción |
|---|---|
| Jugador registrado | Se registra solo, se identifica, carga partidos y consulta el ranking. En la Entrega 2 también crea grupos o se une a ellos. |
| Jugador invitado | Participa en un partido sin tener cuenta. Solo se registra su nombre y no accede a la aplicación. |
| Creador del grupo _(Entrega 2)_ | Jugador que creó el grupo. Puede modificar sus datos o darlo de baja. **(Supuesto)** |

No hay administrador general.

---

## 4. Requerimientos Funcionales

### 4.1 Registro e identificación

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-01 | Registro abierto: cada jugador crea su propia cuenta. | Alta |
| RF-02 | Autenticación con **email y contraseña**. | Alta |
| RF-03 | **Todas** las funcionalidades requieren login; no hay consulta pública. | Alta |
| RF-04 | Email único por cuenta. | Alta |
| RF-05 | Recuperación de contraseña por email. | Media |
| RF-06 | Cierre de sesión. | Alta |

### 4.2 Jugadores

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-10 | Datos del jugador: Id, nombre, email, **fecha de nacimiento** (se muestra la edad calculada), mano hábil (derecho/zurdo) y **posición preferida** (drive / revés / indistinto). | Alta |
| RF-11 | Datos opcionales: apodo y foto. | Baja |
| RF-12 | El jugador puede editar sus propios datos. | Media |
| RF-13 | Baja de cuenta: es lógica y conserva los partidos en el historial. | Media |

### 4.3 Grupos _(Entrega 2)_

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-20 | Cualquier jugador registrado puede crear un grupo con **Id** y **Nombre** (obligatorios) y una descripción (opcional). | Media |
| RF-21 | Nombre de grupo único. | Media |
| RF-22 | Los grupos son **visibles para todos** los jugadores registrados, que pueden buscarlos por nombre. | Media |
| RF-23 | Un jugador puede **unirse** a cualquier grupo visible sin aprobación y **salir** de él. | Media |
| RF-24 | Un jugador puede pertenecer a **más de un grupo** (relación N a N Jugador–Grupo). | Media |
| RF-25 | El creador puede modificar el nombre y la descripción del grupo, o darlo de baja (baja lógica). **(Supuesto)** | Media |
| RF-26 | El grupo se usa para agrupar jugadores y **filtrar** estadísticas; no genera un ranking propio. | Media |

### 4.4 Carga de partidos

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-30 | El jugador identificado carga un partido en el que participó. Siempre queda como integrante de una de las parejas. | Alta |
| RF-31 | Datos obligatorios: **fecha**, **compañero**, **2 rivales** y **resultado set por set**. | Alta |
| RF-32 | Compañero y rivales se eligen entre los jugadores registrados (con autocompletado) **o se ingresan como invitados escribiendo solo el nombre**. En la Entrega 2 el autocompletado prioriza a los miembros de los grupos del jugador. | Alta |
| RF-33 | Grupo del partido: opcional, entre los grupos a los que pertenece quien carga. **(Supuesto)** | Media |
| RF-34 | Datos opcionales: club o cancha y observaciones. | Baja |
| RF-35 | El partido impacta **inmediatamente** en el ELO y el ranking; no requiere confirmación de otros participantes. | Alta |
| RF-36 | **Detección de duplicados:** si ya existe un partido con la misma fecha y los mismos jugadores registrados, el sistema lo advierte antes de guardar y permite cancelar o guardar igual. | Alta |
| RF-37 | Quien cargó el partido puede **eliminarlo**; se recalcula el ELO (ver RF-59). **(Supuesto:** solo quien lo cargó**)** | Alta |
| RF-38 | No se permiten fechas futuras. | Alta |
| RF-39 | Un mismo jugador no puede figurar dos veces en el partido. | Alta |
| RF-40 | **No se registran** abandonos, W.O. ni partidos incompletos: solo partidos terminados con resultado completo. | Alta |
| RF-41 | Listado de "mis partidos" ordenado por fecha descendente, con acceso a eliminar. | Alta |
| RF-42 | Quien cargó el partido puede **editarlo**; se recalcula el ELO (ver RF-59). **(Supuesto:** solo quien lo cargó**)** | Media |
| RF-43 | Filtros en "mis partidos" por fecha, compañero y rival. | Media |

### 4.5 Reglas de validación del resultado

- Partido **al mejor de 3 sets**. Gana quien gana **2 sets**.
- **Los 3 sets son completos**; no hay super tie-break ni partidos por tiempo.
- Resultados válidos por set: **6-0, 6-1, 6-2, 6-3, 6-4, 7-5 y 7-6** (7-6 = tie-break con 6-6), y sus inversos. **(Supuesto:** también hay tie-break con 6-6 en el tercer set**)**
- Cualquier otro marcador de set es inválido (ej. 6-5, 8-6, 7-4, 5-3).
- Si un set queda 2-0, **no** se permite cargar un tercer set.
- Si los sets quedan 1-1, el tercer set es **obligatorio**.
- No se aceptan resultados incompletos.

### 4.6 Sistema de puntaje – ELO

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-50 | Cada **jugador registrado** tiene un puntaje ELO **único** (no depende del grupo). | Alta |
| RF-51 | Cada **pareja** (2 jugadores registrados, sin importar el orden: A-B = B-A) tiene su propio ELO de pareja. | Media |
| RF-52 | ELO inicial: **1500**. | Alta |
| RF-53 | **Cálculo del ELO individual por partido:** <br>1. **Rating de cada equipo** = promedio de los ratings de sus 2 integrantes. Un **invitado cuenta siempre como 1500** en ese promedio. Ej.: jugador con 1600 + invitado → equipo 1550. <br>2. Resultado esperado del equipo: `E = 1 / (1 + 10^((R_equipo_rival − R_equipo_propio) / 400))` <br>3. Variación de **cada jugador registrado**: `Δ = K_jugador × M × (S − E)`, con S = 1 si su equipo ganó y 0 si perdió. <br>4. Cada jugador usa **su propio K** (RF-54), así que los compañeros pueden recibir Δ distintos y la suma del partido puede no ser cero. <br>5. Los invitados no se actualizan. | Alta |
| RF-54 | **Factor K** configurable: **40** si el jugador tenía **menos de 10 partidos** antes de este partido, y **24** en caso contrario. | Alta |
| RF-55 | **Multiplicador por diferencia (M)** configurable: victoria **2-0 → 1,25** y victoria **2-1 → 1,0**. El mismo M se aplica a ganadores y perdedores. | Alta |
| RF-56 | **ELO de pareja:** se aplica la misma fórmula, pero tomando como rating de cada equipo el ELO de pareja, y K según los partidos que la pareja jugó junta. Una pareja con invitado **no tiene ELO de pareja**. Si la pareja rival tiene un invitado, su rating es **1500**. | Media |
| RF-57 | **Invitados:** rating fijo de **1500** (tanto para el ELO individual como para el de pareja), no acumulan ELO y no aparecen en rankings. | Alta |
| RF-58 | **Mínimo para entrar al ranking: 1 partido.** El ranking muestra la cantidad de partidos jugados para dar contexto. | Alta |
| RF-59 | El puntaje es un dato **derivado**. Cuando se carga un partido con fecha anterior al último registrado, o se edita o elimina un partido, se **recalcula el ELO de todos los jugadores** desde ese partido en adelante. Orden cronológico: fecha del partido y, a igual fecha, fecha y hora de carga. | Alta |
| RF-60 | Pantalla "¿Cómo se calcula el puntaje?" con la explicación del ELO y del recálculo retroactivo. | Media |
| RF-61 | **Precisión:** el ELO se guarda con 2 decimales y se muestra redondeado a entero (redondeo simétrico, 0,5 hacia arriba). Los cálculos se hacen siempre sobre el valor con decimales. | Alta |

### 4.7 Estadísticas y rankings

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-70 | **Ranking individual** de todos los jugadores registrados, ordenado por ELO. A igual ELO se ordena por más partidos jugados y luego por nombre. | Alta |
| RF-71 | **Ranking de parejas** ordenado por ELO de pareja. | Media |
| RF-72 | Columnas básicas del ranking: posición, ELO, PJ, PG, PP y % efectividad. | Alta |
| RF-73 | Filtros de los rankings: **grupo** (muestra solo a los miembros, con su ELO global) y rango de fechas. **(Supuesto:** el filtro de fechas limita PJ, PG y %; el ELO que se muestra es el vigente**)** | Media |
| RF-74 | **Evolución del jugador:** gráfico de la **media móvil** de efectividad (% de partidos ganados en los últimos **N = 10** partidos, configurable), con eje X = fecha del partido. Con menos de N partidos se usan todos los disponibles. | Media |
| RF-75 | En el mismo gráfico, opcionalmente, la curva de ELO en el tiempo. | Media |
| RF-76 | Filtro por rango de fechas en el gráfico de evolución. | Media |
| RF-77 | **Ficha del jugador:** datos, ELO, posición en el ranking, PJ/PG/PP, mejores compañeros, rivales más frecuentes, historial contra un rival (head-to-head), racha y rendimiento por posición. | Media |
| RF-78 | **Ficha de la pareja:** historial de partidos, % de efectividad y evolución. | Media |
| RF-79 | Comparar la evolución de 2 jugadores en el mismo gráfico. | Baja |
| RF-80 | Exportar un ranking a PDF o Excel, o compartirlo por WhatsApp. | Baja |
| RF-81 | Columnas extendidas del ranking: sets y games a favor y en contra, y racha actual. | Media |

---

## 5. Requerimientos No Funcionales

| ID | Categoría | Requerimiento |
|---|---|---|
| RNF-01 | Plataforma | Aplicación web responsive **mobile-first**. Hacerla instalable como PWA queda para después del MVP. |
| RNF-02 | Usabilidad | Cargar un partido en menos de 30 segundos, con autocompletado de jugadores y teclado numérico para los games. |
| RNF-03 | Volumen | Diseñada para **~100 usuarios registrados**, hasta 20 concurrentes y hasta 10.000 partidos en total. |
| RNF-04 | Rendimiento | Ranking y listados en menos de 2 segundos; recálculo completo de ELO en menos de 10 segundos con 10.000 partidos. |
| RNF-05 | Seguridad | Contraseñas con hash mediante **ASP.NET Core Identity**, HTTPS obligatorio, mínimo 8 caracteres de contraseña, bloqueo temporal tras 5 intentos fallidos, sesión que expira por inactividad y protección anti-CSRF. |
| RNF-06 | Autorización | Todas las pantallas requieren sesión. Solo quien cargó un partido puede editarlo o eliminarlo, y solo el creador de un grupo puede modificarlo. |
| RNF-07 | Privacidad | Cumplimiento de la Ley 25.326 (Argentina): aceptación de términos al registrarse y derecho de acceso y de baja. Se muestra la **edad**, no la fecha de nacimiento, y el email no se muestra a otros jugadores. |
| RNF-08 | Integridad | Los puntajes deben poder recalcularse desde cero con el historial de partidos. El recálculo se ejecuta en una transacción: si falla, no queda un estado a medias. |
| RNF-09 | Auditoría | Registrar quién creó, modificó o eliminó cada partido y cuándo. |
| RNF-10 | Disponibilidad | 99% mensual. |
| RNF-11 | Backup | Backup automático de Azure SQL con restauración a un punto en el tiempo de al menos 7 días. |
| RNF-12 | Compatibilidad | Últimas 2 versiones de Chrome, Safari, Edge y Firefox; Android 10+ e iOS 15+. |
| RNF-13 | Idioma y formato | Español; fechas dd/mm/aaaa; zona horaria de Argentina (UTC-3). |
| RNF-14 | Mantenibilidad | Parámetros de puntaje (ELO inicial, K, umbral de partidos para K, multiplicadores, N de la media móvil) configurables en `appsettings.json`, sin cambiar código. El motor de validación de sets y el cálculo de ELO se implementan como clases independientes de la interfaz, con tests unitarios que cubren los criterios CA-09 a CA-11, CA-16, CA-17 y CA-25. |
| RNF-15 | Tecnología | **Definido:** ver la tabla siguiente. |

**Stack tecnológico (RNF-15)**

| Capa | Tecnología |
|---|---|
| Aplicación | **ASP.NET Core MVC** (.NET 10 LTS, C#) con vistas Razor server-side |
| Autenticación | ASP.NET Core Identity (email y contraseña, lockout, hash de contraseñas) |
| Acceso a datos | Entity Framework Core (code-first con migraciones) |
| Base de datos | **SQL Server** en desarrollo y **Azure SQL Database** en producción |
| Interfaz | Bootstrap 5 (responsive) |
| Gráficos (Entrega 2) | Chart.js |
| Tests | xUnit para el motor de validación y el ELO |
| Hosting | **Azure App Service** (Linux o Windows) + Azure SQL Database |
| Email (Entrega 2) | Azure Communication Services Email o SendGrid, para la recuperación de contraseña |

---

## 6. Criterios de Aceptación

Formato **Dado / Cuando / Entonces**. Los criterios marcados _[Entrega 2]_ aplican a funcionalidades de prioridad Media.

### Registro y acceso

**CA-01 – Registro**
- Dado un usuario no registrado
- Cuando completa email, contraseña, nombre, fecha de nacimiento, mano hábil y posición
- Entonces se crea la cuenta y queda identificado.

**CA-02 – Email duplicado**
- Cuando intenta registrarse con un email ya existente
- Entonces el sistema rechaza el registro e informa que el email ya está en uso.

**CA-03 – Acceso sin login**
- Dado un usuario sin sesión iniciada
- Cuando intenta acceder a cualquier pantalla (ranking, partidos)
- Entonces es redirigido al login.

### Grupos _[Entrega 2]_

**CA-04 – Crear grupo**
- Cuando un jugador crea el grupo "Martes Club Norte"
- Entonces el grupo queda visible para todos los jugadores y el creador queda como miembro.

**CA-05 – Nombre de grupo repetido**
- Cuando se crea un grupo con un nombre existente
- Entonces el sistema lo rechaza.

**CA-06 – Unirse a varios grupos**
- Dado un jugador miembro de "Martes"
- Cuando se une a "Club Norte"
- Entonces pertenece a ambos grupos y aparece al filtrar el ranking por cualquiera de los dos.

### Carga de partidos

**CA-07 – Partido válido**
- Dado un jugador identificado
- Cuando carga fecha de hoy, un compañero, 2 rivales y el resultado 6-4 / 3-6 / 7-5
- Entonces el partido se guarda y el ELO y el ranking se actualizan de inmediato.

**CA-08 – Invitado**
- Cuando carga un partido con un rival no registrado, ingresando solo el nombre "Juan"
- Entonces el partido se guarda y "Juan" no aparece en el ranking.

**CA-09 – Set inválido**
- Cuando se carga un set 6-5, 8-6 o 7-4
- Entonces el sistema rechaza la carga e informa "Resultado de set inválido".

**CA-10 – Tercer set innecesario**
- Cuando se carga 6-2 / 6-3 / 6-4
- Entonces el sistema rechaza la carga porque el partido ya estaba definido 2-0.

**CA-11 – Resultado incompleto**
- Cuando se carga 6-2 / 3-6 sin tercer set
- Entonces el sistema no permite guardar y exige completar el tercer set.

**CA-12 – Jugador repetido**
- Cuando el mismo jugador figura como compañero y como rival
- Entonces el sistema no permite guardar.

**CA-13 – Fecha futura**
- Cuando se carga un partido con fecha posterior a hoy
- Entonces el sistema rechaza la carga.

**CA-14 – Duplicado**
- Dado un partido A-B vs C-D del 10/09 ya cargado
- Cuando C carga un partido del 10/09 con los mismos 4 jugadores
- Entonces el sistema advierte el posible duplicado y permite cancelar o guardar igual.

**CA-15 – Solo quien cargó puede modificar**
- Dado un partido cargado por A, en el que también participa B
- Cuando B intenta eliminarlo (o editarlo, en la Entrega 2)
- Entonces el sistema no lo permite.

**CA-24 – La eliminación recalcula el ELO**
- Dados 4 jugadores nuevos (1500 cada uno) y un único partido cargado por A en el que A-B le gana 2-1 a C-D (A y B en 1520; C y D en 1480)
- Cuando A elimina ese partido
- Entonces A, B, C y D vuelven a 1500 con PJ = 0.
- Y la eliminación queda registrada en la auditoría con usuario y fecha.

### Puntaje y ranking

**CA-16 – Cálculo ELO**
- Dados 4 jugadores nuevos (1500 cada uno, K = 40)
- Cuando A-B le gana 2-1 a C-D
- Entonces A y B pasan a 1520, y C y D pasan a 1480.

**CA-17 – Multiplicador**
- Dados los mismos jugadores iniciales
- Cuando A-B gana 2-0
- Entonces A y B pasan a 1525, y C y D pasan a 1475.

**CA-25 – Equipo con invitado**
- Dado el jugador A con ELO 1600 y 20 partidos (K = 24), que juega con un invitado
- Y los rivales C y D con ELO 1500 y 20 partidos cada uno (K = 24)
- Cuando A + invitado le gana 2-1 a C-D
- Entonces el rating del equipo de A es 1550 y su resultado esperado es E = 0,5715.
- Y A pasa a **1610,28** (se muestra 1610), mientras C y D pasan a **1489,72** (se muestra 1490).
- Y el invitado no tiene ELO registrado.

**CA-26 – K distinto entre compañeros**
- Dados A (1500, 0 partidos, K = 40) y B (1500, 20 partidos, K = 24) contra C y D (1500, 20 partidos cada uno, K = 24)
- Cuando A-B gana 2-1
- Entonces A pasa a 1520 y B pasa a 1512, mientras C y D pasan a 1488.

**CA-18 – Mínimo de partidos**
- Cuando un jugador tiene 1 partido cargado
- Entonces aparece en el ranking individual con PJ = 1.

**CA-22 – Carga retroactiva**
- Dado un jugador con partidos del 01/09 y del 10/09
- Cuando se carga un partido del 05/09
- Entonces el ELO se recalcula en orden cronológico (01/09 → 05/09 → 10/09).
- Y el ELO de los jugadores del partido del 10/09 puede cambiar aunque no hayan jugado el 05/09.

**CA-19 – Pareja sin orden** _[Entrega 2]_
- Dado que A-B jugaron juntos 10 partidos y B-A 5 partidos
- Entonces el ranking de parejas los muestra como **una sola pareja** con 15 partidos.

**CA-20 – Pareja con invitado** _[Entrega 2]_
- Cuando A juega con un invitado
- Entonces el ELO individual de A se actualiza, pero esa pareja no aparece en el ranking de parejas.

**CA-21 – Filtro por grupo** _[Entrega 2]_
- Cuando se filtra el ranking individual por el grupo "Martes"
- Entonces solo se listan los miembros de "Martes", ordenados por su ELO.

**CA-23 – Media móvil** _[Entrega 2]_
- Dado un jugador con 12 partidos (N = 10)
- Cuando abre su gráfico de evolución
- Entonces cada punto muestra el % de victorias de los últimos 10 partidos hasta esa fecha; los primeros puntos usan los partidos disponibles.

---

## 7. Fuera de Alcance

- Registro de **abandonos, W.O.** y partidos incompletos o por tiempo.
- **Super tie-break** y otros formatos de partido.
- **Confirmación** de partidos por otros participantes.
- **Consulta pública** sin login.
- **Rankings por grupo** con puntaje independiente (el grupo es solo un filtro).
- Login con Google u otras redes; autenticación de dos factores.
- Roles de administrador general y moderación.
- Grupos privados o con aprobación de ingreso.
- Notificaciones push o por email (salvo la recuperación de contraseña).
- **Migración** de datos históricos.
- Organización de **torneos**, fixtures o cuadros.
- **Reserva de canchas**, integración con clubes y **pagos**.
- **Marcador en vivo** y estadísticas punto por punto.
- Chat o red social entre jugadores.
- Matchmaking o sugerencia de rivales.
- **Apps nativas** iOS/Android.
- Integración con rankings oficiales.
- Multi-idioma.
- Singles y otros deportes.

---

## 8. Riesgos y Dependencias

### 8.1 Riesgos

| # | Riesgo | Impacto | Prob. | Mitigación |
|---|---|---|---|---|
| R-01 | **Resultados erróneos o falseados:** no hay confirmación y el partido impacta de inmediato. | Alto | Media | Auditoría de cambios, historial visible para todos los participantes y posibilidad de agregar confirmación en una versión futura. |
| R-02 | **Partidos duplicados** si varios participantes cargan el mismo partido. | Alto | Alta | Advertencia de duplicados (RF-36) y eliminación por quien cargó. |
| R-03 | **Ranking poco representativo con 1 partido mínimo:** un jugador con 1 victoria puede quedar arriba. | Medio | Alta | ELO con valor inicial 1500 (1 partido mueve ±20 a 30 puntos) y columna PJ visible. |
| R-04 | **Invitados con nombres distintos** para la misma persona ("Juan", "Juan P."), lo que impide seguir su historial. | Bajo | Alta | Autocompletar invitados usados antes e invitar a esa persona a registrarse. |
| R-05 | **Distorsión por invitados:** se les asigna 1500 aunque su nivel real sea otro. | Medio | Media | Parámetro configurable; evaluar un rating de invitado distinto más adelante. |
| R-06 | **Pocas combinaciones de parejas:** muchas parejas con 1 o 2 partidos. _(Entrega 2)_ | Medio | Alta | Mostrar PJ y permitir filtrar por mínimo de partidos en la vista. |
| R-07 | **Baja adopción:** si no se cargan todos los partidos, las estadísticas pierden valor. | Alto | Media | Carga rápida desde el celular y compartir el ranking. |
| R-08 | **Disputas por el algoritmo.** | Medio | Media | Pantalla explicativa (RF-60) y parámetros ajustables. |
| R-09 | **Grupos abiertos:** cualquiera puede unirse a cualquier grupo. _(Entrega 2)_ | Bajo | Media | Aceptable para 100 usuarios; grupos privados fuera de alcance. |
| R-10 | Datos personales (fecha de nacimiento, email). | Medio | Baja | Mostrar solo la edad, ocultar el email y cumplir la Ley 25.326. |
| R-11 | **Cambios de ranking sin haber jugado:** al cargar, editar o eliminar un partido con fecha pasada (RF-59) se recalcula el ELO de todos los partidos posteriores, así que un jugador puede bajar puestos sin haber jugado. Es una consecuencia del diseño, no un error, pero puede generar quejas. | Medio | Alta | Explicarlo en la pantalla del ELO (RF-60). Mostrar en "mis partidos" el ELO antes y después de cada partido, para que se vea el efecto del recálculo. Evaluar en la Entrega 2 un límite de antigüedad para la carga retroactiva (ej. 30 días). |
| R-12 | **Alcance vs. duración del curso:** el total de requerimientos excede el tiempo disponible. | Alto | Alta | MVP recortado a registro, carga con validaciones, ELO individual y ranking individual (sección 2). El resto queda en la Entrega 2. |
| R-13 | **Costo de Azure** si se superan los tiers básicos. | Bajo | Baja | Para ~100 usuarios alcanza con App Service tier básico y Azure SQL serverless o Basic; configurar alertas de presupuesto. |

### 8.2 Dependencias

- **Suscripción de Azure** con permisos para crear App Service y Azure SQL Database.
- **SDK de .NET 10** y SQL Server (o LocalDB) en el entorno de desarrollo.
- **Grupo piloto** de jugadores reales para validar la carga y la calibración del ELO (K y multiplicadores).
- **Textos legales:** términos y condiciones y política de privacidad.
- _(Entrega 2)_ **Servicio de envío de emails** para la recuperación de contraseña.
- _(Entrega 2)_ **Chart.js** para los gráficos de evolución.

---

## 9. Modelo de datos (referencia)

| Entidad | Campos principales |
|---|---|
| Jugador | Id, Nombre, Apodo, Email, PasswordHash, FechaNacimiento, ManoHabil, Posicion, EloActual (decimal 2), PartidosJugados, Activo, FechaAlta |
| Partido | Id, Fecha, IdGrupo (opcional), Club, Observaciones, IdCargadoPor, FechaCarga, Eliminado |
| PartidoParticipante | IdPartido, Equipo (1/2), IdJugador (null si es invitado), NombreInvitado |
| PartidoSet | IdPartido, NroSet (1-3), GamesEquipo1, GamesEquipo2 |
| HistorialElo | IdPartido, IdJugador, EloAntes, EloDespues, K, Delta |
| Auditoria | Entidad, IdEntidad, Accion, IdUsuario, Fecha, Detalle |
| _Grupo (Entrega 2)_ | Id, Nombre (único), Descripcion, IdCreador, Activo, FechaAlta |
| _JugadorGrupo (Entrega 2)_ | IdJugador, IdGrupo, FechaIngreso |
| _Pareja (Entrega 2)_ | Id, IdJugador1, IdJugador2 (con IdJugador1 < IdJugador2), EloActual, PartidosJuntos |
| _HistorialEloPareja (Entrega 2)_ | IdPartido, IdPareja, EloAntes, EloDespues |

---

## 10. Decisiones y supuestos

### 10.1 Decisiones tomadas

| Tema | Decisión |
|---|---|
| Alcance del MVP | Registro, carga de partido con validaciones, ELO individual y ranking individual. El resto va a la Entrega 2. |
| Identificación | Email y contraseña; no hay consulta sin login. |
| Alta de jugadores | Cada jugador se registra solo. |
| Grupos | Los crea cualquier jugador, son visibles para todos y cualquiera puede unirse (Entrega 2). |
| Rankings | Por jugador (ELO único) y por pareja (Entrega 2); el grupo es un filtro. |
| Formato | Mejor de 3 sets completos; no hay partidos por tiempo; el resultado debe estar completo. |
| Abandono / W.O. | No se registran. |
| Algoritmo | ELO por equipos, con K por jugador; el invitado cuenta como 1500 en el promedio del equipo. |
| Mínimo para ranking | 1 partido. |
| Evolución | Media móvil de efectividad (Entrega 2). |
| Invitados | Permitidos, solo con nombre. |
| Confirmación | No se requiere. |
| Volumen | ~100 usuarios. |
| Datos históricos | No hay migración. |
| Stack | ASP.NET Core MVC (.NET 10) + Entity Framework Core + SQL Server / Azure SQL. |
| Hosting | Azure App Service + Azure SQL Database. |

### 10.2 Supuestos a validar

1. Hay tie-break con 6-6 en todos los sets, incluido el tercero (7-6 es válido).
2. Solo quien cargó el partido puede editarlo o eliminarlo.
3. El creador del grupo puede modificarlo o darlo de baja.
4. El grupo del partido es opcional.
5. Los invitados cuentan con un rating fijo de 1500 y las parejas con invitado no entran al ranking de parejas.
6. Parámetros ELO: inicial 1500; K = 40 con menos de 10 partidos previos y 24 después; multiplicador 1,25 para 2-0 y 1,0 para 2-1.
7. Media móvil sobre los últimos N = 10 partidos.
8. El filtro de fechas del ranking afecta las estadísticas (PJ, PG, %), no el ELO.
9. Metas de las métricas de éxito (60% de jugadores activos, 80 partidos por mes, 5% de eliminados y 30 segundos de carga).

### 10.3 Pendientes

No hay pendientes bloqueantes. Queda validar los supuestos de 10.2 con el grupo piloto.
