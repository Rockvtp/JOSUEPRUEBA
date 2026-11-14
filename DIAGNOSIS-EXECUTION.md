# Ejecutar el motor y obtener salidas completas

## Estado

El usuario aportó los informes de la ejecución 16 (`43354709402`, commit `bd5f82fc0ca49943ef727e8a65377dea5d95aec7`): 42 controles de los dos casos coincidentes, 110 pruebas unitarias aprobadas (incluidas las 7 de claridad) y lint con 9 advertencias, sin errores reportados. Esa evidencia corresponde a la versión anterior, no a la nueva conexión con registros.

La ejecución de **contexto desde registros** está preparada, no realizada desde este chat. Solo JUnit genera las salidas reales; no se fabrican resultados esperados como si fueran ejecutados.

## Cómo ejecutar desde GitHub

1. Sincroniza esta versión del código con el repositorio de GitHub.
2. Abre **Actions → Android - APK de prueba → Run workflow**. Inicia una ejecución nueva; no uses **Re-run jobs** de la ejecución anterior, porque conserva su commit antiguo.
3. Selecciona la rama que contiene estos cambios y confirma la ejecución.
4. Revisa **Ejecutar motor Kotlin - referencias y contexto desde registros**. Ejecuta `DiagnosisExecutionReportTest`, `DiagnosisClarityTest` y `DiagnosisRecordedContextTest`, forzando una ejecución nueva de las tareas.
5. En **Summary** aparece un resumen basado exclusivamente en los archivos producidos por la prueba, con `diagnosis-clarity-v2`, origen `tank-and-water-change-records-v1` y el commit actual.
6. Descarga el nuevo artefacto **Diagnostico-ejecucion-real-[número]** y **Android-informes-[número]** en **Artifacts**. Las 12 pruebas nuevas de contexto están en JUnit, no se incluyen en los 42 controles del JSON.
7. Comparte ambos ZIP aquí para revisar los resultados. Si falla algún paso, comparte también sus logs.

No hace falta abrir la app, instalar el APK o conectar un teléfono para ejecutar el motor. Sí se compila el código Kotlin de Android antes de las pruebas. Una compilación fallida impide obtener resultados y se informa como tal.

## Qué ejecuta

La prueba llama a `diagnoseRecordedTank`, la misma ruta utilizada por `DiagnosisScreen`: obtiene contexto desde registros y llama al motor existente `diagnoseTank`. No ejecuta el motor HTML ni reconstruye su salida: compara contra los valores y hallazgos de las salidas del HTML compartidas por el usuario.

Casos:

- A: pH 7.2, 73 °F, TAN 5 total como NH3, NO2 0, NO3 10, KH 4.5, GH 4.
- B: idéntico, cambiando solo pH a 8.0.

Fauna: corydoras, neón y caracol manzana. Recuentos ficticios 6/6/1 (el usuario confirmó especies, no cantidades); no cambian las bandas químicas del modelo. Sustrato inerte, pocas plantas, luz 9.5 h, sin TDS/EC. Método líquido fijo, pruebas simultáneas, sin síntomas/eventos ni historial adicional, sin fecha de inicio y sin registros de cambios son las condiciones de los casos. No son datos reales de un tanque del usuario.

La prueba no escribe en Room ni cambia tanques. El motor se evalúa a una fecha de referencia fija para evitar que datos ficticios caduquen. La fecha real de ejecución, commit y GitHub run también se registran, por separado.

## Contenido de los informes

- Entradas, unidades, fechas y habitantes.
- Salida real de NH3, fracción libre, CO2 con sus limitaciones, severidad, confianza y completitud.
- Interpretación de todos los parámetros.
- Todas las fichas: regla, evidencia, recomendaciones, plazo de revisión, mejoría y escalación.
- Plan conjunto: Ahora / Próxima revisión / Seguimiento.
- Referencias esperadas claramente separadas de la salida calculada.
- Comprobaciones numéricas y de recomendaciones específicas para el caracol y el neón.
- Diferencias intencionales que requieren revisión humana: Crítico/Urgencia, cambios condicionados al agua, KH puntual, confianza de GH, CO2 orientativo, advertencias de recuperación y fotoperiodo.

Los informes se escriben **antes** de la afirmación final de la prueba. Si hay diferencias, la prueba falla pero se publican las salidas obtenidas para investigar. Si el motor lanza una excepción o no compila antes de exportar, no se fabrica un informe: revisar los logs y JUnit.

## Comprobaciones de las correcciones v2

El informe incluye **21 controles por caso (42 en total)**: los 14 originales más 7 sobre separación de confianza, riesgo sin daño confirmado, escalación apropiada al nivel y KH puntual sin obligación de corregir por décimas. Se conservan los valores químicos esperados y las severidades originales.

