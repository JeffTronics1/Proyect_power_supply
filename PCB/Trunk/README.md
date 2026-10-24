# 🛠️ Diseño Esquemático Jerárquico y Layout PCB (Altium Designer)

<div align="center">

![Altium Designer](https://img.shields.io/badge/Altium%20Designer-Project%20Trunk-005596?style=for-the-badge&logo=altiumdesigner)
![Architecture](https://img.shields.io/badge/Arquitectura-Estrictamente%20Jerárquica-8a2be2?style=for-the-badge)
![ERC](https://img.shields.io/badge/Compiler%20ERC-0%20Errors%20%7C%20Validated-brightgreen?style=for-the-badge)
![Components](https://img.shields.io/badge/BOM%20Total-66%20Componentes-informational?style=for-the-badge)

**Archivos nativos de captura esquemática multinivel, validación de reglas eléctricas (ERC), diseño de circuito impreso (PCB) y trabajo de manufactura (OutJob).**

[⬅️ Volver al README Principal](../../README.md) • [📚 Ir a Documentación y Cálculos](../../Documentation/README.md)

</div>

<br>

## 🗂️ 1. Inventario de Archivos en `PCB/Trunk/`

Este directorio (`Trunk`) aloja los archivos editables de diseño electrónico dentro del flujo de trabajo en **Altium Designer**:

| Archivo / Carpeta | Tipo en Altium | Función dentro del Proyecto |
| :--- | :---: | :--- |
| 📁 **`Proyect Power Supply/`** | `Project Folder` | Contiene el archivo maestro de proyecto `Proyect Power Supply.PrjPcb` y el árbol de estructura `.PrjPcbStructure`. |
| 📄 **`000_Hierarchical.SchDoc`** | `Schematic Top` | **Hoja Raíz (Top Sheet):** Contiene los 4 bloques de hoja (*Sheet Symbols*) e interconecta el bus principal `24VDC`. |
| 📄 **`100_AC-DC.SchDoc`** | `Schematic Sub` | **Etapa Primaria AC/DC:** Entrada $18\text{ VAC}_{\text{rms}}$, fusible, varistor, puente rectificador, banco de $20,400\text{ }\mu\text{F}$ y filtro Pi EMI. |
| 📄 **`200_DC-DC.SchDoc`** | `Schematic Sub` | **Etapa Buck Alta Potencia:** Convertidores síncronos `SY8205FCC` para los rieles fijos de `+12V0` ($5\text{ A}$) y `+5V0` ($5\text{ A}$). |
| 📄 **`201_DC-DC_Part2.SchDoc`** | `Schematic Sub` | **Etapa Negativa y Lógica:** Inversor `-12V0` ($1\text{ A}$) con `TPS54360DDAR` y Buck `+3V3` ($3\text{ A}$) con `SY8205FCC`. |
| 📄 **`202_DC-DC_Part3.SchDoc`** | `Schematic Sub` | **Etapa Variable e Instrumentación:** Buck ajustable `1.25V–15.0V` ($3\text{ A}$) con `LM2596SX-ADJ` y conector de voltímetro `J2`. |
| 🟩 **`Main_PCB_V1R0_XXXXXX.PcbDoc`** | `PCB Document` | **Layout de la Placa:** Documento de ruteo físico, definición de capas, planos de potencia y huellas (*footprints*). |
| 📦 **`Power_Supply_MainBoard.OutJob`** | `Output Job` | **Entregables de Fabricación:** Automatización de archivos Gerber, NC Drill, Pick & Place, BOM y esquemáticos en PDF. |

<br>

## 🌳 2. Arquitectura Jerárquica de Conectividad

El proyecto utiliza una estructura **estrictamente jerárquica (*Top-Down / Bottom-Up*)**:

```text
                       ┌───────────────────────────────────────────┐
                       │          000_Hierarchical.SchDoc          │
                       │            (Hoja Raíz / Top)              │
                       └─────────────────────┬─────────────────────┘
                                             │
                       ┌─────────────────────▼─────────────────────┐
                       │              100_AC-DC.SchDoc             │
                       │    Entrada 18 VAC -> Salida Puerto 24VDC  │
                       └─────────────────────┬─────────────────────┘
                                             │  Bus Distribuido 24VDC
           ┌─────────────────────────────────┼─────────────────────────────────┐
           ▼                                 ▼                                 ▼
┌──────────────────────┐         ┌───────────────────────┐         ┌───────────────────────┐
│   200_DC-DC.SchDoc   │         │ 201_DC-DC_Part2.SchDoc│         │ 202_DC-DC_Part3.SchDoc│
│  • +12V0 @ 5A (U1)   │         │  • -12V0 @ 1A (U5)    │         │  • 1.25-15V @ 3A (U4) │
│  • +5V0  @ 5A (U2)   │         │  • +3V3  @ 3A (U3)    │         │  • Voltímetro (J2)    │
└──────────────────────┘         └───────────────────────┘         └───────────────────────┘