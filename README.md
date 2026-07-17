# 📈 AlfonInvest — Control de Inversiones

Aplicación web para llevar el control completo de tu portafolio de inversiones: registra compras y ventas de acciones, sigue tus ganancias en tiempo real y recibe recomendaciones de diversificación.

## 🚀 Cómo usarla

**No necesitas instalar nada.** Descarga `index.html` y ábrelo en cualquier navegador (Chrome, Safari, Edge, Firefox), en computadora o celular.

También puedes publicarla gratis con GitHub Pages: en este repositorio ve a **Settings → Pages → Branch: main → Save** y tendrás la app en línea con una URL propia.

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
