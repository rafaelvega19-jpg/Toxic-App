# Toxic App - Emisiones Tóxicas en Puerto Rico

## Descripción
Aplicación web interactiva desarrollada en Observable Framework que visualiza 
los datos del Inventario de Emisiones Tóxicas (TRI) de Puerto Rico desde 1987 
hasta 2024, obtenidos de la Agencia de Protección Ambiental de los Estados 
Unidos (EPA).

**Fuente de datos:** https://www.epa.gov/toxics-release-inventory-tri-program/tri-basic-data-files-calendar-years-1987-present

## Objetivo
El objetivo principal de esta aplicación es permitir al usuario explorar y 
analizar las emisiones tóxicas reportadas en Puerto Rico de manera visual e 
interactiva. La aplicación facilita identificar patrones por municipio, sector 
industrial y químico a través del tiempo.

## Funcionalidades
- **Mapa interactivo** con la localización de cada emisión tóxica en Puerto Rico
- **Colores por sector industrial** para identificar visualmente el tipo de industria
- **Popup informativo** al hacer click en cada punto del mapa con:
  - Año
  - Municipio
  - Nombre de la Facilidad
  - Sector Industrial
  - Químico
- **Selector de año** (1987-2024) para explorar datos históricos
- **Slider de zoom** para ajustar el nivel de detalle del mapa
- **Leyenda interactiva** con opción de mostrar/ocultar
- **Histograma de municipios** con los 20 municipios con más emisiones
- **Histograma de sectores industriales** con la distribución por industria

## Tecnologías Utilizadas
- **Observable Framework** - generador de sitios estáticos
- **Arquero** - manipulación y procesamiento de datos
- **Observable Plot** - visualización de histogramas
- **Leaflet.js** - mapa interactivo
- **Mapbox** - tiles del mapa satelital

## Proceso de Desarrollo
El desarrollo de esta aplicación se realizó en varias versiones:

1. **Versión 1** - Tres histogramas básicos (municipio, sector industrial y químico)
2. **Versión 2** - Se añadió el mapa interactivo con leyenda
3. **Versión 3** - Se añadió selector de año, zoom del mapa y toggle de leyenda
4. **Versión Final** - Diseño completo con layout optimizado, integración de 
   datos históricos 1987-2024 y mejoras estéticas

## Cómo ejecutar localmente
```bash
npm install
npm run dev
```

## Deployment
La aplicación está desplegada en:
```
https://cdat.uprh.edu/~rafael.vega19/toxic-app
```
