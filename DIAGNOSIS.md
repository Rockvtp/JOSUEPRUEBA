# Motor experimental de diagnóstico

`AquariumDiagnosis.kt` no está conectado a la interfaz ni al guardado. Recibe un Tank, contexto explícito y fecha; devuelve evidencia, posibles causas, acciones, información faltante y limitaciones. No modifica datos ni exige red. No se presenta como diagnóstico veterinario.

## Estado de investigación y validación

No se dispuso de navegación web durante esta implementación: **no se consultaron ni verificaron publicaciones en vivo**. Esta es una primera implementación conservadora, no un motor con criterios completamente contrastados. Las referencias siguientes son puntos de revisión, no evidencia de consulta realizada:

- Emerson, K., Russo, R. C., Lund, R. E. y Thurston, R. V. (1975), *Aqueous Ammonia Equilibrium Calculations: Effect of pH and Temperature*, Journal of the Fisheries Research Board of Canada, 32, 2379–2383. DOI: https://doi.org/10.1139/f75-274. Referencia de la ecuación de equilibrio en agua dulce.
- US EPA, *Aquatic Life Ambient Water Quality Criteria for Ammonia — Freshwater (2013)*: https://www.epa.gov/wqc/aquatic-life-criteria-ammonia. Criterios ambientales, **no límites universales para acuarios ni dosis de tratamiento**.
- UF/IFAS Extension, *Ammonia in Aquatic Systems*: https://edis.ifas.ufl.edu/publication/FA031. Revisar química, unidades y control de amoníaco.
- Merck Veterinary Manual, sección de gestión y enfermedades de peces: https://www.merckvetmanual.com/. Revisar calidad del agua, biofiltración y diagnóstico diferencial.
- Manual y tabla del fabricante de cada kit: verificar qué mide (TAN como N, total como NH3 o NH3 libre), resolución, interferencias y tiempo de lectura.

Antes de integrar: contrastar estas referencias, documentar criterios específicos de especies, identificar kits y revisar con una persona cualificada en salud de peces/acuicultura. No se ha investigado exhaustivamente ni se afirma disponer de toda la información sobre acuarios.

## Reglas y límites

- Detección positiva de amoníaco/nitritos en las últimas 24 h => revisión urgente. No es un umbral clínico universal. Un resultado positivo antiguo o sin fecha requiere nueva medición y no se etiqueta como condición urgente actual. Fechas futuras y valores inválidos no sustentan conclusiones.
- Estrés reportado => urgente incluso si los tests muestran ceros. Oxígeno, cloro y otros tóxicos no están medidos.
- pH, temperatura, amoníaco y nitritos se consideran actuales por 24 h. Ventana conservadora de producto, no garantía de estabilidad. Otros parámetros usan vigencia del tanque.
- NH3: solo agua dulce declarada, base conocida, parámetros en una ventana de una hora y dominio restringido 0–30 °C / pH 6–10. pKa = 0.09018 + 2729.92 / (T°C + 273.15); fracción = 1 / (1 + 10^(pKa − pH)). Si TAN está expresado como N se convierte mediante 17.031/14.007. No se asigna un límite seguro a esta estimación. Temperatura interna del Tank está en °C.
- Los perfiles de Water.kt se reutilizan únicamente para agua dulce declarada: NO3 <20 mg/L y preferencia <10 para neocaridina son criterios actuales de la app, pendientes de contraste por especie; no límites universales.
- Cambios de pH >0.5, temperatura >2 °C y GH/KH/TDS >20 % son heurísticas de revisión entre pruebas genuinamente medidas en 24 h. No son tolerancias clínicas. EC/TDS no permiten deducir GH, KH ni oxígeno.
- Un montaje reciente o mantenimiento del filtro solo añade contexto si hay detección actual. No demuestra causalidad. Ceros y nitratos no certifican ciclado.
- No prescribe sal, fármacos, dosis ni porcentajes universales; nunca recomienda probar carga de amoníaco con habitantes.

## Simulaciones

18 pruebas en `AquariumDiagnosisTest.kt`: estabilidad limitada, amoníaco, nitritos, datos antiguos/futuros, estrés, nitratos, agua salada, base desconocida, asincronía de tests, conversión N/NH3, benchmark de la ecuación, tendencias y conservación de datos. Se incluyen en `testDebugUnitTest` del flujo existente.

**No ejecutadas desde este entorno.** Se necesita una ejecución de GitHub Actions para conocer si compilan y pasan. Pasar pruebas valida implementación contra escenarios, no exactitud clínica ni exhaustividad científica.

## Integración pendiente

No se añadió tarjeta visible, contexto persistente, importación de ese contexto ni uso de snapshots de cambios de agua. Revisar resultados de las pruebas y fuentes antes de integrar. El motor usa las mediciones/historial del tanque; no adjudica causas a operaciones ni ofrece frecuencia de cambios. El módulo existente de cambios de agua sigue intacto.
