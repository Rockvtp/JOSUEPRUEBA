# Aquaria Pro · Android nativo

Proyecto separado en Kotlin + Jetpack Compose + Room. No incluye WebView, Capacitor, JavaScript ni servidor. La versión web no cambia. El proyecto exige Android 8 (API 26) o posterior y apunta a API 35.

## Estado de esta entrega

Primera entrega 0.1.0, **no paridad completa con la web**. El código está preparado, pero todavía no se ha compilado ni probado en un dispositivo Android desde este entorno.

Funciones implementadas en el código:

- Crear, editar y eliminar tanques, capacidad, unidad y habitantes.
- Registrar nueve parámetros; conservar valores y fechas de los campos no medidos.
- Guardar en Room, sin cuenta ni internet.
- Historial y eliminación individual/completa con confirmación, sin alterar valores actuales al borrar historial.
- Evaluación por habitantes: criterios compartidos y preferidos de pH, GH, KH y temperatura; criterios generales de amoníaco, nitritos y nitratos; aviso de criterios parciales y mediciones antiguas.
- Precauciones ante amoníaco/nitritos, perfiles provisionales, grupos pequeños y tendencias de pH/temperatura/TDS.
- Temporizador de pH de 60 segundos, pausa, continuación y reinicio. No es una alarma de segundo plano ni guarda mediciones.
- Exportación/importación de JSON mediante el selector nativo de archivos.
- Importación validada de los respaldos JSON actuales de la web. La operación reemplaza los tanques nativos tras confirmación y se ejecuta en una transacción.
- Cabecera original, inicio en cuatro columnas, pestañas de tanques, tarjetas de parámetros en dos columnas y formulario de medición en línea.
- Las seis paletas originales: Ocean, Obsidian, Apple, Facebook, Medusa y Cyberpunk; selector visible en la cabecera.
- Fondo global de imagen mediante el selector Android, eliminación de fondo y transparencia de tarjetas. La imagen se copia al almacenamiento privado y sigue disponible sin acceso al archivo original.
- Los 23 diseños SVG de la galería web, con los mismos trazados y gradientes, dibujados en Canvas nativo mediante AndroidSVG. Selección por tanque/acceso y orden del inicio con huecos, arrastre e intercambio.
- Tipografía Plus Jakarta Sans empaquetada en el APK. Gradle descarga la fuente y su licencia OFL desde un commit fijo de Google Fonts durante la compilación; la app no la descarga ni requiere internet al ejecutarse.
- Avisos locales derivados de mediciones urgentes, fuera de criterio, antiguas o sin fecha; no son notificaciones de segundo plano.
- Cambios y rellenos de agua: volumen real en galones US, agua retirada/añadida, marca y tipo de agua embotellada, notas y fechas reales por parámetro. Los snapshots son independientes del historial original.
- Pruebas posteriores manuales o copiadas del historial con confirmación; corrección y eliminación de operaciones. Los historiales de tanques eliminados permanecen accesibles.
- Balance ideal de GH/KH/TDS/NO3, tabla antes/nueva/estimado/después, criterios del perfil capturado y recomendaciones conservadoras de frecuencia, bloqueadas con urgencias, pruebas incompletas o condiciones no comparables.
- Importación de cambios de agua desde respaldos web o desde datos preservados por entregas Android anteriores. Respaldo único previo a la primera operación/importación y exportación de todos los datos actuales.
- Exportación CSV de mediciones de todos los tanques, con temperatura en °F y protección ante fórmulas de hojas de cálculo.
- Desenfoque de 10 dp en tarjetas y 12 dp en cabecera mediante bitmaps locales, compatible con API 26. El filtro gaussiano se aproxima mediante tres pasadas; no depende de RenderScript ni de APIs exclusivas de Android 12.
- Brillo de los SVG con máscara alfa, campana con la secuencia de cuatro segundos de la web, pulso y brillo de botones, respuesta al pulsar iconos y entrada de formularios. Las sombras se cachean, no se regeneran como bitmaps en cada fotograma.

Lo pendiente anunciado de cambios de agua y efectos está implementado en el código. **La fidelidad píxel por píxel sigue sin estar validada**: hace falta compilar el APK y comparar capturas en el dispositivo. No debe confundirse una implementación nativa equivalente con evidencia de igualdad visual exacta.

## Cómo obtener el primer APK desde GitHub

