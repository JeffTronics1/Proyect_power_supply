# ⚡ Fuente de Alimentación Multisalida de Laboratorio (151.9 W)

<div align="center">

![Hardware](https://img.shields.io/badge/Hardware-Open%20Source-orange?style=for-the-badge&logo=open-source-hardware)
![Altium Designer](https://img.shields.io/badge/Altium%20Designer-Hierarchical%20PCB-blue?style=for-the-badge&logo=altiumdesigner)
![Excel](https://img.shields.io/badge/Excel-Power%20%26%20BOM%20Calculator-217346?style=for-the-badge&logo=microsoftexcel)
![Draw.io](https://img.shields.io/badge/Draw.io-Power%20Tree-F08705?style=for-the-badge&logo=diagramsdotnet)
![Status](https://img.shields.io/badge/ERC%20Check-Passed%20(0%20Errors)-brightgreen?style=for-the-badge)

**Ingeniería electrónica integral: Arquitectura estrictamente jerárquica en Altium Designer, modelado energético en Excel y diseño de circuito impreso (PCB) para equipos de laboratorio.**

[📚 Ir a Documentación de Ingeniería](./Documentation/README.md) • [🛠️ Ir a Esquemáticos y PCB (Trunk)](./PCB/Trunk/README.md) 

</div>

---

## 🚀 Resumen del Proyecto

Este repositorio documenta el diseño completo de una **Fuente de Alimentación de 151.9 W y 5 Canales**, desarrollada específicamente para soportar cargas críticas en entornos de laboratorio, sistemas embebidos, lógica digital y amplificadores operacionales simétricos.

> 💡 **Características Destacadas:**
> * **Diseño Jerárquico (*Top-Down*):** Estructura modular en Altium que facilita el aislamiento de etapas y la depuración sin errores eléctricos (ERC).
> * **Alta Eficiencia:** Uso de reguladores Buck síncronos (`SY8205FCC`) y topologías Buck-Boost de grado industrial.
> * **Protección Integral:** Aislamiento primario (fusible y varistor MOV), filtrado capacitivo de $20,400\text{ }\mu\text{F}$ y red EMI tipo Pi.

---

## 📐 Arquitectura Visual del Sistema

En lugar de simples bloques de texto, el diseño del flujo de energía y la interconexión de etapas se ha modelado gráficamente. *(Puedes ver los archivos originales editables en la carpeta de [Diagramas](./Documentation/Diagrams/))*

<div align="center">

### 🔹 Diagrama de Bloques (Topología General)
*Muestra el recorrido de la energía desde la entrada AC hasta las bornas de salida de cada canal.*

![Diagrama de Bloques](./Documentation/Diagrams/Block_diagram.png)

<br>

### 🔹 Árbol de Potencias (Distribución Energética)
*Mapea la distribución de los **151.9 W** totales, indicando corrientes, caídas de tensión y eficiencia por etapa.*

![Árbol de Potencias](./Documentation/Diagrams/Power_tree.png)

</div>

---

## ⚡ Especificaciones Técnicas por Canal

El sistema parte de un secundario de transformador de **18 VAC rms**, rectificado a un **Bus DC Principal de ~24 VDC**, del cual se derivan los siguientes rieles de potencia:

| ID Canal | Riel de Salida | Tensión Nominal | Corriente Máx. | Potencia Máx. | Regulador Principal | Frecuencia |
| :---: | :---: | :---: | :---: | :---: | :--- | :---: |
| **OUT_01** | 🔴 `+12V0` | $+12.0\text{ V}$ | $5.0\text{ A}$ | $60.0\text{ W}$ | Buck Síncrono (`SY8205FCC`) | $500\text{ kHz}$ |
| **OUT_02** | 🟠 `+5V0` | $+5.0\text{ V}$ | $5.0\text{ A}$ | $25.0\text{ W}$ | Buck Síncrono (`SY8205FCC`) | $500\text{ kHz}$ |
| **OUT_03** | 🟡 `+3V3` | $+3.3\text{ V}$ | $3.0\text{ A}$ | $9.9\text{ W}$ | Buck Síncrono (`SY8205FCC`) | $500\text{ kHz}$ |
| **OUT_04** | 🔵 `-12V0` | $-12.0\text{ V}$ | $1.0\text{ A}$ | $12.0\text{ W}$ | Inversor (`TPS54360DDAR`) | $500\text{ kHz}$ |
| **OUT_05** | 🟢 `Variable`| $1.25\text{ V} – 15.0\text{ V}$| $3.0\text{ A}$ | $45.0\text{ W}$ | Buck Ajustable (`LM2596SX-ADJ`) | $150\text{ kHz}$ |

---

## 🗂️ Inventario y Estructura del Repositorio

Para facilitar la revisión del proyecto, los archivos están organizados en dos ramas principales: **Documentación Teórica** y **Desarrollo de Hardware (Altium)**.

| Ícono | Ruta del Archivo / Directorio | Descripción y Función en el Proyecto |
| :---: | :--- | :--- |
| 📚 | **[`Documentation/`](./Documentation/README.md)** | **Carpeta raíz de ingeniería, matemáticas y hojas de datos.** |
| 📊 | ↳ `Power_supply_design.xlsx` | Libro maestro con especificaciones, restricciones de ruteo y lista BOM (66 ítems). |
| 🧮 | ↳ `Capture_the_buck_calculator/`| Análisis de disipación térmica y requerimientos de corriente por convertidor. |
| 📐 | ↳ `Diagrams/` | Diagrama de bloques y Árbol de potencias (Archivos `.drawio` y `.png`). |
| | | |
| 🔌 | **[`PCB/Trunk/`](./PCB/Trunk/README.md)** | **Entorno de desarrollo de hardware en Altium Designer.** |
| 🧠 | ↳ **`Proyect Power Supply.PrjPcb`**| **Archivo Maestro:** Enlaza todos los esquemáticos y la placa PCB en un solo proyecto. |
| 📄 | ↳ `000_Hierarchical.SchDoc` | Hoja principal (*Top Sheet*) que conecta las sub-hojas mediante el bus de 24V. |
| ⚡ | ↳ `100_AC-DC.SchDoc` | Esquemático: Entrada 18VAC, protecciones, rectificación y Filtro EMI. |
| 🔋 | ↳ `200... / 201... / 202...` | Esquemáticos: Convertidores Buck, Inversores y salida ajustable con instrumentación. |
| 🟩 | ↳ `Main_PCB_V1R0_XXXXXX.PcbDoc` | Archivo de ruteo de la placa de circuito impreso (*PCB Layout*). |

---

## 👨‍💻 Autor

* **Jefferson Andrés Oviedo Ríos** — *Estudiante de Ingeniería Mecatrónica* (`@JeffTronics1`)