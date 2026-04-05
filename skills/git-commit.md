---
name: git-commit
trigger: Cuando se completa una tarea o unidad de trabajo atómica
---

## Que hacer

1. Verificar el estado actual del repositorio con `git status`
2. Revisar los cambios con `git diff` para confirmar que solo incluyes lo relacionado a la tarea
3. Agregar archivos específicos con `git add <archivo>` — nunca `git add .` ni `git add -A`
4. Escribir el commit siguiendo la convención del proyecto

## Convención de mensajes

```
<type>(<scope>): <descripción corta>

<cuerpo opcional: por qué se hizo este cambio>
```

Types permitidos:
- `feat` — nueva funcionalidad
- `fix` — corrección de bug
- `refactor` — reestructuración sin cambio de comportamiento
- `test` — agregar o modificar tests
- `docs` — documentación
- `chore` — tareas de mantenimiento (dependencias, config)

## Output esperado

Un commit atómico que:
- Contiene solo los cambios de una tarea o unidad lógica
- Tiene un mensaje que explica el por qué, no el qué
- No incluye archivos no relacionados
- No incluye archivos sensibles (.env, credentials, secrets)

## Errores comunes

- Hacer commits gigantes con múltiples tareas mezcladas
- Usar `git add .` e incluir archivos no deseados
- Mensajes genéricos como "fix bug" o "update code"
- Hacer commit de archivos con secretos o credenciales
- Usar `--no-verify` para saltear hooks sin justificación
