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
  settings:     { currency: "MXN"|"USD" }      // moneda de visualización
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
