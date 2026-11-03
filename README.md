# XSLOPE 🏗️

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.9%2B-3776AB?style=for-the-badge&logo=python" alt="Python 3.9+" />
  <img src="https://img.shields.io/badge/Docs-xslope.org-2b6cb0?style=for-the-badge" alt="Documentation" />
  <img src="https://img.shields.io/badge/License-Apache%202.0-blue?style=for-the-badge" alt="Apache 2.0" />
</p>

<h2 align="center">
  Documentación, tutoriales y videos: <a href="https://xslope.org"><b>xslope.org</b></a>
</h2>

`xslope` es un paquete de Python de código abierto para el análisis de estabilidad de taludes y flujo de filtración en geotecnia. Responde a la pregunta central de un ingeniero geotécnico en cada corte, terraplén, presa o dique: ¿este talud es estable y con qué margen de seguridad?

## 🌍 Visión general

`xslope` integra tres motores de análisis con un único archivo de entrada:

- 🔹 Análisis por equilibrio límite (LEM): incluye siete métodos de rebanadas — Ordinary Method of Slices, Simplified Janbu, Bishop Simplified, Corps of Engineers, Lowe & Karafiath, Spencer y Morgenstern–Price. Soporta superficies circulares y no circulares, búsqueda automática de la superficie crítica, abatimiento rápido, refuerzo, cargas sísmicas y análisis de confiabilidad por Monte Carlo.
- 💧 Flujo de filtración por elementos finitos: modelado de flujo de agua en medio saturado y no saturado, régimen permanente o transitorio, sobre una malla generada a partir de la misma geometría del talud. Genera el campo de presiones de poros que alimenta los análisis de estabilidad, en lugar de asumir presiones piezométricas simplificadas.
- 🧱 Estabilidad de taludes por elementos finitos: análisis elastoplástico con criterio de Mohr–Coulomb mediante el método de reducción de resistencia al corte (SRM), permitiendo que el mecanismo de falla emerja de forma natural sin imponer una superficie de falla previa.

Los problemas se definen mediante una plantilla en Excel, lo que mantiene un flujo accesible para la práctica profesional, mientras el cálculo y la visualización se realizan en Python. Además, existe una aplicación de escritorio llamada XSLOPE Studio que ofrece una interfaz gráfica para acceder a las mismas capacidades.

## 🧩 Estructura del proyecto

La base del repositorio está organizada de la siguiente manera:

```text
xslope/
├── xslope/                  # Núcleo principal del paquete Python
│   ├── __init__.py
│   ├── advanced.py
│   ├── cad.py
│   ├── fem.py
│   ├── fileio.py
│   ├── mesh.py
│   ├── plot.py
│   ├── search.py
│   ├── seep.py
│   ├── solve.py
│   ├── summary.py
│   ├── units.py
│   ├── reliability.py
│   └── resources/           # Plantillas y recursos del paquete
├── docs/                    # Documentación, ejemplos y tutoriales
├── notebooks/               # Cuadernos de trabajo
├── studio/                  # Aplicación XSLOPE Studio
├── test/                    # Pruebas del proyecto
├── tools/                   # Utilidades y validaciones
├── packaging/               # Empaquetado y distribución
├── benchmarks/              # Casos de referencia y comparaciones
├── README.md                # Este archivo
├── pyproject.toml           # Configuración del paquete y dependencias
├── LICENSE.txt              # Licencia Apache 2.0
├── CHANGELOG.md             # Historial de cambios
├── CITATION.cff             # Metadatos de citación
├── mkdocs.yml               # Configuración de la documentación
└── main_*.py                # Scripts de ejemplo y uso directo
```

## ⚙️ Instalación

`xslope` requiere Python 3.9 o superior y se publica en PyPI.

```bash
pip install xslope
```

Esto instala lo necesario para análisis por equilibrio límite. Los extras opcionales agregan funcionalidades adicionales:

