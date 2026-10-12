# YouTube Morphe 21.40.163 — Optimización 03 (sin desactivar parches)

**Estado:** cambio de código preparado; compilación y validación en Android pendientes.

## Objetivo
Conservar los 96 parches aplicados en la Estabilización 02, sin desactivar funciones ni cambiar sus preferencias. Modificar únicamente el hot path del filtro Litho.

## Cambio realizado
- `LithoFilterPatch.LithoFilterParameters` construye `BufferAsciiStrings` de forma diferida, solo cuando un filtro (o el log de protobuf activado) lo necesita.
- El objeto auxiliar se comparte entre todas las coincidencias dentro de la misma evaluación, como ocurría antes.
- Evita crear el proveedor del mensaje del log de búsqueda cuando la depuración general no está activada; si se activa, el log se mantiene.
- Conserva `LITHO_LAYOUT_THREAD_POOL_SIZE = 1` porque subir los hilos sin corregir primero las carreras del filtrado podría provocar componentes ocultos erróneamente.
- No altera `patches-list.json`, versiones objetivo, firmas ni lógica funcional de los parches.

## Validación imprescindible
1. Ejecutar compilación Gradle en `MorpheApp/morphe-patches` rama `dev`, usando el override de este repositorio.
2. Obtener MPP compilado y parchear el **APK limpio** de YouTube 21.40.163 (no el APK modificado de Estabilización 02).
3. Exigir 96 aplicados, 0 fallidos, reconstrucción y firma correctas.
4. Probar en Android sobre el mismo dispositivo las mismas acciones antes y después, observando jank en el feed, Shorts, cambiar de pestaña y reproducción.

No se puede afirmar que el parche haya eliminado los tirones sin esas pruebas. El paquete oficial 1.48.0-dev.3 no contiene esta optimización.

## Código optimizado
`optimization/overrides/extensions/shared-youtube/library/src/main/java/app/morphe/extension/shared/patches/components/LithoFilterPatch.java`

## Compilación en GitHub Actions
`.github/workflows/morphe-youtube-opt03.yml`

Si el workflow no puede acceder a dependencias de Morphe en GitHub Packages, esto se considerará un bloqueo de compilación, no una prueba de rendimiento fallida.
