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


# Cómo contribuir al proyecto

Este es el proceso recomendado para contribuir a los repositorios del proyecto. Sigue estos pasos para asegurar un flujo de trabajo organizado y eficiente.

## 1. Clonar el repositorio
Primero, clona el repositorio en tu máquina local utilizando el siguiente comando:

```bash
git clone https://github.com/usuario/proyecto.git
```

## 2. Crear una nueva rama
Crea una nueva rama para tus cambios siguiendo la convención de nombres de ramas. Por ejemplo:

```bash
git checkout -b feat/nueva-funcionalidad
```

## 3. Realizar los cambios
Haz los cambios necesarios en el código según el requerimiento o la tarea asignada.

## 4. Hacer commit y push de los cambios
Una vez realizados los cambios, realiza un commit y sube la rama a remoto:

```bash
git add .
git commit -m "Descripción de los cambios"
git push origin feat/nueva-funcionalidad
```

## 5. Abrir una Pull Request (PR)
Desde GitHub, abre una Pull Request de tu rama hacia la rama principal (main).

## 6. Asignar a otra persona para revisión
Si no hay un archivo `CODEOWNERS`, asigna a un compañero para que revise la PR. Si el archivo `CODEOWNERS` está configurado, este paso puede omitirse.

## 7. Revisión y aprobación
La persona asignada revisará los cambios. Una vez que se aprueben, se habilitará el botón de **Merge**.

### 7.1 Resolver conflictos
Si existen conflictos con la rama principal, resuélvelos antes de poder hacer el merge.

## 8. Hacer Merge (Squash and Merge)
Finalmente, haz el merge utilizando la opción **Squash and Merge** para combinar todos los commits en un solo commit en la rama `main`.

¡Gracias por tu contribución!
