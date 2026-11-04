# Capturas para demo y defensa

Tomar desde `http://localhost:4200` con usuarios demo (ver `docs/TRAZABILIDAD_SPRINT_P2.md`). **Anonimizar** si aparecen datos reales.

## Checklist antes de capturar

- [ ] Backend y frontend levantados (`npm run dev:server` + `npm run dev:web` en `src/`).
- [ ] Usuario aspirante `20123456789` / `demo1234` con seed cargado.
- [ ] Ventana de inscripción abierta (`INSCRIPCION_ABIERTA=true`).
- [ ] Recortar capturas sin barra de direcciones con datos sensibles.

## Orden sugerido (guión 8–10 min)

| # | Archivo sugerido | Pantalla | Usuario | Qué debe verse |
|---|------------------|----------|---------|----------------|
| 1 | `01-home-pasos.png` | `/home` | `20123456789` | Pasos legajo, Form C, puntaje, chip intereses mapa |
| 2 | `02-legajo-titulos.png` | `/legajo` | aspirante | Catálogo títulos desde validador + grilla |
| 3 | `03-formulario-c-ee.png` | `/formulario-c` | aspirante | Búsqueda escuela + ítems 320/193 |
| 4 | `04-puntaje-desglose.png` | `/puntaje` | aspirante | Columnas código + tipo + 3 filas |
| 5 | `05-mapa-vacantes.png` | `/mapa` | aspirante | Filtro asignatura `320`, semáforo, panel intereses |
| 6 | `06-simulador-ascenso.png` | `/simulador` | aspirante | Checklist mock |
| 7 | `07-capacitaciones.png` | `/capacitaciones` | aspirante | Sugerencias |
| 8 | `08-junta-rechazo.png` | `/junta` | `27999888777` | Revisión vocal + rechazo |
| 9 | `09-legajo-observacion.png` | `/legajo` | `20123456789` | Observación tras rechazo |

## Cómo generar

```powershell
cd src
npm run dev:server
npm run dev:web
```

Captura con Win+Shift+S o herramienta del navegador. Guardar PNG en esta carpeta.

## Uso académico

- **Inst 3:** evidencia de review de sprint en informe.
- **Inst 4:** frames para video demo y póster.
- **Inst 5:** base para instructivo de uso aspirante/vocal.
