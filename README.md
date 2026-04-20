# CENGOB Bolivia — IAE v28

**Centro de Gobierno · Índice de Avance de Ejecución**  
Sistema de seguimiento y validación ministerial de intervenciones prioritarias del Órgano Ejecutivo Plurinacional de Bolivia.

---

## ¿Qué es esto?

Aplicación web de una sola página (SPA) para el monitoreo del **Índice de Avance de Ejecución (IAE)** de las intervenciones gubernamentales priorizadas. Permite registrar, visualizar y validar el avance por etapas de cada indicador, agrupado por ministerio.

## Características

- **Dashboard ejecutivo** con resumen de avance global por ministerio
- **Vista de sesiones** por indicador con fichas detalladas y etapas configurables
- **Panel analítico** con gráficos de velocidad, distribución y radar de avance
- **Vista de presentación** en formato cinematográfico para reuniones de gabinete
- **Panel de edición** (acceso restringido) para gestionar indicadores, etapas y ponderaciones
- Importación/exportación en **Excel, CSV y JSON**
- Persistencia local mediante **localStorage**

## Uso

Abre el archivo `CENGOB_v28_Final.html` directamente en tu navegador. No requiere servidor ni dependencias adicionales.

## Configuración de credenciales

El panel de edición está protegido por usuario y contraseña. Las credenciales por defecto son:

```
Usuario:  admin
Contraseña: changeme
```

> ⚠️ **Cambia estos valores** antes de distribuir el archivo. Edita la función `doLogin()` en el código fuente y reemplaza `validU` y `validP` con tus credenciales. Recuerda que al ser un archivo HTML estático, la protección es solo de interfaz — no almacenes credenciales sensibles en repositorios públicos.

## Dependencias externas (CDN)

- [Bootstrap 5.3.3](https://getbootstrap.com/)
- [Chart.js 4.4.1](https://www.chartjs.org/)
- [SheetJS (xlsx) 0.18.5](https://sheetjs.com/)
- [Google Fonts — Barlow / Barlow Condensed](https://fonts.google.com/)

## Versiones

| Versión | Descripción |
|---------|-------------|
| v28     | Versión actual — Panel editor con secciones de IO y sistema |
| v27     | Mejoras en vista analítica y panel de presentación |
| v26     | Primera versión con login modal |

## Licencia

Uso interno del Estado Plurinacional de Bolivia — Centro de Gobierno.
