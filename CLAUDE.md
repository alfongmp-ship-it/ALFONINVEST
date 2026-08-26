# AlfonInvest — contexto del proyecto

App web de una sola página para llevar el control del patrimonio personal de Alfonso:
cuentas de GBM, otras plataformas (NU, Revolut, Bitso) y coleccionables (tarjetas
deportivas gradeadas). Todo el código vive en `index.html` (HTML + CSS + JS, sin
dependencias ni build). Interfaz en español.

Publicada en https://alfongmp-ship-it.github.io/ALFONINVEST/ vía GitHub Pages
(`.github/workflows/pages.yml`): **cada push a la rama despliega la app automáticamente**.

## Rama de trabajo

Desarrollar y hacer push a `claude/investment-portfolio-app-g2ofx5` (es la rama por
defecto del repo y la que despliega Pages).

## ⚠️ El repositorio es PÚBLICO

GitHub Pages gratuito lo exige. **Nunca commitear estados de cuenta, PDFs, CSVs con
movimientos ni respaldos JSON del portafolio** — contienen datos financieros personales.
`.gitignore` ya bloquea esos patrones; mantener los archivos fuera de la carpeta del repo
es aún más seguro.

## Estructura de cuentas (flujo real de GBM)

1. **GBM Smart Cash (MXN, tipo `cash`)** — entra dinero del banco (Depósito) y genera
   rendimientos variables que se abonan periódicamente (Rendimiento).
2. **GBM Trading MX (MXN, tipo `broker`)** — se fondea con Transferencia desde Smart Cash;
   se invierte en instrumentos en pesos (tickers con sufijo `.MX`).
3. **GBM Trading USA (USD, tipo `broker`)** — se fondea con Transferencia desde Smart Cash
   **convirtiendo a dólares**: se registran los MXN que salieron y los USD que llegaron,
   de donde sale el tipo de cambio implícito de esa operación.

El usuario puede crear más cuentas (NU, Revolut, Bitso, coleccionables) en MXN o USD.

## Modelo de datos (localStorage, clave `alfoninvest_v1`)

```js
{
  version: 2,
  accounts:     [{ id, name, type: "cash"|"broker", currency: "MXN"|"USD" }],
  transactions: [{ id, account, type: "buy"|"sell", ticker, name, sector, qty, price, fees, date }],
  cashflows:    [{ id, kind: "deposit"|"withdraw"|"transfer"|"yield"|"fee",
                   from, to, amount, amountTo, date, note }],
  prices:       { TICKER: { price, prevClose, currency, updatedAt, manual } },
  fx:           { rate, updatedAt, manual },   // pesos por dólar
  history:      [{ date, valueMXN, aportMXN }],
  history_snapshots: [{ date, accounts: { id: valor }, fx, note }],   // cortes de patrimonio
  settings:     { currency: "MXN"|"USD",       // moneda de visualización
                  scope: "all"|"g:<grupo>"|"<id de cuenta>" }         // ámbito de análisis
}
```

Notas del motor de cálculo (`computePortfolio`):
- Costo promedio por **cuenta + ticker** (la misma emisora en dos cuentas son posiciones distintas).
- Las compras restan efectivo de su cuenta y las ventas lo suman.
- `amountTo` en una transferencia entre monedas distintas es obligatorio.
- **Ganancia global real** = patrimonio actual − aportaciones netas desde el banco.
  Incluye rendimientos, plusvalías y efecto del tipo de cambio. Si no hay depósitos
  registrados, cae a un cálculo basado solo en operaciones.
- Precios y tipo de cambio (`MXN=X`) vienen de Yahoo Finance vía proxies CORS; un precio
  puesto a mano queda marcado `manual: true` y no se sobrescribe al actualizar.

## Cifrado en reposo

Si el usuario pone contraseña, `localStorage` guarda un **sobre cifrado**:

```js
{ __alfoninvest_enc: 1, kdf: "PBKDF2-SHA256", iter: 600000, salt, iv, ct }  // todo en base64
```

AES-256-GCM con llave derivada por PBKDF2-SHA256. La llave vive solo en la variable
`sesion` (memoria); ni la llave ni la contraseña se escriben nunca. **No hay recuperación.**

- `saveState()` sigue siendo síncrono para sus decenas de llamadas: encola una escritura que
  serializa el estado más reciente, así que basta con una pendiente (`guardadoPendiente`).
- `loadState(p)` recibe el objeto ya descifrado; el arranque decide si mostrar el candado.
- Bloquear = `location.reload()` (deja cero rastro en memoria). Hay bloqueo automático
  opcional por inactividad (`settings.autoLock`, en minutos).
