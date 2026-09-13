
Proyecto de Data Science para comparar la rentabilidad, el riesgo y el tiempo de
recuperación de la inversión de distintos tipos de negocio en Colombia, usando
datos reales de fuentes oficiales (DANE, Confecámaras, gremios sectoriales) y
simulación financiera (Monte Carlo, análisis de sensibilidad).

## Pregunta de investigación

> Dado un capital disponible **X** (COP) y un horizonte de análisis de 3–5 años,
> ¿cómo se comparan distintos tipos de negocio en términos de **TIR esperada**,
> **riesgo de pérdida** (probabilidad de VAN < 0), **tiempo de recuperación**
> (payback) y **sensibilidad** a variables macro (arriendo, inflación, demanda)?
> ¿Qué combinación de capital y perfil de riesgo favorece a cada tipo de negocio?

No buscamos un "mejor negocio" único — el resultado depende del capital
disponible y de la tolerancia al riesgo del inversionista. Por eso el proyecto
termina en un dashboard interactivo, no en un ranking fijo.

## Negocios comparados

| Negocio | Por qué se incluye |
|---|---|
| Restaurante | CAPEX medio-alto, márgenes ajustados, alta tasa de mortalidad, muy sensible a ubicación |
| Droguería | Demanda inelástica (salud), pero mercado muy concentrado en grandes cadenas |
| Parqueadero | Ingreso muy ligado a ubicación/tráfico; CAPEX puede ser bajo o alto según el modelo |
| Tienda de barrio / minimercado | Segmento más grande de micronegocios en Colombia, mejor cobertura de datos DANE |
| Lavandería autoservicio | Modelo de bajo componente operativo/personal, tendencia creciente, poco estudiado |

## Principio de trabajo: datos reales vs. estimaciones vs. supuestos

Este proyecto distingue explícitamente tres tipos de cifras en cada resultado:

- **Dato real**: viene directo de una fuente oficial citada (ej. ingreso
  promedio de EMICRON para un sector y ciudad específicos).
- **Estimación**: se deriva de datos reales pero requiere un cálculo o
  supuesto adicional (ej. costo de nómina calculado a partir de personal
  ocupado promedio + salario mínimo + prestaciones).
- **Supuesto**: no hay dato público disponible y se define un rango
  justificado (ej. tasa de rotación de una lavandería autoservicio).

Cada notebook y cada tabla de resultados debe dejar claro a cuál de las tres
categorías pertenece cada cifra. No se inventan datos sin marcarlos como
supuesto.

## Metodología (fases del proyecto)

- **Fase 0 — Marco y alcance**: ciudad(es) de análisis, horizonte temporal,
  tasa de descuento de referencia.
- **Fase 1 — Recolección y limpieza de datos reales**: microdatos EMICRON por
  CIIU, series de IPC por división, informes de creación/liquidación de
  empresas (Confecámaras), muestreo de arriendos comerciales.
- **Fase 2 — Modelo financiero por negocio**: P&L parametrizado (mismo
  esqueleto para los 5 negocios), escenarios pesimista/base/optimista, cálculo
  de VAN, TIR, payback y punto de equilibrio.
- **Fase 3 — Simulación Monte Carlo**: distribuciones de probabilidad
  ajustadas a los datos reales disponibles, 10.000+ iteraciones por negocio y
  nivel de capital.
- **Fase 4 — Análisis de sensibilidad**: tornado charts y correlación entre
  variables de entrada y VAN de salida.
- **Fase 5 — Comparación entre negocios**: tabla resumen por nivel de capital,
  ratio riesgo/retorno.
- **Fase 6 — Dashboard interactivo**: capital disponible, ciudad, horizonte y
  supuestos ajustables.

## Fuentes de datos

