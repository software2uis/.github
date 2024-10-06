# Guía de Configuración de Repositorios

Esta guía detalla el proceso de creación y configuración de los repositorios para nuestro proyecto, asegurando una estructura consistente y buenas prácticas de desarrollo.

## Índice
1. [Creación de Repositorios](#creación-de-repositorios)
2. [Gestión de Permisos](#gestión-de-permisos)
3. [Configuración de Protección de Ramas](#configuración-de-protección-de-ramas)
4. [Estructura del README](#estructura-del-readme)
5. [Configuración de CODEOWNERS](#configuración-de-codeowners)
6. [Workflows de GitHub Actions](#workflows-de-github-actions)

## Creación de Repositorios

1. Crea dos repositorios siguiendo la convención de nombres:
   - `msv-catalogo` para el microservicio de catálogo
   - `mfe-catalogo` para el microfrontend de catálogo

   Repite este proceso para los demás componentes del proyecto (ej: carro, ordenes).

## Gestión de Permisos

1. Agrega a todos los miembros del equipo asociado a la funcionalidad como `maintainers`.
2. Agrégate a ti mismo como `admin` para facilitar la gestión y permitir commits directos a `main` cuando sea necesario.

## Configuración de Protección de Ramas

1. Configura un ruleset para proteger la rama `main`. Esto se puede hacer importando el siguiente [JSON](./protection.json) en la configuración de rulesets del repositorio:

```json
{
  "id": 2112226,
  "name": "protection",
  "target": "branch",
  "source_type": "Repository",
  "source": "software2uis/msv-catalogo",
  "enforcement": "active",
  "conditions": {
    "ref_name": {
      "exclude": [],
      "include": [
        "~DEFAULT_BRANCH"
      ]
    }
  },
  "rules": [
    {
      "type": "deletion"
    },
    {
      "type": "non_fast_forward"
    },
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 1,
        "dismiss_stale_reviews_on_push": true,
        "require_code_owner_review": true,
        "require_last_push_approval": true,
        "required_review_thread_resolution": true
      }
    }
  ],
  "bypass_actors": [
    {
      "actor_id": 5,
      "actor_type": "RepositoryRole",
      "bypass_mode": "always"
    },
    {
      "actor_id": 1,
      "actor_type": "OrganizationAdmin",
      "bypass_mode": "always"
    },
    {
      "actor_id": null,
      "actor_type": "DeployKey",
      "bypass_mode": "always"
    }
  ]
}
```

Este ruleset:
- Previene eliminaciones de la rama principal
- Requiere pull requests para todos los cambios
- Exige al menos una aprobación antes de fusionar
- Requiere revisión de los propietarios del código
- Requiere la resolución de todos los comentarios antes de fusionar

## Estructura del README

Actualiza el README de cada repositorio con la siguiente [estructura](https://github.com/software2uis/msv-catalogo/blob/main/README.md) (adaptándola según sea necesario):

```markdown
# [Nombre del Repositorio]
[Breve descripción del propósito del repositorio]

## Documentación
[Enlace o información sobre la documentación completa del proyecto]

## Configuración de Desarrollo
### Prerrequisitos
- Instala [Node.js](https://nodejs.org) que incluye [npm](https://www.npmjs.com/get-npm).

### Configuración del Proyecto
[Cómo clonar, instalar dependencias, qué versiones, etc.]

### Contribución
Por favor, sigue nuestras [Guías de Contribución](https://github.com/software2uis/.github/blob/main/CONTRIBUTING.md) para conocer el proceso de envío y las reglas de codificación.
```


## Configuración de CODEOWNERS

1. Crea una carpeta `.github` en la raíz del repositorio.
2. Dentro de esta carpeta, crea un archivo `CODEOWNERS`.
3. Añade el siguiente contenido al archivo, ajustando el nombre del equipo según corresponda:

   ```
   * @software2uis/catalogo
   ```

   Para los otros repositorios, usa `@software2uis/carro` o `@software2uis/ordenes` según corresponda.

## Workflows de GitHub Actions

Crea dos workflows en la carpeta `.github/workflows/`:

1. `label-prs.yml` para etiquetar PRs basadas en el nombre de la rama:

```yaml
name: "Label PRs based on branch name"

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  assign-label:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Assign Enhancement Label
      if: startsWith(github.head_ref, 'feat/')
      env:
        GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: gh pr edit ${{ github.event.number }} --add-label "enhancement"

    - name: Assign Fix Label
      if: startsWith(github.head_ref, 'fix/')
      env:
        GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: gh pr edit ${{ github.event.number }} --add-label "fix"
```

2. `auto-assign-author.yml` para asignar automáticamente el autor a las PRs:

```yaml
name: Auto Author Assign

on:
  pull_request_target:
    types: [ opened, reopened ]

permissions:
  pull-requests: write

jobs:
  assign-author:
    runs-on: ubuntu-latest
    steps:
      - uses: toshimaru/auto-author-assign@v2.1.1
```

Estos workflows son opcionales pero altamente recomendados para mejorar la automatización y la gestión del proyecto.