1. Sincroniza estos archivos nuevos con el repositorio, incluyendo `.github/workflows/android-apk.yml` y `src/android/`.
2. En GitHub abre **Actions → Android - APK de prueba → Run workflow**. Si Actions solicita habilitación, habilítalo para tu repositorio.
3. Espera a que termine. El flujo instala SDK 35 y Gradle 8.9; ejecuta pruebas unitarias, lint y compilación de depuración.
4. Si termina correctamente, abre esa ejecución y descarga **Aquaria-Android-APK-prueba-N** en **Artifacts**. Descomprime el ZIP: contiene `app-debug.apk`.
5. Transfiere el APK a tu teléfono y ábrelo. Android puede pedir permiso para instalar desde el navegador o administrador de archivos que estés usando.

El flujo es manual y no se ha ejecutado desde esta conversación. Guardar los archivos aquí no equivale a subirlos o compilarlos en GitHub. Si falla, no habrá APK válido y los informes de esa ejecución permitirán corregir el problema.

### Firma de prueba

Es un APK firmado para depuración, no una publicación de Google Play. No se añadieron claves privadas al repositorio. La firma de depuración puede cambiar entre ejecuciones de GitHub, por lo que una versión posterior podría no instalarse encima de la anterior. **Exporta siempre los datos antes de desinstalar**. Una firma personal estable y su almacenamiento seguro deben configurarse antes de distribuir actualizaciones permanentes. No desinstales una versión con datos sin respaldo.

## Trasladar datos sin perder información

1. En la web: **Cambios de agua → Datos actuales completos**. Guarda el JSON.
2. En Android: botón de configuración → **Importar respaldo**; selecciona el JSON.
3. Revisa la confirmación de reemplazo. Si ya tienes datos Android, expórtalos primero.
4. Se activan tanques, habitantes, valores, fechas y mediciones. Los registros antiguos sin `measuredKeys` no se convierten en nuevas pruebas. También se restauran temas, transparencia, fondo de imagen, iconos y orden del inicio cuando están incluidos en el JSON.
5. Los cambios de agua se activan en Android conservando los snapshots y perfiles originales, incluidas operaciones de tanques eliminados. Los demás datos web, como la configuración completa de recordatorios, permanecen en `preservedWebData`; esa configuración no programa notificaciones Android. La web nunca es modificada por esta importación.
6. El respaldo Android incluye `appearance`, con la imagen embebida, y `waterChanges`, en el esquema de operaciones web. Puede restaurarse en Android sin depender de rutas del dispositivo anterior.
7. Antes de la primera operación o importación se conserva un respaldo único anterior en una fila separada de Room. La base no cambia de esquema ni usa migraciones destructivas; los cambios de agua no alteran el historial de mediciones original.

El JSON de Android es su propio formato; no hay importación web de ese formato en esta entrega. Conserva también tu respaldo web original. No hay acceso automático al almacenamiento del navegador. Un respaldo con operaciones inválidas se rechaza antes de reemplazar datos. Si falla el guardado de apariencia después de importar los tanques, se avisa explícitamente y se conserva la configuración visual anterior.

## Pendientes de validación y migración adicional

- Compilar y comparar capturas del APK con la web; corregir diferencias observadas en el dispositivo.
- Centro completo de alarmas y recordatorios mediante APIs Android, permisos, reprogramación y pruebas con restricciones de batería. No se garantiza exactitud de alarmas sin diseñar esa integración.
- Firma estable, pruebas instrumentadas/emulador y validación física de instalación, rotación, restauración, selectores de archivos y temporizador.

Las animaciones generales y el fondo de video se discutieron, pero no estaban implementados en la web; no se presentan como funciones existentes migradas.

## Verificación

Hay 30 pruebas unitarias preparadas en `app/src/test/.../WaterTest.kt` y `WaterChangesTest.kt`; **no se han ejecutado aquí**. Incluyen criterios por habitantes, conservación de fechas, conversiones, respaldos, volúmenes, snapshots, pruebas posteriores, balance de mezcla, tanques archivados y bloqueos de recomendaciones con urgencias, agua nueva no caracterizada o rellenos intermedios.

Se realizó inspección estática de la integración y de los recursos; no equivale a compilar Kotlin ni ejecutar las pruebas. Las comprobaciones TypeScript de la web no verifican Kotlin, Gradle, Room ni Compose. Un APK compilado y pruebas en Android son requisitos antes de considerar esta entrega validada para uso real. La base Room no utiliza migraciones destructivas ni permisos de internet. Se deshabilitó el respaldo automático del sistema en esta primera entrega; utiliza la exportación explícita.
