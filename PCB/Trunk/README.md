# 🛠️ Diseño Esquemático Jerárquico, Ecuaciones y Layout PCB (Altium Designer)

<div align="center">

![Altium Designer](https://img.shields.io/badge/Altium%20Designer-Project%20Trunk-005596?style=for-the-badge&logo=altiumdesigner)
![Architecture](https://img.shields.io/badge/Arquitectura-Estrictamente%20Jerárquica-8a2be2?style=for-the-badge)
![ERC](https://img.shields.io/badge/Compiler%20ERC-0%20Errors%20%7C%20Validated-brightgreen?style=for-the-badge)
![Math](https://img.shields.io/badge/Diseño-Ecuaciones%20en%20Esquemático-informational?style=for-the-badge)

**Archivos nativos de captura esquemática multinivel, memorias de cálculo integradas, validación de reglas eléctricas (ERC), diseño de circuito impreso (PCB) y trabajo de manufactura (OutJob).**

[⬅️ Volver al README Principal](../../README.md) • [📚 Ir a Documentación y Cálculos Térmicos](../../Documentation/README.md)

</div>

<br>

## 🗂️ 1. Inventario de Archivos en `PCB/Trunk/`

| Archivo / Carpeta | Tipo en Altium | Función dentro del Proyecto |
| :--- | :---: | :--- |
| 📄 **`Proyect Power Supply.PrjPcb`** | `Altium PCB Project` | **Archivo Maestro de Proyecto:** Enlaza todos los esquemáticos y la placa PCB en un solo flujo jerárquico. |
| 📄 **`Proyect Power Supply.PrjPcbStructure`** | `PRJPCBSTRUCTURE` | Archivo de metadatos estructurales que define la jerarquía de las hojas del proyecto en Altium. |
| 📁 **`Proyect Power Supply/`** | `File Folder` | Carpeta de caché generada automáticamente que contiene los historiales locales de Altium (`History`) y los reportes de compilación/errores (`Project Logs`). |
| 📄 **`000_Hierarchical.SchDoc`** | `Altium Schematic` | **Hoja Raíz (Top Sheet):** Símbolos de hoja e interconexión del bus `24VDC`. |
| 📄 **`100_AC-DC.SchDoc`** | `Altium Schematic` | **Etapa AC/DC:** Entrada $18\text{ VAC}_{\text{rms}}$, rectificación, filtrado capacitivo y EMI. |
| 📄 **`200_DC-DC.SchDoc`** | `Altium Schematic` | **Bucks Fijos Alta Potencia:** `+12V0` ($5\text{ A}$) y `+5V0` ($5\text{ A}$) con `SY8205FCC`. |
| 📄 **`201_DC-DC_Part2.SchDoc`** | `Altium Schematic` | **Inversor y Lógica:** `-12V0` ($1\text{ A}$) con `TPS54360DDAR` y `+3V3` ($3\text{ A}$) con `SY8205FCC`. |
| 📄 **`202_DC-DC_Part3.SchDoc`** | `Altium Schematic` | **Ajustable:** `1.25V–15.0V` ($3\text{ A}$) con `LM2596SX-ADJ` y conector `J2` (Voltímetro). |
| 🟩 **`Main_PCB_V1R0_XXXXXX.PcbDoc`** | `Altium PCB Document`| **Layout de la Placa:** Documento de ruteo físico y planos de potencia. |
| 📦 **`Power_Supply_MainBoard.OutJob`** | `Altium Output Job` | **Entregables:** Generación automatizada de archivos Gerber, NC Drill y BOM. |

<br>

## 🧮 2. Ecuaciones de Diseño (Plasmadas en las Hojas Esquemáticas)

Toda la memoria de cálculo para la selección de inductores, capacitores de filtrado y redes resistivas fue calculada según los *datasheets* e incrustada como plantillas de texto directamente en las hojas de Altium Designer:

### ⚡ Etapa Primaria AC/DC y Filtro EMI (`100_AC-DC.SchDoc`)
* **Tensión Pico Rectificada ($V_p$):** 
  $$V_p = V_{\text{rms}} \times 1.414 = 18\text{ VAC} \times 1.414 = 25.46\text{ V}$$
* **Banco Capacitivo Principal ($C_{28}, C_{29}, C_{30}$):** Rizado máximo $V_r = 2.5\text{ V}$ a frecuencia de red rectificada $f_{\text{rect}} = 120\text{ Hz}$ ($f_{\text{red}} = 60\text{ Hz}$).
  $$C_{\text{total}} = \frac{I_{\text{carga}}}{2 \cdot f_{\text{red}} \cdot V_r} = \frac{6.381\text{ A}}{2 \cdot 60\text{ Hz} \cdot 2.5\text{ V}} = 21,270\text{ }\mu\text{F} \implies 3 \times 6800\text{ }\mu\text{F} = 20,400\text{ }\mu\text{F}$$
* **Frecuencia de Corte Filtro EMI ($L_6 = 10\text{ }\mu\text{H}$, $C_{32/33} = 20\text{ }\mu\text{F}$):**
  $$f_c = \frac{1}{2 \pi \sqrt{L \cdot C}} = 11.25\text{ kHz}$$
* **Indicador LED Bus 24V ($D_3$):** Resistencia limitadora para $20\text{ mA}$.
  $$R_{15} = \frac{24\text{ V} - 2.1\text{ V}}{20\text{ mA}} = 1.095\text{ k}\Omega \approx 1.1\text{ k}\Omega$$

### 🔋 Convertidores Buck Fijos (`200_DC-DC.SchDoc` y `201_DC-DC_Part2.SchDoc`)
Utilizan el IC `SY8205FCC` ($f_{sw} = 500\text{ kHz}$, $V_{\text{ref}} = 0.6\text{ V}$).
* **Inductor Crítico (Rizado de corriente del 40%):**
  $$L = \frac{V_{\text{out}} \cdot \left(1 - \frac{V_{\text{out}}}{V_{\text{in(max)}}}\right)}{f_{sw} \cdot I_{\text{out(max)}} \cdot 0.40}$$
  * *Resultado +12V:* $L_1 = 6.0\text{ }\mu\text{H}$
  * *Resultado +5V / +3.3V:* $L_2, L_3 = 4.8\text{ }\mu\text{H}$
* **Divisor de Tensión (Feedback):** Fijando $R_{\text{sup}} = 10\text{ k}\Omega$.
  $$R_{\text{inf}} = \frac{0.6\text{ V} \cdot 10\text{ k}\Omega}{V_{\text{out}} - 0.6\text{ V}}$$
* **Tiempo de Arranque Suave ($t_{ss} = 6\text{ ms}$):**
  $$C_{ss} = \frac{t_{ss} \cdot 10\text{ }\mu\text{A}}{0.6\text{ V}} = 0.1\text{ }\mu\text{F}$$

### 🔄 Inversor Buck-Boost -12V (`201_DC-DC_Part2.SchDoc`)
Utiliza el `TPS54360DDAR` configurado como inversor ($f_{sw} = 500\text{ kHz}$, $V_{\text{ref}} = 0.8\text{ V}$).
* **Inductor para Topología Inversora:**
  $$L_5 = \frac{V_{\text{in}} \cdot D}{f_{sw} \cdot \Delta I_L} = 42.66\text{ }\mu\text{H} \implies 47\text{ }\mu\text{H}$$
* **Red de Realimentación Inversora:** Fijando $R_{13} = 1\text{ k}\Omega$.
  $$R_{14} = R_{13} \cdot \left(\frac{\vert{}V_{\text{out}}\vert{}}{0.8\text{ V}} - 1\right) = 14\text{ k}\Omega$$

### 🎛️ Convertidor Buck Ajustable (`202_DC-DC_Part3.SchDoc`)
Utiliza el `LM2596SX-ADJ/NOPB` ($f_{sw} = 150\text{ kHz}$, $V_{\text{ref}} = 1.23\text{ V}$).
* **Cálculo del Potenciómetro para $15\text{ V}$ máximos:** Fijando $R_{10} = 175\text{ }\Omega$.
  $$VR_1 = R_{10} \cdot \left(\frac{V_{\text{out(max)}}}{1.23\text{ V}} - 1\right) = 175\text{ }\Omega \cdot \left(\frac{15\text{ V}}{1.23\text{ V}} - 1\right) = 1959\text{ }\Omega \approx 2\text{ k}\Omega$$

<br>

## 🌳 3. Arquitectura Jerárquica de Conectividad

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