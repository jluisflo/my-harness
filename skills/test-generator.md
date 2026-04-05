---
name: test-generator
trigger: Cuando se implementa una feature o se corrige un bug
---

## Que hacer

1. Leer los criterios de aceptación de la tarea en TASK.md
2. Identificar los casos que deben cubrirse:
   - Camino feliz (happy path)
   - Validaciones de input
   - Casos borde definidos en los criterios
3. Escribir tests que verifiquen comportamiento, no implementación
4. Seguir la convención de testing del proyecto (ubicación, naming, framework)

## Principios

- **Un test = una aserción principal.** Si necesitas verificar múltiples cosas, son múltiples tests.
- **Nombrar el test por lo que verifica**, no por lo que ejecuta. `deberia_rechazar_email_invalido` > `test_validacion`.
- **No mockear lo que no es necesario.** Si puedes testear contra la dependencia real sin complejidad excesiva, hazlo.
- **Los tests son documentación.** Alguien que lea solo los tests debería entender qué hace la feature.

## Output esperado

- Tests que cubren los criterios de aceptación de la tarea
- Tests que pasan en un proyecto limpio (no dependen de estado externo no controlado)
- Tests ubicados donde la convención del proyecto los espera

## Errores comunes

- Escribir tests que verifican detalles de implementación en lugar de comportamiento
- Crear tests que pasan siempre (aserciones triviales o vacías)
- No limpiar estado entre tests (dependencia de orden de ejecución)
- Testear código de terceros en lugar del propio
- Ignorar los criterios de aceptación y testear otra cosa