- **⬇ Exportar sale cifrado** cuando hay contraseña, y ⬆ Importar detecta el sobre y pide
  la clave con la que se creó ese archivo (puede no ser la de hoy).
- Web Crypto funciona en `file://` (es contexto seguro); si no hubiera, la app avisa y
  guarda en claro.

## Respaldo en la nube (`state.sync`)

Sube el **mismo sobre cifrado** a un repositorio **privado** de GitHub vía la Contents API
(`GET`/`PUT /repos/{owner}/{repo}/contents/{path}`), con `fetch` directo — la API manda
`Access-Control-Allow-Origin: *`, así que funciona hasta desde `file://`. Cada subida es un
commit, de modo que el historial del repo permite volver a los datos de cualquier día.

```js
state.sync = { owner, repo, path, token, branch, sha, ultima, auto }
```

- **El token nunca sale del dispositivo**: `contenidoParaSubir()` recorta `sync` antes de
  subir, y `aplicarDeNube()` conserva el `sync` local en vez del que venga en el archivo.
- Requiere un token *fine-grained* con `Contents: Read and write` sobre ese único repo.
- **Conflictos**: se compara el `sha` de la nube con el último que vio este dispositivo. Si
  no coinciden no se sube nada — se muestra `#nubeAviso` con las dos salidas explícitas
  (bajar la de la nube o forzar la de aquí). Nunca se pisa en silencio.
- La subida automática va con rebote de 8 s desde `saveState()`; `aplicandoNube` evita que
  bajar dispare una subida.
- **Repo recién creado**: no tiene commits, así que la rama por omisión todavía no existe.
  Al *crear* el archivo se omiten `branch` y `sha` (GitHub usa la suya y hace el commit
  inicial); solo al *actualizar* se mandan, que es cuando sirven para detectar conflictos.
- `permissions.push` solo se toma como negativa si viene explícitamente en `false`: con
  tokens fine-grained el campo puede no venir, y ahí manda lo que responda la subida real.
- Al conectar se avisa si el repo resulta ser público.

## Ámbito de análisis (`settings.scope`)

El selector del encabezado recorta **toda** la app: dashboard, posiciones, operaciones,
por año, dólar y diversificación. Se puede ver todo junto, un grupo (el grupo sale de la
primera palabra del nombre, así que las tres cuentas GBM caen juntas) o una sola cuenta.

`computePortfolio(scope)` acepta el ámbito como argumento y devuelve además `dentro(id)`,
`esParcial` y `txsScope`, que las demás funciones (`computeAnual`, `computeFX`,
`flujosExternos`, `snapMXN`) usan para filtrarse igual.

**Regla clave:** un traspaso que cruza la frontera del ámbito deja de ser interno y cuenta
como aportación o retiro. Lo que Smart Cash le pasa a Trading USA es dinero nuevo desde el
punto de vista de Trading USA, y así "ganancia = valor de hoy − lo que le metí" sigue
siendo cierto dentro de cualquier recorte. Por eso los renglones del desglose no suman el
aportado total: el mismo peso puede haber pasado por varias cuentas.

Llamadas que **deben** ir con `computePortfolio("all")` aunque haya un ámbito activo:
`refreshPrices` (precios de todas las cuentas), `recordHistorySnapshot` y `guardarCorte`
(los cortes guardan todo), `actualizarSaldo` y la validación de ventas.

El rendimiento anual usa Dietz modificado; si casi todo el dinero del año entró al final,
la base ponderada queda minúscula y el porcentaje se dispara, así que se muestra `n/d`
(la ganancia en pesos sí se enseña).

## Importar estados de cuenta

El importador de la pestaña Operaciones acepta CSV o texto pegado, con encabezados en
español o inglés (fecha, operación, emisora, serie, títulos, precio, comisión), fechas
`dd/mm/aaaa`, montos con `$` y comas. Detecta duplicados e ignora filas que no son
compras/ventas.

Para los PDFs de GBM el flujo es: leer los PDFs → extraer depósitos, rendimientos,
transferencias (con MXN→USD) y compras/ventas → generar un JSON con el esquema de arriba
→ el usuario lo carga con el botón **⬆ Importar** de la app.

## Pendientes / ideas

- Cargar el histórico de Smart Cash 2025 desde los PDFs del usuario.
- Sumar NU, Revolut y Bitso como cuentas con sus movimientos.
- Tarjetas deportivas gradeadas: cuenta tipo `broker`, cada carta como activo con
  identificador propio (ej. `JORDAN-PSA10`, sector "Coleccionables") y precio manual.
- Sincronización entre dispositivos (hoy los datos viven en el navegador de cada uno;
  se mueven con Exportar/Importar).