| Fuente | Qué aporta | Enlace |
|---|---|---|
| DANE — EMICRON (microdatos) | Ingresos, costos y personal ocupado por micronegocio y sector CIIU | https://microdatos.dane.gov.co/index.php/catalog/875 |
| DANE — EMICRON (boletines agregados) | Cifras agregadas sin necesidad de registro | https://www.dane.gov.co/index.php/estadisticas-por-tema/mercado-laboral/micronegocios |
| Datos Abiertos Colombia (EMICRON) | Alternativa de descarga sin registro DANE | https://www.datos.gov.co/dataset/Encuesta-de-Micronegocios-EMICRON-2023/axyw-yxnc |
| DANE — IPC por división de gasto | Inflación específica de alimentos, restaurantes, salud, etc. | https://www.dane.gov.co/index.php/estadisticas-por-tema/precios-y-costos/indice-de-precios-al-consumidor-ipc/ipc-historico |
| Confecámaras — RUES | Natalidad/mortalidad empresarial, tasas de supervivencia por sector | https://confecamaras.org.co |
| Portales inmobiliarios (Fincaraíz, Metrocuadrado) | Proxy de precios de arriendo comercial (sin índice oficial en Colombia para uso comercial) | — |

> **Nota:** a diferencia del arriendo de vivienda (regulado por la Ley 820 de
> 2003 e indexado al IPC), el arriendo de locales comerciales en Colombia se
> pacta libremente entre las partes. No existe un índice oficial equivalente,
> por lo que cualquier cifra de arriendo comercial usada en este proyecto es
> un muestreo propio de anuncios, no una estadística oficial.

## Estructura del repositorio

```
├── data/
│   ├── raw/               # Archivos originales descargados (no versionar si son pesados/con licencia restringida)
│   └── processed/         # CSV limpios e intermedios (ingresos_por_negocio.csv, ipc_por_division.csv, etc.)
├── notebooks/
│   ├── 01_datos_emicron.ipynb          # Carga y filtrado de microdatos EMICRON
│   ├── 02_datos_contexto.ipynb         # IPC, Confecámaras, arriendos
│   ├── 03_modelo_por_negocio.ipynb     # Ensambla escenarios y estados financieros
│   ├── 04_montecarlo_sensibilidad.ipynb
│   └── 05_comparacion_resultados.ipynb
├── src/
│   └── finanzas.py         # Funciones reutilizables: P&L, VAN, TIR, payback, punto de equilibrio
├── app/
│   └── app_dashboard.py    # Dashboard interactivo (Streamlit)
├── requirements.txt
└── README.md
```

## Estado actual

- [x] Diseño del estudio (pregunta de investigación, negocios, variables, fuentes, metodología)
- [x] Identificación de fuentes de datos reales y enlaces de descarga
- [ ] `01_datos_emicron.ipynb`: carga y exploración inicial de microdatos (en curso)
- [ ] `02_datos_contexto.ipynb`
- [ ] `src/finanzas.py`
- [ ] `03_modelo_por_negocio.ipynb`
- [ ] `04_montecarlo_sensibilidad.ipynb`
- [ ] `05_comparacion_resultados.ipynb`
- [ ] `app_dashboard.py`

## Cómo reproducir

1. Descargar los microdatos de EMICRON desde el catálogo de DANE (requiere
   registro gratuito) o desde Datos Abiertos Colombia.
2. Colocar el archivo en `data/raw/`.
3. Abrir `notebooks/01_datos_emicron.ipynb` en Google Colab o Jupyter y
   ejecutar las celdas en orden. El notebook exporta un CSV limpio a
   `data/processed/`.
4. Repetir con `02_datos_contexto.ipynb` para las demás fuentes.
5. Continuar con los notebooks de modelo una vez existan los CSV procesados.

## Limitaciones conocidas

- EMICRON cubre micronegocios de hasta 9 personas ocupadas — no representa
  negocios más grandes ni franquicias.
- No existe fuente pública con estado de resultados detallado de un negocio
  "promedio" para cada categoría; los resultados se triangulan entre varias
  fuentes y requieren supuestos explícitos, documentados en cada notebook.
- El arriendo comercial no tiene índice oficial en Colombia (ver nota en la
  sección de fuentes).
