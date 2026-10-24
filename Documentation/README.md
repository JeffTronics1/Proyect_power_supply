# 📚 Documentación de Ingeniería, Cálculos Térmicos y Arquitectura

<div align="center">

![Excel](https://img.shields.io/badge/Excel-Thermal%20%26%20Power%20Calc-217346?style=for-the-badge&logo=microsoftexcel)
![Datasheets](https://img.shields.io/badge/Datasheets-Reference%20PDFs-EC1C24?style=for-the-badge&logo=adobeacrobatreader)
![Draw.io](https://img.shields.io/badge/Draw.io-Architecture%20Diagrams-F08705?style=for-the-badge&logo=diagramsdotnet)
![Power](https://img.shields.io/badge/Potencia%20Total-151.9%20W-ffb400?style=for-the-badge)

**Análisis de balance energético, cálculos de disipación térmica por convertidor, hojas de datos oficiales y diagramas de arquitectura del sistema.**

[⬅️ Volver al README Principal](../README.md) • [🛠️ Ir a Esquemáticos y PCB (Trunk)](../PCB/Trunk/README.md)

</div>

<br>

## 📂 1. Estructura del Directorio `Documentation/`

Esta carpeta centraliza el análisis de potencia, requerimientos del proyecto y la arquitectura del sistema:

* 📁 **`Capture_the_buck_calculator/`** — Capturas de pantalla de la calculadora en Excel (*Power Dissipation Calcu.*) que evalúa el comportamiento térmico y el consumo de corriente de cada regulador DC/DC.
* 📁 **`Datasheets/`** — Hojas de datos oficiales de los fabricantes para los convertidores conmutados (`SY8205FCC`, `TPS54360DDAR`, `LM2596SX-ADJ/NOPB`) y semiconductores de potencia.
* 📁 **`Diagrams/`** — Archivos editables en formato `.drawio` y exportaciones `.png` del **Diagrama de Bloques** y del **Árbol de Potencias (*Power Tree*)**.
* 📊 **`Power_supply_design.xlsx`** — Libro maestro de ingeniería con 5 hojas estructuradas: Especificaciones Eléctricas, Requisitos Funcionales, Requisitos No Funcionales, Restricciones de PCB y Lista de Materiales (BOM) consolidada de 66 componentes.

<br>

## 🧮 2. Calculadora de Balance Energético y Térmico (`Capture_the_buck_calculator/`)

Las ecuaciones de dimensionamiento de componentes pasivos (inductores, capacitores y resistencias) se encuentran documentadas directamente dentro de los esquemáticos de Altium Designer. 

Sin embargo, para garantizar la viabilidad del diseño, se utilizó una plantilla de cálculo en Excel que evalúa las condiciones de peor caso (*Worst Out Current case*) para cada convertidor. Los parámetros calculados en estas hojas incluyen:

* **Parámetros de Entrada (Input Data):**
  * `Vin`: Tensión de entrada proveniente del bus DC (ej. $24\text{ V}$).
  * `Vout`: Tensión de salida del canal correspondiente (ej. $15\text{ V}$, $12\text{ V}$, $5\text{ V}$).
  * `Worst Out Current case`: Corriente máxima demandada por la carga (ej. $3\text{ A}$, $5\text{ A}$).
  * `Effi`: Eficiencia nominal del convertidor extraída del datasheet a plena carga (ej. $0.915$ o $91.5\%$).
  * `Thermal θJA`: Resistencia térmica de unión a ambiente del encapsulado en $\text{°C/W}$.
  * `Temp Amb`: Temperatura ambiente de operación (ej. $27\text{ °C}$).

* **Resultados del Análisis (Output Data):**
  * `Wout` ($P_{out}$): Potencia real entregada a la carga ($V_{out} \times I_{out}$).
  * `Win` ($P_{in}$): Potencia total demandada al bus de $24\text{ VDC}$ ($W_{out} / \text{Effi}$).
  * `Wdiff` ($P_{loss}$): Potencia disipada en forma de calor por el circuito integrado ($W_{in} - W_{out}$).
  * `Power Dis` ($T_j$): Estimación de la temperatura de la unión térmica del chip.
  * `Iin`: Corriente media exigida al bus principal de entrada ($W_{in} / V_{in}$). Este valor es crítico para dimensionar el banco de capacitores principal y el inductor del filtro EMI.

<br>

## 📐 3. Diagramas de Arquitectura (`Diagrams/`)

1. **Diagrama de Bloques (`Block_diagram`):** Detalla la cadena funcional desde la entrada de $18\text{ VAC}_{\text{rms}}$, protecciones primarias (fusible + varistor), rectificación de onda completa, filtrado capacitivo y EMI, hasta la partición en los 5 canales DC/DC y el bloque de instrumentación visual (voltímetro digital en `J2`).
2. **Árbol de Potencias (`Power_tree`):** Ilustra el flujo jerárquico de energía, detallando corrientes por rama, eficiencias de conversión, pérdidas estimadas por calor y distribución de los $151.9\text{ W}$ de potencia máxima instalada.

<br>

## 📊 4. Libro Maestro de Diseño (`Power_supply_design.xlsx`)

Documenta la trazabilidad completa del hardware a través de 5 pestañas técnicas:
1. **`1. Especificaciones`:** Cuadro de carga de las 5 salidas, tensiones, corrientes máximas ($17\text{ A}$ acumulados), potencia total ($151.9\text{ W}$) y gráfico comparativo.
2. **`2. Req. Funcionales`:** Trazabilidad de los requerimientos vinculados con los esquemáticos.
3. **`3. Req. No Funcionales`:** Interfaz de usuario (Voltímetro, LED de bus, Potenciómetro).
4. **`4. Restricciones`:** Reglas de ruteo de pistas para $6.381\text{ A}$ en el bus DC y $17\text{ A}$ en planos `GND`.
5. **`5. Lista de Componentes`:** BOM consolidada de 66 ítems con designadores IPC.