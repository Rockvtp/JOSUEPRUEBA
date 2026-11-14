# Diagnóstico independiente en Android

## Fuente y arquitectura

Adaptación del archivo aportado `Motor de Diagnóstico Acuario Plantado (2).html`: JavaScript `SPECIES_DB`, `buildTankProfile`, `fNH3`, `co2est`, `diagnose` y acciones por regla (aprox. líneas 713–930). Se revisó el código del adjunto, no se verificaron externamente sus citas.

`diagnoseTank` combina `DetailedDiagnosis.kt` con reglas de `DiagnosisContext.kt`; `DiagnosisFollowUp.kt` añade seguimiento, mejoría/escalación y plan conjunto. `DiagnosisProfiles.kt` contiene las bandas por especies del adjunto. `DiagnosisResult.kt` muestra resumen, parámetros, plan Ahora/Próxima revisión/Seguimiento y fichas con evidencia, confianza, acciones y criterios de reevaluación.

La función sigue separada desde Inicio; no cambia web, mediciones, gráfica existente, cuadrícula o datos guardados. `ComprehensiveDiagnosis.kt` conserva un prototipo anterior no conectado a esta pantalla.

## Perfiles y contexto

Modo y bandas se calculan desde habitantes con recuento positivo, no nombres ni números de tanque. Se muestran especies limitantes de pH/temperatura/GH/KH y límite de NO3. Para corydoras + neón + manzana, el modelo del HTML da GH 8–10 y KH 4 puntual. Esto se etiqueta como intersección del modelo, no exigencia biológica de precisión decimal. No se recomienda perseguir KH exacto ni subir pH/KH con TAN presente.

Las bandas del diagnóstico proceden de este HTML y pueden diferir de las de otros módulos. Se mantienen separadas para no cambiar funciones existentes sin petición. Corydoras/manzana siguen con identificación provisional; bandas y límites del documento no son umbrales clínicos externamente verificados.

Densidad Poca/Media/Densa, sustrato con capas y filtración híbrida siguen guardados en Tank/Room y respaldos. Contexto de sesión no sobrescribe esos datos. Sin fauna no se presume ciclado. Grava + Stratum es mixto activo; el filtro híbrido no garantiza oxígeno suficiente.

## Mediciones y gravedad

Siete pruebas vigentes de las últimas 24 h y base de amoníaco confirmada permiten completar la evaluación: pH, temperatura, TAN/NH3, NO2, NO3, KH, GH. TDS/EC son contextuales y nunca bloquean. Falta de una prueba no impide evaluar las demás, pero se muestra evaluación parcial. Valores caducados/ausentes no se convierten en cero.

NH3: Emerson, dominio conservador pH 6–10 / 0–30 °C y TAN/pH/temperatura de la misma hora. Base total como NH3, total como N o NH3 directo confirmada por usuario. Entrada/visualización de temperatura °F; almacenamiento °C. NO2/NO3 como N convierte ambos a ion; si kits difieren, introducir ambos ya convertidos. No se deducen tóxicos de TDS.

R-01/02/03: NH3 ≥0.20 / ≥0.05 / ≥0.02; R-04 triaje con TAN cuando no puede calcularse. R-05/06/07: NO2 ≥1 / ≥0.25 (≥0.1 con Neocaridina) / detectable. Perfiles R-48 generan alertas por habitantes. Nitrato se compara con el techo más restrictivo, incluso con EI; no crea urgencia aguda por sí solo. Síntomas respiratorios pueden priorizar gravedad sin diagnóstico completo. Tres muestreos diarios reales elevados de NH3 durante ≥48 h pueden generar peligro por persistencia; no prueban exposición continua y requieren confirmar misma base de kit en historial.

## Recomendaciones y límites

Cada regla tiene acciones, plazo de retest, criterios de mejoría/reconsideración y escalación. Hay sugerencias iniciales de 25 %, 50 % o 50–75 % para tóxicos, condicionadas a compatibilidad del agua y retest; no se garantiza que un porcentaje resuelva el problema. Ajustes de dureza separados de riesgo agudo: priorizar TAN antes de subir pH/KH. Reducción de alimentación orientativa para adultos, no universal para alevines. No se dosifican sal, medicamentos, bicarbonato o fertilizantes en mg/L sin datos que los justifiquen.

Seguimiento de plantas y algas es específico por patrón y crecimiento nuevo; no confirma una carencia sin pruebas. Se proponen identificación, limpieza, circulación, ajuste prudente de luz y revisión de abonado; no se prescribe algicida/carbono líquido ni se recomienda añadir fauna como tratamiento.

Se omiten como entradas/requisitos O2, K, PO4, Fe, cobre y calcio conforme al usuario; cloro/Mg siguen opcionales. No hay controles ni recomendaciones de inyección de CO2. Se recupera el cálculo 3 × KH × 10^(7−pH) con pH/KH contemporáneos, también con Stratum, pero marcado como potencialmente sesgado cuando corresponda. Sustrato inerte tampoco confirma tampón exclusivo carbonato. No se considera CO2 medido ni eleva gravedad por sí solo. Objetivo 15–30 no es obligatorio sin inyección.

Se conserva el criterio conservador donde el HTML hace afirmaciones demasiado fuertes: TAN bajo no se llama inocuo, no se certifica ciclado por ceros/edad, no se dosifica amoníaco con fauna, KH bajo no significa crash inevitable, persistencia no se infiere de valores arrastrados y un solo retest no cancela daño ni prueba recuperación. Acondicionadores según etiqueta para agua nueva; sin prometer neutralización de NH3/NO2 ni un puente garantizado de 24–48 h.

## Simulaciones y verificación

Ver `DIAGNOSIS-SIMULATIONS.md`. Doce casos en Diagnóstico → Abrir simulaciones; seleccionado inicialmente el caso del usuario. Incluyen resultado calculado y esperado. No guardan datos. `DiagnosisReferenceTest.kt` compara el caso con NH3 ≈0.038, CO2 ≈8.5, R-03 y R-48 GH/KH, verifica planes, recuperación, persistencia y evaluación sin TDS/EC.

El workflow Android ejecuta pruebas, lint y APK, publicando HTML y XML JUnit. No se ejecutaron Gradle, pruebas, lint, APK ni inspección en dispositivo desde este entorno. Revisión estática y resultados esperados no equivalen a pruebas pasadas. No es implementación íntegra de todas las R-01 a R-48 ni diagnóstico clínico.
