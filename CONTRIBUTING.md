# Guía de Contribución

Este documento describe el estándar para nombrar ramas y escribir mensajes de commits en este proyecto.

## Ramas

Las ramas deben crearse según la Historia de Usuario (HU) correspondiente. El formato es:

```
[tipo]/[nombre-descriptivo]
```

### Tipos:
- **feat**: nueva funcionalidad.
- **fix**: corrección de errores.
- **chore**: mantenimiento o configuraciones.

### Ejemplo:
- `feat/ver-detalles-producto`

### Pasos:
1. Crear la rama:
   ```bash
   git checkout -b feat/ver-detalles-producto
   ```
2. Hacer commits.
3. Subir cambios:
   ```bash
   git push origin feat/ver-detalles-producto
   ```

## Commits

El formato de los mensajes de commit es:

```bash
[acción]([componente]): [descripción corta]
```

### Acciones:
- **add**: agregar nueva funcionalidad o archivos.
- **update**: mejorar o modificar código existente.
- **fix**: corregir errores.
- **remove**: eliminar archivos o funcionalidades.
- **refactor**: reestructurar código sin cambiar su funcionalidad.
- **docs**: actualizar documentación.

### Ejemplos:

#### "Ver detalles del producto":
- `add(ui): display product details page`
- `update(api): improve product stock availability check`

#### "Sugerir productos":
- `add(search): implement product autocomplete`
- `fix(search): correct dropdown behavior`

Mantén los mensajes claros y en inglés.
