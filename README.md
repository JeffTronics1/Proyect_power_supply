# ⚡ Fuente de Alimentación Multisalida (Altium & Excel)

<div align="center">

![Hardware](https://img.shields.io/badge/Hardware-Open%20Source-orange?style=for-the-badge&logo=open-source-hardware)
![Altium Designer](https://img.shields.io/badge/Altium%20Designer-23.0+-blue?style=for-the-badge&logo=altiumdesigner)
![Excel](https://img.shields.io/badge/Microsoft%20Excel-Calculator-green?style=for-the-badge&logo=microsoftexcel)
![Status](https://img.shields.io/badge/Estado-En%20Desarrollo-brightgreen?style=for-the-badge)

**Diseño electrónico integral, dimensionamiento matemático en Excel y layout de PCB multicapa en Altium Designer.**

[📊 Cálculos en Excel](#-memoria-de-cálculo-excel) • [📐 Diagramas y PCB](#-diseño-en-altium-designer) • [⚡ Especificaciones](#-especificaciones-técnicas) • [📂 Estructura](#-estructura-del-repositorio)

</div>

---

## 📌 Descripción del Proyecto

Este repositorio contiene el diseño completo de una **Fuente de Alimentación Multisalida de Alta Eficiencia y Bajo Ruido**, diseñada para aplicaciones de laboratorio, embebidos y prototipado.

El flujo de trabajo combina:
1. **Modelado Matemático y Dimensionamiento Térmico:** Hoja de cálculo parametrizada en Microsoft Excel para cálculo de componentes, disipación de potencia y estimación de ripple.
2. **Diseño Electrónico en Altium Designer:** Diagramas esquemáticos modulares, selección de componentes con catálogo activo y diseño de PCB optimizado para integridad de potencia (PI) y compatibilidad electromagnética (EMC).

---

## ⚡ Especificaciones Técnicas

| Salida | Voltaje Nom. | Corriente Máx. | Topología / Regulador | Ripple Estimado |
| :---: | :---: | :---: | :---: | :---: |
| **Salida 1** | $5.0\text{ V}$ (Fijo) | $3.0\text{ A}$ | Regulador Conmutado (Buck) | $< 20\text{ mV}_{pp}$ |
| **Salida 2** | $3.3\text{ V}$ (Fijo) | $1.5\text{ A}$ | LDO / Buck de Alta Eficiencia | $< 10\text{ mV}_{pp}$ |
| **Salida 3** | $0.0 - 24.0\text{ V}$ (Ajustable) | $0.0 - 2.0\text{ A}$ | Regulador Lineal Ajustable | $< 5\text{ mV}_{pp}$ |
| **Salida 4** | $-12.0\text{ V}$ / $+12.0\text{ V}$ | $500\text{ mA}$ | Inversor / Carga Simétrica | $< 15\text{ mV}_{pp}$ |

> **Entrada Principal:** $12.0\text{ V} - 24.0\text{ V DC}$ (o Jack de entrada con protección contra polaridad inversa).

---

## 📐 Arquitectura del Sistema

```
                        ┌─────────────────────────────────┐
                        │   Entrada DC (12V - 24V)       │
                        └────────────────┬────────────────┘
                                         │
                   ┌─────────────────────┴─────────────────────┐
                   │   Filtro EMI y Protección Térmica/OVP    │
                   └─────────────────────┬─────────────────────┘
                                         │
        ┌───────────────────┬────────────┴───────┬───────────────────┐
        ▼                   ▼                    ▼                   ▼
┌───────────────┐   ┌───────────────┐   ┌─────────────────┐   ┌───────────────┐
│ Canal 5.0V    │   │ Canal 3.3V    │   │ Canal Variable  │   │ Canal Doble   │
│ Buck Step-Down│   │ LDO / Buck    │   │ 0-24V Linear    │   │ ±12V Dual     │
└───────────────┘   └───────────────┘   └─────────────────┘   └───────────────┘
```

---

## 🧮 Memoria de Cálculo (Excel)

El archivo de Excel incluido en la carpeta `/Calculos_Excel/` permite realizar el cálculo automático de los siguientes parámetros clave:

- **Cálculo de Inductancias y Capacitancias:** Cálculo del valor crítico $L_{min}$ para regulación Buck continua y $C_{out}$ según el rizado admisible.
- **Análisis Térmico:** Estimación de la temperatura de junta ($T_j$) para reguladores lineales mediante:
  $$T_j = T_a + P_d \cdot (\theta_{jc} + \theta_{cs} + \theta_{sa})$$
- **BOM y Costos:** Estimación automatizada de costos con footprint y número de parte del fabricante.

---

## 🛠️ Diseño en Altium Designer

El diseño en Altium Designer incluye las siguientes características clave:

- **Esquemático Hierárquico:** Separación clara por bloques funcionales (Etapa de Entrada, Regulación Fija, Regulación Variable, Control).
- **PCB Stackup y Routing:** 
  - Capas dedicadas para planos de masa (**GND Ground Plane**).
  - Pistas anchas dimensionadas por criterio de temperatura e intensidad de corriente según norma **IPC-2221**.
  - Plano de disipación térmica con vías (Thermal Vias Matrix) bajo los componentes de potencia.

---

## 🖼️ Galería del Proyecto

<div align="center">

| Vista 3D del PCB | Diagrama Esquemático |
| :---: | :---: |
| *(Agrega aquí la imagen 3D de Altium: `![PCB 3D](docs/images/pcb_3d.png)`)* | *(Agrega aquí la imagen del Esquemático: `![Schematic](docs/images/schematic.png)`)* |

</div>

---

## 📂 Estructura del Repositorio

```text
├── Altium_Project/
│   ├── Schematics/         # Hojas de esquemáticos (.SchDoc)
│   ├── PCB/                # Layout de la placa (.PcbDoc)
│   ├── Libraries/          # Librerías personalizadas (.SchLib, .PcbLib)
│   └── Outputs/            # Gerbers, NC Drill, Pick & Place y BOM
├── Calculos_Excel/
│   └── Calculadora_Fuente_Multisalida.xlsx  # Hoja de cálculos dimensionales
├── Documentation/
│   ├── Datasheets/         # Hojas de datos de componentes críticos
│   └── Images/             # Capturas del diseño y PCB 3D
├── .gitignore
└── README.md               # Documentación principal
```

---

## 🚀 Cómo Usar este Repositorio

1. **Revisar los Cálculos:** Abre el archivo `Calculadora_Fuente_Multisalida.xlsx` en Microsoft Excel para ajustar los valores de salida o corriente según tus necesidades.
2. **Abrir el Proyecto en Altium:** Abre el archivo de proyecto `.PrjPcb` dentro de la carpeta `Altium_Project/`.
3. **Generar Archivos de Fabricación:** Navega a la carpeta `Outputs/` para encontrar los archivos Gerber $RS-274X$ y tablas de perforación listos para enviar a fabricar (JLCPCB, PCBWay, etc.).

---

## 🤝 Contribución y Licencia

Desarrollado como proyecto de diseño de hardware libre. Sientete libre de clonar, modificar y mejorar el diseño.

- **Licencia:** MIT Hardware License / Open Source.
- **Contacto / Soporte:** Si encuentras un problema o tienes sugerencias, por favor abre un *Issue* o envía un *Pull Request*.