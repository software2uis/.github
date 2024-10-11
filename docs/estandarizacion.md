# Estándares del Proyecto

## Estandarización de los MFEs

### 1. **Estructura del Proyecto**

Se propone dividir los módulos del proyecto de la siguiente forma:

- `src/app/components` (componentes reutilizables)
- `src/app/services` (servicios para lógica de negocio)
- `src/app/models` (interfaces y tipos)

### 2. **Convenciones de Nombres**

- **Componentes:** Usa `PascalCase` con el sufijo `Component` (e.g., `ProductListComponent`).
- **Servicios:** Usa `CamelCase` con el sufijo `Service` (e.g., `ProductService`).
- **Variables:** Sigue `camelCase` para las variables y `SCREAMING_SNAKE_CASE` para constantes.