```bash
pip install "xslope[fem]"        # flujo de filtración y análisis por FEM (agrega gmsh)
pip install "xslope[gui]"        # XSLOPE Studio, aplicación de escritorio
pip install "xslope[ai]"         # asistente IA del Studio
pip install "xslope[cad]"        # importación/exportación DXF
pip install "xslope[gui,fem,ai,cad]"  # todo incluido
```

Para iniciar la interfaz gráfica:

```bash
xslope-studio
```

En Debian/Ubuntu (incluido Google Colab), `gmsh` puede requerir librerías del sistema OpenGL; en ese caso se recomienda ejecutar:

```bash
apt-get install -y libgl1 libglu1-mesa
```

## 🚀 Ejemplo rápido

Un problema se define a partir de un archivo Excel. El siguiente ejemplo carga un archivo de muestra y resuelve un caso simple:

```bash
curl -O https://raw.githubusercontent.com/njones61/xslope/main/docs/inputs/slope/xslope_simple1.xlsx
```

```python
from xslope.fileio import load_slope_data
from xslope.slice import generate_slices
from xslope.solve import solve_selected
from xslope.plot import plot_solution

slope_data = load_slope_data("xslope_simple1.xlsx")

circle = slope_data['circles'][0]
success, result = generate_slices(slope_data, circle=circle, num_slices=20)
slice_df, failure_surface = result

results = solve_selected('bishop', slice_df)
print(f"FS = {results['FS']:.3f}")

plot_solution(slope_data, slice_df, failure_surface, results)
```

También es posible buscar la superficie crítica automáticamente:

```python
from xslope.search import circular_search
from xslope.plot import plot_circular_search_results

fs_cache, converged, search_path, circle_cache = circular_search(slope_data, 'bishop')
print(f"minimum FS = {fs_cache[0]['FS']:.3f}")

plot_circular_search_results(slope_data, fs_cache, search_path)
```

Más ejemplos están disponibles en `docs/inputs/slope/` y la plantilla base puede generarse con:

```python
from xslope.fileio import default_template_path
print(default_template_path())
```

## 📚 Documentación

La documentación completa incluye:

- teoría y fundamentos
- referencia de plantillas de entrada
- ejemplos resueltos
- uso de XSLOPE Studio
- validación frente a benchmarks publicados

Consulta el sitio oficial:

- https://xslope.org
- https://xslope.org/en/stable/

## ✅ Calidad, validación y contribución

El proyecto cuenta con una suite de validación y mecanismos para verificar resultados frente a referencias publicadas. Además, se mantiene una estructura pensada para desarrolladores y usuarios avanzados.

Si deseas contribuir:

1. Haz un fork del repositorio.
2. Crea una rama de trabajo.
3. Realiza cambios documentados y pruebas relevantes.
4. Envía un pull request con una descripción clara del problema y la solución.

## 🤝 Soporte y solicitudes

Para preguntas, errores o nuevas funcionalidades puedes abrir un issue en GitHub:

- https://github.com/njones61/xslope/issues

Si no puedes compartir información pública o no dispones de cuenta en GitHub, también se puede contactar por correo:

- njones@byu.edu
- xslope@jonesgeo.com

## 📌 Citas

Si usas `xslope` en trabajo publicado, por favor cita el artículo:

> Jones, N. L., & Williams, G. P. (2026). xslope: An open, validated Python package and desktop application for integrated seepage and slope stability analysis. SoftwareX, 36, 103018.

También se incluye la referencia bibliográfica en formato machine-readable en:

- `CITATION.cff`

## 🧾 Licencia

Este proyecto se distribuye bajo la licencia Apache License, Version 2.0. Consulta el archivo [LICENSE.txt](LICENSE.txt) para más detalles.

## ©️ Copyright

Copyright 2025 Norman L. Jones

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

```text
http://www.apache.org/licenses/LICENSE-2.0
```

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

---

<p align="center">
  <b>XSLOPE</b> — análisis integrado de flujo y estabilidad de taludes
</p>
