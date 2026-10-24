---

## 3. README de Hardware y PCB (`PCB/Trunk/README.md`)
📍 **Ruta donde debes guardarlo:** `Proyect_power_supply/PCB/Trunk/README.md`[cite: 20]

Este archivo documenta los esquemáticos jerárquicos y archivos de Altium Designer que aparecen en tu carpeta `PCB/Trunk`[cite: 13, 14, 15, 16, 18, 20]:

```markdown
# 🛠️ Diseño Esquemático Jerárquico y PCB (Altium Designer)

<div align="center">

![Altium Designer](https://img.shields.io/badge/Altium%20Designer-PrjPcb-005596?style=for-the-badge&logo=altiumdesigner)
![Architecture](https://img.shields.io/badge/Topología-Estrictamente%20Jerárquica-purple?style=for-the-badge)
![ERC](https://img.shields.io/badge/Compilación-0%20Errores-success?style=for-the-badge)

**Archivos fuente de captura esquemática modular, reglas eléctricas (ERC) verificadas, diseño de PCB y trabajo de salida (OutJob).**

[⬅️ Volver al README Principal](../../README.md) • [📚 Ir a Documentación](../../Documentation/README.md)

</div>

---

## 🗂️ Estructura de Archivos en `PCB/Trunk/`

```text
Trunk/
├── Proyect Power Supply/             # 📁 Carpeta contenedora del proyecto (.PrjPcb)
├── 000_Hierarchical.SchDoc           # 🌳 Hoja raíz con símbolos de hoja (Sheet Symbols) y bus 24VDC
├── 100_AC-DC.SchDoc                  # ⚡ Etapa de entrada 18 VAC, rectificación y filtro EMI (Salida 24VDC)
├── 200_DC-DC.SchDoc                  # 🔋 Reguladores Buck fijos: +12V0 @ 5A (U1) y +5V0 @ 5A (U2)
├── 201_DC-DC_Part2.SchDoc            # 🔋 Inversor -12V0 @ 1A (U5) y Regulador Buck +3V3 @ 3A (U3)
├── 202_DC-DC_Part3.SchDoc            # 🎛️ Regulador Buck ajustable 1.25V-15.0V @ 3A (U4) y Voltímetro (J2)
├── Main_PCB_V1R0_XXXXXX.PcbDoc       # 🟩 Documento de diseño de placa de circuito impreso (PCB Layout)
├── Power_Supply_MainBoard.OutJob     # 📦 Configuración de generación de archivos de manufactura
└── README.md                         # 📖 Este documento técnico