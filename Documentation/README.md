# 📚 Documentación de Ingeniería, Cálculos y Arquitectura

<div align="center">

![Excel](https://img.shields.io/badge/Excel-Design%20%26%20Thermal%20Calc-217346?style=for-the-badge&logo=microsoftexcel)
![Datasheets](https://img.shields.io/badge/Datasheets-Reference%20PDFs-EC1C24?style=for-the-badge&logo=adobeacrobatreader)
![Draw.io](https://img.shields.io/badge/Draw.io-Architecture%20Diagrams-F08705?style=for-the-badge&logo=diagramsdotnet)
![Power](https://img.shields.io/badge/Potencia%20Total-151.9%20W-ffb400?style=for-the-badge)

**Memoria de cálculo matemático, balance energético y térmico por convertidor, hojas de datos oficiales y diagramas de arquitectura del sistema.**

[⬅️ Volver al README Principal](../README.md) • [🛠️ Ir a Esquemáticos y PCB (Trunk)](../PCB/Trunk/README.md)

</div>

<br>

## 📂 1. Estructura del Directorio `Documentation/`

Esta carpeta centraliza toda la ingeniería previa y de soporte que justifica cada valor de componente colocado en los esquemáticos de Altium Designer:

* 📁 **`Capture_the_buck_calculator/`** — Capturas de pantalla de la calculadora en Excel desarrollada para cada regulador DC/DC (cálculo de potencia de entrada, corriente de entrada, eficiencia, potencia disipada e inductancias críticas según su respectivo *datasheet*).
* 📁 **`Datasheets/`** — Hojas de datos oficiales de los fabricantes para los convertidores conmutados (`SY8205FCC`, `TPS54360DDAR`, `LM2596SX-ADJ/NOPB`) y elementos de potencia.
* 📁 **`Diagrams/`** — Archivos editables en formato `.drawio` y exportaciones en alta resolución `.png` del **Diagrama de Bloques** del sistema y del **Árbol de Potencias (*Power Tree*)**.
* 📊 **`Power_supply_design.xlsx`** — Libro maestro de ingeniería con 5 hojas estructuradas: Especificaciones Eléctricas, Requisitos Funcionales, Requisitos No Funcionales, Restricciones de PCB y Lista de Materiales (BOM) consolidada de 66 componentes.

<br>

## 🧮 2. Memoria de Cálculo de los Convertidores (`Capture_the_buck_calculator/`)

Cada etapa de conversión fue dimensionada matemáticamente a partir de las ecuaciones de los fabricantes. En las capturas de esta carpeta se documenta el análisis de potencia de entrada ($P_{in}$), corriente de entrada ($I_{in}$) y potencia disipada ($P_d$) bajo carga nominal máxima:

### ⚡ 2.1 Etapa Primaria AC/DC y Filtro EMI (`100_AC-DC`)
* **Tensión de Entrada AC:** Secundario de transformador de $18\text{ VAC}_{\text{rms}}$ a $60\text{ Hz}$.
* **Tensión Pico Rectificada ($V_p$):**
  $$V_p = V_{\text{rms}} \times \sqrt{2} = 18\text{ VAC} \times 1.414 = 25.46\text{ V}$$
* **Banco de Capacitores de Filtrado ($C_{28}, C_{29}, C_{30}$):** Calculado para sostener una corriente de carga en el bus DC de $I_{\text{carga}} = 6.381\text{ A}$ con un rizado máximo de $V_r = 2.5\text{ V}$ a una frecuencia de rectificación de onda completa $f_{$f_{\text{rect}} = 120\text{ Hz}$} = 120\text{ Hz}$:
  $$C_{\text{total}} = \frac{I_{\text{carga}}}{2 \cdot f_{\text{red}} \cdot V_r} = \frac{6.381\text{ A}}{2 \cdot 60\text{ Hz} \cdot 2.5\text{ V}} = 21,270\text{ }\mu\text{F} \implies \text{Implementado con } 3 \times 6800\text{ }\mu\text{F} = 20,400\text{ }\mu\text{F}$$
* **Filtro Pi EMI de Salida ($L_6, C_{31}, C_{32}, C_{33}$):** Con un inductor $L_6 = 10\text{ }\mu\text{H}$ y capacitores de salida $C_{32} = C_{33} = 10\text{ }\mu\text{F}$ en paralelo ($20\text{ }\mu\text{F}$), atenúa el ruido de conmutación de alta frecuencia proveniente de los 5 reguladores.
* **Indicador Luminoso de Bus Activo ($D_3, R_{15}$):**
  $$R_{15} = \frac{24\text{ V} - 2.1\text{ V}}{20\text{ mA}} = 1.095\text{ k}\Omega \approx 1.1\text{ k}\Omega \quad \Big\vert{} \quad P_{R15} = 21.9\text{ V} \times 20\text{ mA} = 0.438\text{ W} \text{ (Resistencia de } \ge 0.5\text{ W)}$$

### 🔋 2.2 Convertidores Buck Síncronos Fijos (`SY8205FCC` — +12V, +5V y +3.3V)
Operan a una frecuencia de conmutación fija $f_{sw} = 500\text{ kHz}$ con tensión de referencia interna $V_{\text{ref}} = 0.6\text{ V}$ y un criterio de rizado de corriente en el inductor ($\Delta I_L$) del $40\%$ de la corriente máxima de salida:

$$L = \frac{V_{\text{out}} \cdot \left(1 - \frac{V_{\text{out}}}{V_{\text{in(max)}}}\right)}{f_{sw} \cdot I_{\text{out(max)}} \cdot 0.40} \quad \Big\vert{} \quad R_{\text{inf}} = \frac{0.6\text{ V} \cdot R_{\text{sup}}}{V_{\text{out}} - 0.6\text{ V}} \quad \Big\vert{} \quad C_{ss} = \frac{t_{ss} \cdot 10\text{ }\mu\text{A}}{0.6\text{ V}}$$

| Parámetro Calculado | Riel `+12V0` (`U1`) | Riel `+5V0` (`U2`) | Riel `+3V3` (`U3`) |
| :--- | :---: | :---: | :---: |
| **Tensión / Corriente Salida** | $+12.0\text{ V}$ @ $5.0\text{ A}$ | $+5.0\text{ V}$ @ $5.0\text{ A}$ | $+3.3\text{ V}$ @ $3.0\text{ A}$ |
| **Potencia de Salida ($P_{\text{out}}$)** | $60.0\text{ W}$ | $25.0\text{ W}$ | $9.9\text{ W}$ |
| **Inductor Calculado ($L$)** | $L_1 = 6.0\text{ }\mu\text{H}$ | $L_2 = 4.8\text{ }\mu\text{H}$ | $L_3 = 4.8\text{ }\mu\text{H}$ |
| **Divisor Feedback ($R_{\text{sup}} / R_{\text{inf}}$)** | $R_2 = 10\text{ k}\Omega$ / $R_3 = 523\text{ }\Omega$ | $R_5 = 10\text{ k}\Omega$ / $R_6 = 1.37\text{ k}\Omega$ | $R_8 = 10\text{ k}\Omega$ / $R_9 = 2.2\text{ k}\Omega$ |
| **Arranque Suave ($t_{ss} = 6\text{ ms}$)** | $C_2 = 0.1\text{ }\mu\text{F}$ | $C_8 = 0.1\text{ }\mu\text{F}$ | $C_{14} = 0.1\text{ }\mu\text{F}$ |
| **Capacitor Feed-Forward** | $C_5 = 100\text{ pF}$ | $C_{11} = 100\text{ pF}$ | $C_{17} = 100\text{ pF}$ |

### 🔄 2.3 Convertidor Buck-Boost Inversor -12V (`TPS54360DDAR` — `U5`)
Configurado en topología inversora para entregar $-12.0\text{ V}$ @ $1.0\text{ A}$ ($12.0\text{ W}$) desde el bus de $+24\text{ VDC}$:
* **Frecuencia de Conmutación ($f_{sw}$):** Programada en $500\text{ kHz}$ mediante la resistencia $R_{11} = 200\text{ k}\Omega$ en el pin `RT/CLK`.
* **Inductor Principal ($L_5$):** Calculado para soportar la suma de tensiones entrada-salida:
  $$L_5 = \frac{V_{\text{in}} \cdot D}{f_{sw} \cdot \Delta I_L} = 42.66\text{ }\mu\text{H} \implies \text{Valor comercial implementado: } 47\text{ }\mu\text{H}$$
* **Lazo de Realimentación ($V_{\text{ref}} = 0.8\text{ V}$):**
  $$R_{13} = 1\text{ k}\Omega \quad \implies \quad R_{14} = R_{13} \cdot \left(\frac{\vert{}V_{\text{out}}\vert{}}{0.8\text{ V}} - 1\right) = 1\text{ k}\Omega \cdot \left(\frac{12\text{ V}}{0.8\text{ V}} - 1\right) = 14\text{ k}\Omega$$
* **Red de Compensación Tipo II (`COMP`):** Frecuencia de cruce $f_{co} = 7792\text{ Hz}$, polo $f_{p1} = 1102.5\text{ Hz}$ y cero $f_{z2} = 55077\text{ Hz}$, implementada con $R_{12} = 4.7\text{ k}\Omega$ ($4.207\text{ k}\Omega$ teórico), $C_{25} = 68\text{ nF}$ ($68.7\text{ nF}$ teórico) y $C_{26} = 680\text{ pF}$ ($686.9\text{ pF}$ teórico).

### 🎛️ 2.4 Convertidor Buck Ajustable 1.25V – 15.0V (`LM2596SX-ADJ/NOPB` — `U4`)
Regulador step-down de $150\text{ kHz}$ capaz de entregar hasta $3.0\text{ A}$ ($45.0\text{ W}$ máx. a $15\text{ V}$):
* **Ecuación de Ajuste Lineal ($V_{\text{ref}} = 1.23\text{ V}$):** Con resistencia fija $R_{10} = 175\text{ }\Omega$, el potenciómetro $VR_1$ para alcanzar $V_{\text{out(max)}} = 15.0\text{ V}$ se determina por:
  $$VR_1 = R_{10} \cdot \left(\frac{V_{\text{out(max)}}}{1.23\text{ V}} - 1\right) = 175\text{ }\Omega \cdot \left(\frac{15\text{ V}}{1.23\text{ V}} - 1\right) = 1959\text{ }\Omega \approx 2\text{ k}\Omega$$
* **Componentes de Potencia y Estabilidad:** Inductor $L_4 = 47\text{ }\mu\text{H}$, diodo Schottky de rueda libre $D_1$ ($40\text{ V} / 5\text{ A}$), capacitor de entrada $C_{19} = 470\text{ }\mu\text{F}$, salida $C_{21} = 220\text{ }\mu\text{F}$ y capacitor de compensación en paralelo al potenciómetro $C_{20} = 680\text{ pF}$.

<br>

## 📐 3. Diagramas de Arquitectura (`Diagrams/`)

En la subcarpeta `./Diagrams/` se encuentran los archivos fuente en formato **Draw.io (`.drawio`)** junto con sus capturas en **`.png`**:

1. **Diagrama de Bloques (`Block_diagram`):** Detalla la cadena funcional desde la entrada de $18\text{ VAC}_{\text{rms}}$, protecciones primarias (fusible + varistor), rectificación de onda completa, filtrado capacitivo y EMI, hasta la partición en los 5 canales DC/DC y el bloque de instrumentación visual (voltímetro digital en `J2`).
2. **Árbol de Potencias (`Power_tree`):** Ilustra el flujo jerárquico de energía, detallando corrientes por rama, eficiencias de conversión, pérdidas estimadas por calor y distribución de los $151.9\text{ W}$ de potencia máxima instalada.

<br>

## 📊 4. Libro Maestro de Diseño (`Power_supply_design.xlsx`)

El archivo Excel documenta la trazabilidad completa del hardware a través de 5 pestañas técnicas:

| Pestaña en Excel | Contenido Técnico |
| :--- | :--- |
| **`1. Especificaciones`** | Cuadro de carga de las 5 salidas (`OUT_01` a `OUT_05`), tensiones, corrientes máximas ($17\text{ A}$ acumulados), potencia total ($151.9\text{ W}$), topologías con referencias de IC y gráfico de barras de potencia por canal. |
| **`2. Req. Funcionales`** | Trazabilidad de los requerimientos `RF-01` a `RF-08` vinculados directamente con los componentes calculados en cada hoja esquemática. |
| **`3. Req. No Funcionales`** | Definición de `RNF-01` a `RNF-05`: voltímetro LED de 3 hilos en `J2`, indicador LED verde `D3` en el bus de 24V, conectores de potencia, potenciómetro de ajuste `VR1` y organización modular en Altium. |
| **`4. Restricciones`** | Reglas `RD-01` a `RD-09` para el diseño de la PCB: dimensionamiento de pistas para $6.381\text{ A}$ en el bus DC, $17\text{ A}$ en el plano de retorno `GND` y disipación térmica en los *Exposed Pads* (`EP`). |
| **`5. Lista de Componentes`** | **BOM consolidada de 66 componentes** con designadores exactos de Altium (`J1`–`J2`, `F1`, `V1`, `BR1`, `U1`–`U5`, `D1`–`D3`, `L1`–`L6`, `C1`–`C33`, `R1`–`R15`, `VR1`) y código IPC. |

<br>

## 📄 5. Hojas de Datos Oficiales (`Datasheets/`)

* **`SY8205FCC` (Silergy):** Regulador Buck síncrono de alta eficiencia, entrada de $4.5\text{ V}$ a $30\text{ V}$, salida continua de $5\text{ A}$, $500\text{ kHz}$, encapsulado `SO8E` con pad térmico expuesto.
* **`TPS54360DDAR` (Texas Instruments):** Convertidor DC/DC *Eco-mode* de $4.5\text{ V}$ a $60\text{ V}$ y $3.5\text{ A}$, encapsulado `HSOIC-8` (`DDA`), empleado en configuración Buck-Boost inversora para el riel de $-12\text{ V}$.
* **`LM2596SX-ADJ/NOPB` (Texas Instruments):** Regulador conmutado Buck ajustable de $150\text{ kHz}$ y $3.0\text{ A}$ en encapsulado de montaje superficial de potencia `DDPAK / TO-263-5`.