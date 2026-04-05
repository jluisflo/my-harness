# TECH — [Nombre del feature]

## Proyectos afectados

| Proyecto | Stack | Tipo de cambio |
|---|---|---|
| (nombre-repo) | (.NET / JS / Python / Flutter) | (nuevo servicio / modificacion / consumo de API) |

## Decisiones tecnicas

### (Decision 1: ej. patron de autenticacion)

- **Opcion elegida:** (que)
- **Razon:** (por que)
- **Alternativas descartadas:** (cuales y por que)

### (Decision 2)

- **Opcion elegida:**
- **Razon:**
- **Alternativas descartadas:**

## Dependencias nuevas

| Proyecto | Dependencia | Version | Justificacion |
|---|---|---|---|
| | | | |

## Riesgos tecnicos

| Riesgo | Probabilidad | Impacto | Mitigacion |
|---|---|---|---|
| | (alta/media/baja) | (alto/medio/bajo) | |

## Notas de arquitectura

(Cualquier consideracion sobre como los cambios se integran con la arquitectura existente: layers afectados, patrones a seguir, restricciones)

## Contrato de interfaz

(Solo si este feature involucra comunicacion entre servicios o entre backend y frontend. Si no aplica, omitir esta seccion.)

```json
{
  "feature": "nombre-del-feature",
  "endpoints": [
    {
      "method": "POST",
      "path": "/api/resource",
      "description": "Descripcion de lo que hace este endpoint",
      "request": {
        "field_name": "string",
        "field_name_2": "number"
      },
      "response": {
        "status": 201,
        "body": {
          "id": "string",
          "field_name": "string",
          "created_at": "datetime"
        }
      },
      "errors": [
        { "status": 400, "code": "VALIDATION_ERROR", "when": "descripcion" }
      ]
    }
  ],
  "states": ["idle", "loading", "success", "error"]
}
```

(Para features cross-project complejos donde el contrato es extenso, se puede extraer a un contract.json separado por conveniencia.)
