# Base de Datos de Proyectos CES (Chile)

![Python](https://img.shields.io/badge/Python-3.12-blue)
![Pandas](https://img.shields.io/badge/pandas-data%20processing-orange)
![Status](https://img.shields.io/badge/status-active-success)

## Descripción del Proyecto

Herramienta que extrae, procesa y consolida la información pública de los proyectos de **Certificación Edificio
Sustentable (CES)** en Chile, publicada en [certificacionsustentable.cl](https://www.certificacionsustentable.cl/).

El script recorre el sitio oficial, captura los datos de cada proyecto (mandante, arquitecto, asesor, entidad
evaluadora, puntaje, ubicación e indicadores de desempeño) y genera una base de datos en CSV, lista para análisis o
para herramientas de Business Intelligence como Power BI.

### Características Principales
- **Scraping automatizado** de las cuatro categorías: En Proceso, Pre-Certificación, Certificación y Sello Plus.
- **Indicadores de desempeño** de cada proyecto: ahorro de energía, reducción de emisiones de CO2, de consumo de agua
  y de residuos (en %).
- **Procesamiento concurrente** con 5 workers en paralelo.
- **Consolidación** en un único archivo maestro, con una fila por proyecto y estado (sin duplicados).
- **Robustez y logging**: reintentos ante errores de conexión y registro de la ejecución.
- **Limpieza automática** de los archivos intermedios.

## Estructura del Proyecto

```
proyectos-ces-chile-database/
├── data/
│   └── raw/                  # CSV generados (no se versionan)
├── utils/
│   └── scraper.py            # Lógica de requests y reintentos
├── scrape_projects.py        # Script principal: proyectos CES
├── scrape_assessors.py       # Script: registro oficial de asesores CES
├── requirements.txt          # Dependencias
├── README.md                 # Documentación general
└── MANUAL_DE_USUARIO.md      # Guía detallada de uso y columnas
```

## Instalación y Uso

1.  **Clonar el repositorio**:
    ```bash
    git clone https://github.com/roruizf/proyectos-ces-chile-database.git
    cd proyectos-ces-chile-database
    ```

2.  **Crear un entorno con Python 3.12 e instalar dependencias** (recomendado con [uv](https://docs.astral.sh/uv/)):
    ```bash
    uv venv --python 3.12
    uv pip install -r requirements.txt
    ```

3.  **Ejecutar el scraper de proyectos**:
    ```bash
    .venv/bin/python scrape_projects.py
    ```
    Genera `data/raw/[CES]_Projects_Full_List-YYYY_MM_DD.csv`.

4.  **Ejecutar el scraper de asesores** (opcional):
    ```bash
    .venv/bin/python scrape_assessors.py
    ```
    Genera `data/raw/[CES]_Registro_AsesoresCES_Full_List-YYYY_MM_DD.csv`.

Para el detalle de cada paso y de las columnas, consulta el [Manual de Usuario](MANUAL_DE_USUARIO.md).

## Tecnologías Utilizadas
-   **Python 3.12**
-   **Requests y lxml** para la extracción HTML.
-   **Pandas** para la limpieza y la estructuración de los datos.
-   **openpyxl** para leer el registro de asesores (Excel).

## Datos

Los CSV no se versionan (`data/` está en `.gitignore`): se regeneran ejecutando los scripts. La extracción del
2026-11-10 obtuvo 733 filas: 132 en proceso, 412 pre-certificaciones, 187 certificaciones y 2 Sello Plus.
