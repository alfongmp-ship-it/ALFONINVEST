# 📈 AlfonInvest — Control de Inversiones

Aplicación web para llevar el control completo de tu patrimonio: cuentas de GBM (Smart Cash, Trading MX, Trading USA) y las que quieras agregar (NU, Bitso, Revolut, coleccionables), con compras/ventas, movimientos de dinero, multi-moneda MXN/USD con tipo de cambio en vivo, ganancias en tiempo real y recomendaciones de diversificación.

## 🏦 Estructura de cuentas (flujo GBM)

- **GBM Smart Cash (MXN)** — efectivo con rendimiento: registra ahí tus **Depósitos** desde el banco y los **Rendimientos** que te abona.
- **GBM Trading MX (MXN)** — inversiones en pesos: fondéala con **Transferencias** desde Smart Cash y registra tus compras con tickers `.MX`.
- **GBM Trading USA (USD)** — inversiones en dólares: al transferir desde Smart Cash indicas los pesos que salieron y los dólares recibidos, y la app captura el tipo de cambio implícito.
- Puedes **crear más cuentas** (NU, Revolut, Bitso, tarjetas deportivas…) en MXN o USD, de tipo "efectivo con rendimiento" o "inversiones". Los coleccionables se registran como activos con identificador propio (ej. `JORDAN-PSA10`) y precio manual.

Con los depósitos registrados, la app calcula tu **ganancia global real**: patrimonio actual menos dinero aportado, incluyendo rendimientos de Smart Cash, plusvalías y el efecto del tipo de cambio. El selector "Ver en MXN/USD" convierte todos los totales con el tipo de cambio del mercado (o el que fijes manualmente).

## 🚀 Cómo usarla

**No necesitas instalar nada.** Descarga `index.html` y ábrelo en cualquier navegador (Chrome, Safari, Edge, Firefox), en computadora o celular.

## 📱 Usarla desde el teléfono (recomendado)

1. Publica la app gratis con **GitHub Pages**: en este repositorio ve a **Settings → Pages**, en *Branch* elige la rama y carpeta `/ (root)`, y pulsa **Save**. En unos minutos tendrás una URL tipo `https://tuusuario.github.io/ALFONINVEST/`.
2. Abre esa URL en el navegador de tu teléfono.
3. La app es una **PWA instalable**: en Chrome/Android toca el menú ⋮ → **"Agregar a pantalla de inicio"** (o el aviso de "Instalar app"); en iPhone/Safari toca **Compartir → "Agregar a inicio"**. Queda con su propio ícono, abre a pantalla completa y funciona incluso sin conexión.

## 🏦 Importar tus operaciones de GBM+ (u otro bróker)

GBM no ofrece API pública para clientes, pero en la pestaña **Transacciones → "Importar desde tu bróker"** puedes:

- **Copiar y pegar** la tabla de órdenes ejecutadas / movimientos de GBM+ (o de Excel), o subir un **archivo CSV**.
- El importador reconoce columnas en cualquier orden, en español o inglés: `fecha`, `tipo/operación` (compra/venta), `emisora/ticker`, `serie`, `títulos/cantidad`, `precio`, `comisión`, y opcionalmente `nombre` y `sector`. Acepta fechas `dd/mm/aaaa`, montos con `$` y comas, etc.
- Las filas que no son compras/ventas (dividendos, depósitos, retiros) se ignoran solas, y las operaciones repetidas no se duplican aunque importes dos veces.
- La casilla **".MX"** agrega el sufijo de la BMV/SIC a los tickers para que Yahoo Finance dé los precios **en pesos** (ej. `AAPL.MX`, `NAFTRACISHRS.MX`). Si inviertes en GBM, déjala activada y elige **MXN** en el selector de moneda (arriba a la derecha).
- Hay un botón para descargar una **plantilla CSV** de ejemplo.

## 💾 ¿Dónde se guardan mis datos?

Todo se guarda **en el navegador del dispositivo donde uses la app** (localStorage): nada sale a ningún servidor y nadie más puede verlo. Eso implica:

- La app "recuerda" tus datos entre sesiones automáticamente, mientras uses **el mismo navegador en el mismo dispositivo**.
- Computadora y teléfono **no se sincronizan solos**: usa **⬇ Exportar** en un dispositivo y **⬆ Importar** en el otro para pasarte los datos.
- Haz un respaldo con **⬇ Exportar** de vez en cuando (si borras los datos de navegación, se borra el portafolio).

## ✨ Funciones

### 🧾 Registro de operaciones
- Registra **compras y ventas** con ticker, cantidad, precio, comisión, fecha y sector.
- Confirmación si intentas vender más acciones de las que tienes.
- Historial completo de operaciones con opción de eliminar.

### 💼 Posiciones y ganancias
- Cálculo automático por **método de costo promedio**.
- **Ganancia no realizada** (posiciones abiertas) y **ganancia realizada** (ventas ejecutadas), en dinero y porcentaje.
- Cambio del día por posición y del portafolio completo.
- Peso (%) de cada posición dentro del portafolio.

### 📊 Dashboard en tiempo real
- **Precios en vivo** desde Yahoo Finance (acciones, ETFs, cripto como `BTC-USD`), con actualización automática cada 60 segundos.
- Si algún precio no se puede obtener (sin internet, ticker raro), puedes escribirlo manualmente.
- Gráficas de **distribución por activo y por sector**, ganancia/pérdida por posición y **evolución histórica** del portafolio.

### 🎯 Diversificación
- **Calificación de 0 a 100** de qué tan diversificado estás.
- Métricas profesionales: índice de concentración **HHI**, número efectivo de posiciones, peso del mayor activo y sector.
- **Recomendaciones automáticas**: concentración excesiva en un activo o sector, pocos activos, falta de renta fija, exceso de cripto, sugerencia de ETFs como núcleo, etc.

### 💾 Tus datos
- Todo se guarda **localmente en tu navegador** (localStorage) — nadie más ve tus datos.
- Botones de **Exportar / Importar** para respaldar o mover tus datos entre dispositivos.
- Botón de **datos de ejemplo** para probar la app al instante.

## ⚠️ Nota

Las recomendaciones de diversificación son reglas generales con fines educativos y no constituyen asesoría financiera profesional.
