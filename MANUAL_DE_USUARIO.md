# Manual de Usuario - Base de Datos de Proyectos CES (Chile)

## Introducción
Esta herramienta extrae automáticamente la información pública de los proyectos del sitio de Certificación Edificio
Sustentable (CES), la procesa y genera una base de datos consolidada en CSV.

## Requisitos Previos
1.  **Python 3.12** (recomendado en un entorno aislado creado con `uv`).
2.  **Conexión a Internet** para acceder al sitio de CES.

## Instalación

```bash
uv venv --python 3.12
uv pip install -r requirements.txt
```

## Uso

### 1. Actualizar la base de datos de proyectos

```bash
.venv/bin/python scrape_projects.py
```

**Qué hace este comando:**
1.  Recorre las cuatro categorías: `en-proceso`, `pre-certificacion`, `certificacion` y `sello-plus`.
2.  Descarga la ficha de cada proyecto con 5 workers concurrentes.
3.  Guarda archivos temporales por categoría en `data/raw/`.
4.  Genera el consolidado `[CES]_Projects_Full_List-YYYY_MM_DD.csv`, con una fila por proyecto y estado.
5.  Borra los archivos intermedios.

**Tiempo estimado:** unos minutos para ~730 proyectos (noviembre de 2026).

### 2. Actualizar el registro de asesores

```bash
.venv/bin/python scrape_assessors.py
```

**Qué hace este comando:**
1.  Descarga el Excel oficial de asesores acreditados
    (`https://storage.certificacionsustentable.cl/static/asesores_acreditados.xlsx`).
2.  Estandariza las fechas (de español a ISO).
3.  Filtra las columnas relevantes (RUT, email, región, etc.).
4.  Genera `data/raw/[CES]_Registro_AsesoresCES_Full_List-YYYY_MM_DD.csv`.

> El registro de asesores contiene datos personales publicados por CES (RUT, correo, teléfono). Úsalo solo para
> fines compatibles con su publicación y no lo subas al repositorio: `data/` está en `.gitignore`.

## Estructura de datos del consolidado de proyectos

| Columna | Contenido |
|---|---|
| `project_name` | Nombre del proyecto |
| `status` | Categoría en el sitio: `en-proceso`, `pre-certificacion`, `certificacion` o `sello-plus` |
| `project_url` | URL de la ficha del proyecto (clave para eliminar duplicados) |
| `project_image_url`, `project_entry_date` | Imagen y fecha de ingreso |
| `ahorro_energia_pct`, `reduccion_co2_pct`, `reduccion_agua_pct`, `reduccion_residuos_pct` | Indicadores de desempeño en %; vacío si la ficha muestra "–%" (no se postuló a ese requisito) |
| `mandante`, `arquitecto`, `unidad_tecnica` | Participantes del proyecto |
| `asesor`, `entidad_evaluadora` | Asesor CES y entidad evaluadora |
| `asesor_precertificacion`, `entidad_evaluadora_precertificacion`, `asesor_certificacion`, `entidad_evaluadora_certificacion` | Participantes por etapa, cuando la ficha los separa |
| `region`, `comuna` | Ubicación |
| `version_certificacion`, `nivel_obtenido`, `fecha_logro_obtenido`, `puntaje_obtenido` | Resultado de la certificación |

## Solución de Problemas

-   **Error de conexión**: el script reintenta automáticamente; si persiste, revisa la conexión.
-   **Interrupción** (`Ctrl+C`): vuelve a ejecutar el script; el proceso parte de nuevo.
-   **Columnas vacías**: si el sitio cambia su HTML, los selectores de `scrape_projects.py` deben actualizarse.