`DiagnosisClarityTest` añade **7 pruebas de regresión**: urgencia sin síntomas, persistencia/deterioro, escalación de niveles inferiores, síntomas compatibles no confirmatorios, método deficiente con síntomas, datos incompletos/no contemporáneos y KH puntual. Sus resultados están en JUnit dentro de **Android-informes-[número]**, no dentro de las comparaciones del JSON de los dos casos.

El campo global `confidence` sigue representando calidad de datos; el de cada ficha representa confianza de interpretación. Ambos están etiquetados en Android y Markdown. En amoníaco, sin síntomas concordantes declarados, la interpretación es como máximo Media; una calidad de datos Baja no aumenta por declarar síntomas. Los datos básicos incompletos o NH₃ no calculable no reciben confianza de datos Alta. Ninguna reducción de confianza cancela las acciones preventivas.

## Contexto automático desde registros

- En Crear/Editar pecera se registra opcionalmente la fecha real de inicio del acuario, incluso anterior al registro en la app. Se guarda como `startedOn` (AAAA-MM-DD) en el payload JSON de Room y en el respaldo; no requiere cambiar el esquema Room ni borrar datos. Los tanques anteriores quedan sin fecha hasta que el usuario la registre.
- El diagnóstico calcula la edad por días de calendario local. No pide una edad fija ni interpreta antigüedad como prueba de ciclado.
- El método es líquido fijo en la pantalla y en `diagnoseRecordedTank`. Se conservan las advertencias de calidad y la selección explícita de base del test de amoníaco. El motor puro conserva otros métodos para sus pruebas de calidad.
- El último cambio es el de mayor fecha no futura, del mismo `tankId`, con `kind=change`. Rellenos, operaciones futuras y cambios de otros tanques no cuentan. El porcentaje proviene de agua retirada / volumen previo de esa operación, no de la capacidad actual del tanque.
- Fecha de inicio o cambios ausentes dan `null`, no cero. No bloquean NH₃, CO₂ ni la evaluación básica. Una operación de hoy sí puede dar cero días, porque es un dato real.
- La pantalla espera la carga inicial de Room y reacciona a cambios del tanque o del historial. Si falla la lectura del historial, avisa y permite evaluar sin esos datos; no modifica ni reemplaza el historial.

`DiagnosisRecordedContextTest` incorpora 12 pruebas: edad que avanza, edad entregada al motor, selección por tanque/fecha, eliminación del último cambio, cero real frente a ausencia, método líquido con advertencias de calidad, conservación completa de ambos casos, contexto EI desde cambios, ida y vuelta de payload/respaldo, datos antiguos/sin fecha y rechazo de fecha futura/inválida.

`SpeciesThresholdTest` incorpora 6 pruebas de concordancia: rangos de las cuatro especies iguales a los de Diagnóstico, intersecciones del tanque 1 incluido KH, techos de nitratos inclusivos, techo más restrictivo en tanques mixtos/Cambios de agua y eliminación de preferencias/textos antiguos. El primer paso ejecuta también `WaterTest` y `WaterChangesTest`; sus resultados están en **Android-informes**, no en los 42 controles del JSON. La suite completa esperada es de 128 pruebas; no se afirma que estén aprobadas hasta revisar una ejecución nueva. Las fórmulas y el motor de diagnóstico no se modificaron.

Después de obtener el APK, exporta un respaldo antes de actualizar (la firma de depuración puede variar). En el teléfono:

1. Edita una pecera existente, registra su fecha de inicio y guarda. Vuelve a abrirla y comprueba que la fecha se conserve.
2. Abre Diagnóstico y confirma método líquido fijo y edad automática, sin campos duplicados.
3. Registra un cambio en Cambios de agua y revisa días/porcentaje en Diagnóstico. Registra después un relleno: no debe reemplazar ese cambio.
4. Prueba otra pecera sin fecha ni cambios: debe mostrar sin información y permitir evaluar con mediciones válidas.
5. Exporta/restaura un respaldo y comprueba fecha, cambios y mediciones. La prueba física de interfaz/Room sigue siendo necesaria; JUnit aquí comprueba serialización y lógica, no la base en un dispositivo.

## Cómo interpretar el estado

- **Coinciden:** pasan los controles numéricos y de textos/acciones de ambos casos. No significa que las 7 regresiones de claridad, las 12 de contexto, lint o APK hayan pasado: comprobar también el estado de los pasos y JUnit. No implica identidad de todo el HTML ni validación clínica.
- **Hay diferencias:** consultar los controles fallidos y las salidas completas. No alterar los valores esperados solo para hacer pasar la prueba.
- **No hay salida:** la prueba no generó informe; no hay ejecución del motor demostrada.

El resumen comprueba commit, ejecución y origen del contexto. Se publica antes de las pruebas generales/lint/APK para conservar la ejecución de referencia aunque una verificación posterior falle. Después continúa la verificación Android habitual. Ningún éxito de pruebas demuestra que la interfaz se vea correctamente; eso requiere comprobar el APK.
