# Simulaciones del motor y caso de referencia

Estado: implementadas, con resultados esperados; no ejecutadas con Gradle en este entorno. No se afirma que pruebas, lint o APK hayan pasado.

## Ejecución real sin abrir la app

Ver [DIAGNOSIS-EXECUTION.md](DIAGNOSIS-EXECUTION.md). El workflow ejecuta `DiagnosisExecutionReportTest` con pH 7.2 y pH 8, publica `diagnostico-ejecucion.md` y `.json` con las salidas completas y compara cálculos y recomendaciones por habitantes. No se muestran resultados esperados como ejecutados; si falla la compilación, el resumen indica ausencia de salida.

## Acceso

Inicio → Diagnóstico → Abrir simulaciones → Tu caso de referencia · TAN 5 (seleccionado inicialmente). Doce casos usan `diagnoseTank`, mismo motor del diagnóstico real. Tanques ficticios en memoria: no crean ni cambian registros. Fecha fija `simulationNow` evita caducidad variable.

## Caso externo del usuario

Entradas del HTML: pH 7.2, 73 °F, TAN 5 mg/L total como NH3, NO2 0, NO3 10, KH 4.5, GH 4, sustrato inerte, pocas plantas y 9.5 h. Fauna: corydoras, tetra neón y caracol manzana. TDS y EC ausentes.

Resultados esperados:

- Evaluación completa de las siete pruebas básicas, sin exigir TDS/EC.
- NH3 ≈0.038 mg/L, fracción ≈0.76 %, Alerta R-03.
- CO2 matemático ≈8.5 mg/L, orientativo, no medición ni objetivo obligatorio de inyección.
- GH bajo para manzana, banda efectiva 8–10 según modelo del HTML.
- KH alto respecto al techo del neón: intersección puntual 4 en ese modelo, no precisión biológica ni permiso para perseguir décimas.
- NO2 y NO3 sin alertas tóxicas en ese caso.
- Fotoperiodo 9.5 h genera orientación de vigilancia de luz, no prueba de algas.

Plan esperado: cambio inicial orientativo del 25 % si agua compatible; revisar restos/carga y alimentación de adultos; TAN/pH/temperatura en 24 h y diariamente tres días. GH gradual; no elevar KH/pH con TAN. Cada regla explica mejoría y escalación. No se cancela automáticamente una alerta histórica ni se certifica ciclado por una nueva lectura.

## Otros casos

| Caso | Entrada diferencial | Esperado |
|---|---|---|
| Comunitario estable | Base: 6 neones + 6 corydoras; pH 7.2; NO3 5; TAN/NO2 0; KH 4; GH 8; 77 °F; TDS 180; EC 300; plantas medias; Stratum; híbrido | Sin alertas en lo medido; observación y mantenimiento |
| KH bajo | KH 1 con Stratum | Alerta; interpretar estabilidad/fauna, no crash automático |
| Amoníaco elevado | TAN 1; pH 8; 77 °F | NH3 ≈0.0538; Peligro; acción inicial 50 % condicionada y retest |
| Nitritos altos | NO2 1 | Urgencia; actuación y retest en 2–6 h |
| Gambario | Neocaridina; NO2 0.15; 75.2 °F | Peligro; no sal/cobre automático |
| Nitratos acumulados | NO3 80 | Alerta crónica por techo del neón; no urgencia aguda |
| Falta GH | GH ausente; demás pruebas vigentes | Datos insuficientes para evaluación completa; restantes parámetros sí se evalúan |
| Boqueo sin O2 | GH ausente y boqueo al amanecer | Peligro; evaluación parcial; aireación y diferencial, no hipoxia confirmada |
| Vegetación media | NO3 0; TAN/NO2 0 | Sin alertas + hipótesis de consumo vegetal; no certifica ciclado |
| Evento del filtro | TAN 0.5, ciclado previo y lavado con grifo | Alerta; posible reducción de capacidad, no colapso demostrado |
| Síntomas vegetales | Agujeros viejos/clorosis nueva, sin K/Fe | Alerta de revisión; acciones y seguimiento de brotes nuevos, no carencia confirmada |

Todos muestran plan Ahora/Próxima revisión/Seguimiento y fichas por regla. Las recomendaciones cambian según el problema; no se asigna un cambio de agua genérico a una alerta de plantas.

## Regresión de referencia

`DiagnosisReferenceTest` verifica los números y reglas externas, habitantes limitantes, cambio en GH al retirar manzana, límite de nitrato con EI, evaluación parcial útil, confianza baja con kit caducado, CO2 alto sesgado sin urgencia automática, nueva muestra recuperada sin certificar ciclado y tres muestreos reales elevados frente a arrastres.

`DiagnosisSimulationTest` compara doce escenarios y exige acciones, plazos, mejoría/escalación y plan no vacíos. Pruebas previas de historial/respaldos se conservan. Gráfica simulada: tres registros pero solo dos pH medidos, 7.0 y 7.4; el pH arrastrado se omite.

Los resultados esperados no demuestran validez clínica ni que la compilación pase. GitHub Actions ejecutará pruebas, lint y APK; informes HTML/XML JUnit incluyen `SIMULATION` y `HTML REFERENCE`. Requiere además comprobar resultados y estilo visual en dispositivo.
