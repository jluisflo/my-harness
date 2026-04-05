---
name: build-check
trigger: Despues de implementar cambios y antes de hacer commit
---

## Que hacer

1. Ejecutar el comando de build del proyecto para verificar que compila sin errores
2. Ejecutar la suite de tests existente para verificar que no se rompió nada
3. Si hay linter configurado, ejecutarlo
4. Verificar que el proyecto arranca correctamente si aplica

## Comandos por stack

Identificar el stack del proyecto y usar el comando correspondiente. Ejemplos comunes:

| Stack | Build | Tests | Lint |
|---|---|---|---|
| .NET | `dotnet build` | `dotnet test` | (depende del proyecto) |
| Node/JS | `npm run build` | `npm test` | `npm run lint` |
| Python | (depende del proyecto) | `pytest` | (depende del proyecto) |
| Flutter | `flutter build` | `flutter test` | `flutter analyze` |

Siempre verificar en el proyecto cuales son los comandos reales antes de ejecutar.

## Output esperado

Confirmación de que:
- El proyecto compila sin errores
- Los tests existentes pasan
- No se introdujeron warnings nuevos (si el proyecto los trata como errores)

## Si algo falla

- **Error de compilación**: Corregir antes de continuar. No hacer commit de código que no compila.
- **Test roto**: Verificar si lo rompieron tus cambios. Si es un test preexistente que ya fallaba, documentar en el changelog.
- **Warning nuevo**: Evaluar si es relevante. Si lo es, corregir. Si no, documentar por que se ignora.

## Errores comunes

- Hacer commit sin verificar que el build pasa
- Asumir que "compila en mi contexto" significa que funciona
- Ignorar tests rotos asumiendo que no son responsabilidad propia
- No verificar que el proyecto arranca despues de cambios en configuración
