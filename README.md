# ⚡ Fuente de Alimentación Multisalida de Laboratorio (151.9 W)

<div align="center">

![Hardware](https://img.shields.io/badge/Hardware-Open%20Source-orange?style=for-the-badge&logo=open-source-hardware)
![Altium Designer](https://img.shields.io/badge/Altium%20Designer-Hierarchical%20PCB-blue?style=for-the-badge&logo=altiumdesigner)
![Excel](https://img.shields.io/badge/Excel-Power%20%26%20BOM%20Calculator-217346?style=for-the-badge&logo=microsoftexcel)
![Draw.io](https://img.shields.io/badge/Draw.io-Power%20Tree%20%26%20Blocks-F08705?style=for-the-badge&logo=diagramsdotnet)
![Status](https://img.shields.io/badge/ERC%20Check-Passed%20(0%20Errors)-brightgreen?style=for-the-badge)

**Diseño electrónico jerárquico en Altium Designer, modelado energético y térmico en Excel y arquitectura completa de conversión AC/DC y DC/DC.**

[📚 Ir a Documentación](./Documentation/README.md) • [🛠️ Ir a Esquemáticos y PCB (Trunk)](./PCB/Trunk/README.md) • [⚡ Especificaciones](#-especificaciones-técnicas) • [📂 Estructura](#-estructura-del-repositorio)

</div>

---

## 📌 Descripción del Proyecto

Este repositorio contiene la ingeniería completa de una **Fuente de Alimentación Multisalida de 151.9 W y 5 Canales**, concebida para alimentar sistemas embebidos, lógica digital, etapas analógicas simétricas y pruebas de prototipado en laboratorio.

El proyecto se divide en dos pilares fundamentales:
1. **📊 Ingeniería y Documentación (`./Documentation`):** Memorias de cálculo basadas en los *datasheets* de cada convertidor (corriente de entrada, potencia de entrada y potencia disipada), árbol de potencia (*Power Tree*), diagrama de bloques en Draw.io, hojas de datos y especificación completa con BOM en Excel.
2. **🔌 Diseño Hardware en Altium Designer (`./PCB/Trunk`):** Captura esquemática con arquitectura estrictamente jerárquica validada sin errores eléctricos (ERC), filtro EMI tipo Pi en la etapa rectificadora y layout de PCB (`Main_PCB_V1R0_XXXXXX.PcbDoc`).

---

## ⚡ Especificaciones Técnicas

* **Entrada AC (Secundario Transformador):** 18 VAC rms (desde red de 110 VAC / 60 Hz) con protección por fusible (16 A / 500 V) y varistor MOV (29 V / 500 A).
* **Bus DC Principal (`24VDC`):** ~24.0 VDC rectificados (Puente 25 A / 50 V), banco de filtrado de 20,400 µF (3 × 6800 µF) y filtro Pi EMI (10 µH, Fc = 11.25 kHz).
* **Potencia Total Combinada:** **151.9 W** (Suma máxima de corriente en salidas: **17.0 A**).

| ID Canal | Riel de Salida | Tensión Nominal | Corriente Máx. | Potencia Máx. | Topología / Regulador (IC) | Frecuencia (fsw) |
| :---: | :---: | :---: | :---: | :---: | :--- | :---: |
| **OUT_01** | `+12V0` | +12.0 V | 5.0 A | 60.0 W | Buck Síncrono (`SY8205FCC` - U1) | 500 kHz |
| **OUT_02** | `+5V0` | +5.0 V | 5.0 A | 25.0 W | Buck Síncrono (`SY8205FCC` - U2) | 500 kHz |
| **OUT_03** | `+3V3` | +3.3 V | 3.0 A | 9.9 W | Buck Síncrono (`SY8205FCC` - U3) | 500 kHz |
| **OUT_04** | `-12V0` | -12.0 V | 1.0 A | 12.0 W | Buck-Boost Inversor (`TPS54360DDAR` - U5) | 500 kHz |
| **OUT_05** | `1V25-15V0` | 1.25 V – 15.0 V | 3.0 A | 45.0 W | Buck Ajustable (`LM2596SX-ADJ/NOPB` - U4) | 150 kHz |

---

## 📐 Arquitectura y Árbol de Potencia

```text
                  ┌──────────────────────────────────────────────┐
                  │  Entrada 18 VAC rms (J1) + Fusible F1 (16A)  │
                  │           + Varistor MOV V1 (29V)            │
                  └──────────────────────┬───────────────────────┘
                                         │
                  ┌──────────────────────▼───────────────────────┐
                  │   Rectificador BR1 (25A) + Banco 20,400 µF   │
                  │    + Filtro Pi EMI (L6 = 10 µH) + LED D3     │
                  └──────────────────────┬───────────────────────┘
                                         │  Bus Jerárquico 24VDC (Icarga ≈ 6.38 A)
      ┌─────────────────┬────────────────┼────────────────┬────────────────────┐
      ▼                 ▼                ▼                ▼                    ▼
┌───────────┐     ┌───────────┐    ┌───────────┐    ┌───────────┐     ┌─────────────────┐
│ +12V @ 5A │     │ +5V @ 5A  │    │ +3.3V @ 3A│    │ -12V @ 1A │     │ 1.25-15V @ 3A   │
│ SY8205FCC │     │ SY8205FCC │    │ SY8205FCC │    │ TPS54360  │     │ LM2596SX-ADJ    │
│  (60.0 W) │     │  (25.0 W) │    │  (9.9 W)  │    │  (12.0 W) │     │ + Voltímetro J2 │
└───────────┘     └───────────┘    └───────────┘    └───────────┘     └─────────────────┘