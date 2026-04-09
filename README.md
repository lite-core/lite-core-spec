# LiteCore Specification

**Coherent Silicon Photonic Complex Multiply-Accumulate (CSP-cMAC) Unit Cell**

---

## 📋 Overview

**LiteCore** is a fundamental photonic compute primitive purpose-built for large language model (LLM) inference at quadrillion-parameter scales. Developed by **Dust LLC**, LiteCore leverages 2026-mature silicon-on-insulator (SOI) photonics to perform complex-valued multiply-accumulate operations at **<1 fJ energy** and **1–10 ps latency**—representing 500–2,000× energy and 1,000–10,000× latency improvements over state-of-the-art electronic GPUs.

This repository contains the complete hardware engineering specification corpus for the LiteCore architecture, including mathematical formalization, component specifications, interface definitions, and manufacturing requirements.

---

## 🚀 Quick Start

| Document | Description | Link |
|----------|-------------|------|
| **White Paper** | High-level architecture and vision | [`LITECORE-WP-001.md`](./docs/LITECORE-WP-001.md) |
| **Math Formalization** | Mathematical foundations and proofs | [`LITECORE-MATH-001.md`](./docs/LITECORE-MATH-001.md) |
| **Hardware Spec** | Complete engineering specifications | [`LITECORE-HW-SPEC-001.md`](./docs/LITECORE-HW-SPEC-001.md) |
| **Rust API** | Software abstraction layer | [`litecore/`](./software/litecore/) |
| **PDK** | Process design kit for foundries | [`pdk/`](./pdk/) |

---

## 📊 Key Specifications

| Category | Parameter | Value | Unit |
|----------|-----------|-------|------|
| **Performance** | Peak TOPS | 670 | TOPS |
| | Energy per MAC | 0.1–1.0 | fJ |
| | MVM Latency | 1–10 | ps |
| **Optical** | Wavelength Range | 1,530–1,580 | nm |
| | WDM Channels | 64–256 | channels |
| | Channel Spacing | 50–100 | GHz |
| **Electrical** | Supply Voltage | 0.8, 1.0, 1.8 | V |
| | Total Power | 10–50 | W |
| | Interface | UCIe 2.0 / 224G | - |
| **Thermal** | Operating Temp | 20–80 | °C |
| | Max Junction Temp | 85 | °C |
| | Thermal Resistance | 0.67 | °C/W |
| **Mechanical** | Die Size | 26×33 | mm |
| | Package | FCBGA | - |
| | Fiber Count | 64–256 | fibers |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         LITECORE ACCELERATOR                            │
│                                                                          │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                      PHOTONIC DIE (SOI)                            │  │
│  │  ┌─────────────────────────────────────────────────────────────┐  │  │
│  │  │         LITECORE ARRAY (1,024×1,024 unit cells)              │  │  │
│  │  │  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐           │  │  │
│  │  │  │ LC  │ │ LC  │ │ LC  │ │ LC  │ │ LC  │ │ ... │           │  │  │
│  │  │  └─────┘ └─────┘ └─────┘ └─────┘ └─────┘ └─────┘           │  │  │
│  │  └─────────────────────────────────────────────────────────────┘  │  │
│  │                                                                    │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │  │
│  │  │ VLSP LASER   │  │ MZI SWITCH   │  │ Ge PHOTODIODE│            │  │
│  │  │ ARRAY (64)   │  │ MATRIX       │  │ ARRAY (1,024)│            │  │
│  │  └──────────────┘  └──────────────┘  └──────────────┘            │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│                              │                                          │
│                              ▼                                          │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                   ELECTRONIC CO-PROCESSOR (7nm)                    │  │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐     │  │
│  │  │   TIA   │ │   ADC   │ │  HSER   │ │  TPA    │ │  UCIe   │     │  │
│  │  │  Array  │ │  Array  │ │ Router  │ │ Manager │ │ Interface│     │  │
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘     │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 📁 Repository Structure

```
litecore-spec/
├── README.md                      # This file
├── LICENSE                        # Dust Open Hardware License
├── docs/
│   ├── LITECORE-WP-001.md         # White Paper
│   ├── LITECORE-MATH-001.md       # Mathematical Formalization
│   ├── LITECORE-HW-SPEC-001.md    # Hardware Engineering Spec
│   └── figures/                   # Diagrams and illustrations
├── software/
│   ├── litecore/                  # Rust crate
│   │   ├── Cargo.toml
│   │   ├── src/
│   │   │   ├── lib.rs
│   │   │   ├── device.rs
│   │   │   ├── tensor.rs
│   │   │   ├── tpa.rs
│   │   │   ├── hser.rs
│   │   │   └── error.rs
│   │   └── examples/
│   └── litecore-compiler/         # Photonic compiler
├── pdk/
│   ├── gf-45spclo+/               # GlobalFoundries PDK
│   ├── tsmc-n3e-ph/               # TSMC PDK
│   └── tower-ph18/                # Tower Semiconductor PDK
├── hardware/
│   ├── schematics/                # Circuit diagrams
│   ├── layouts/                   # GDSII layouts
│   └── boms/                      # Bill of materials
├── test/
│   ├── wafer-level/               # Wafer test procedures
│   ├── package-level/             # Package test procedures
│   └── system-level/              # System test procedures
└── compliance/
    ├── safety/                    # Safety certifications
    ├── emi-emc/                   # EMI/EMC compliance
    └── environmental/             # RoHS, REACH, etc.
```

---

## 🔧 Product Variants

| Variant | Array Size | WDM Channels | Target Application | Power |
|---------|------------|--------------|-------------------|-------|
| **LC-Edge** | 512×512 | 32 | Edge inference (1–7B models) | 2–10 W |
| **LC-Datacenter** | 1,024×1,024 | 64 | Datacenter (7–70B models) | 10–50 W |
| **LC-Enterprise** | 2,048×2,048 | 128 | Enterprise (70–405B models) | 30–100 W |
| **LC-Wafer** | 4,096×4,096 | 256 | Wafer-scale (405B–1Q models) | 100–500 W |

---

## 🎯 Key Features

### Native TPA/HSER Support
- **Optical Tier Tagging**: 4-bit PCM metadata per LiteCore unit
- **Wavelength-Selective Routing**: Distinct bands per tier (HBM→DRAM→NVMe→Object)
- **Zero-Power Sparsity**: True optical gating for inactive experts (99.999% sparse)

### Dual Weight Bank Architecture
- **MRR (Dynamic)**: Thermo-optic + electro-optic tuning (MHz rates)
- **PCM (Non-Volatile)**: Zero static power for cold tiers

### WDM Parallelism
- **64–256 Channels**: Per waveguide spectral multiplexing
- **50–100 GHz Spacing**: ITU grid compliant
- **No Area Overhead**: Multiple wavelengths share physical waveguide

### Rust-Native Software
- **Device Trait**: Clean polymorphic abstraction
- **Compiler Support**: `litecore-compiler` crate for graph optimization
- **Framework Integration**: PyTorch, JAX bindings

---

## 📐 Component Specifications

### Optical Components

| Component | Specification | Document |
|-----------|---------------|----------|
| VLSP Laser Array | 64–256 channels, 10–20 mW each | [`docs/components/laser.md`](./docs/components/laser.md) |
| EAM Modulator | >25 GHz, <3 dB loss, <10 fJ/bit | [`docs/components/modulator.md`](./docs/components/modulator.md) |
| MRR Weight Bank | Q=5,000–10,000, 2–5 nm tuning | [`docs/components/mrr.md`](./docs/components/mrr.md) |
| GSST PCM | 2–4 bits/cell, >10⁶ cycles | [`docs/components/pcm.md`](./docs/components/pcm.md) |
| Ge Photodiode | 0.8–1.0 A/W, >40 GHz | [`docs/components/photodiode.md`](./docs/components/photodiode.md) |
| MZI Switch | <0.5 dB loss, >20 dB extinction | [`docs/components/mzi.md`](./docs/components/mzi.md) |

### Electrical Interfaces

| Interface | Specification | Document |
|-----------|---------------|----------|
| UCIe 2.0 | 16–64 lanes, 32–64 GT/s | [`docs/interfaces/ucie.md`](./docs/interfaces/ucie.md) |
| 224G SerDes | 8–16 lanes, PAM4 | [`docs/interfaces/serdes.md`](./docs/interfaces/serdes.md) |
| I2C/SPI | 100–400 kHz / 50 MHz | [`docs/interfaces/management.md`](./docs/interfaces/management.md) |
| Power Rails | 0.8V, 1.0V, 1.8V, 3.3V | [`docs/interfaces/power.md`](./docs/interfaces/power.md) |

---

## 🏭 Manufacturing

### Approved Foundries

| Foundry | Process | Status | Qualification |
|---------|---------|--------|---------------|
| GlobalFoundries | 45SPCLO+ | Production | Q1 2026 |
| TSMC | N3E + Photonics | Risk | Q4 2026 |
| Tower Semiconductor | PH18 | Production | Q2 2026 |

### Yield Targets

| Stage | Target | Measurement |
|-------|--------|-------------|
| Wafer Fabrication | 80% | Wafer sort |
| Die Sort | 90% | Probe test |
| Assembly | 95% | Package test |
| Final Test | 98% | System test |
| **Overall** | **70%** | - |

---

## 🧪 Testing & Validation

### Test Levels

| Level | Description | Duration | Coverage |
|-------|-------------|----------|----------|
| Wafer-Level | Probe test, optical characterization | 1–2 hrs/wafer | 100% |
| Package-Level | Fiber coupling, electrical continuity | 10–30 min/device | 100% |
| System-Level | Performance, functional, thermal | 1–2 hrs/device | 100% |
| Burn-In | 85°C/85%RH, operational bias | 168 hours | Sample |

### Key Test Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| Energy per MAC | <1.0 fJ | Calorimetric + electrical |
| MVM Latency | <100 ps | Optical time-domain reflectometry |
| SNR | >50 dB | BER characterization |
| BER | <10⁻¹² | Error counting |
| Power (70B) | <50 W | Full-system measurement |

---

## 📦 Software Integration

### Rust Crate Usage

```rust
use litecore::device::PhotonicDevice;
use litecore::tensor::{PhotonicTensor, WeightBank};
use litecore::tpa::TierSet;
use litecore::hser::HSERDecision;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Initialize device
    let mut device = litecore::LiteCoreArray::new()?;
    
    // Configure optical mesh
    let wavelength_map = /* ... */;
    let switch_config = /* ... */;
    device.configure_optical_mesh(wavelength_map, switch_config)?;
    
    // Program weights
    let weights = WeightBank::from_file("model.bin")?;
    device.program_weights(&weights, BitPrecision::Eight, TierId::HOT)?;
    
    // Create input tensor
    let input = PhotonicTensor::from_vec(vec![/* activations */])?;
    
    // Define tier set and routing
    let tier_set = TierSet::active_tier(TierId::HOT);
    let routing = HSERDecision::select_top_k(&input, k=2)?;
    
    // Execute matmul
    let output = device.litecore_matmul(&input, &weights, &tier_set, routing)?;
    
    // Read result
    let result = output.to_vec();
    println!("Output: {:?}", result);
    
    Ok(())
}
```

### PyTorch Integration

```python
import torch
import litecore

class LiteCoreLinear(torch.nn.Module):
    def __init__(self, in_features, out_features, tier_id=0):
        super().__init__()
        self.weight = torch.nn.Parameter(torch.randn(out_features, in_features))
        self.tier_id = tier_id
        self.device = litecore.LiteCoreArray()
    
    def forward(self, x):
        routing = litecore.HSERDecision.select_top_k(x, k=2)
        tier_set = litecore.TierSet(active_tiers=[self.tier_id])
        return litecore.litecore_matmul(x, self.weight, tier_set, routing)
```

---

## 📈 Performance Comparison

| Metric | NVIDIA B200 | Lightmatter Envise | **LiteCore** | Advantage |
|--------|-------------|-------------------|--------------|-----------|
| Energy per MAC | 500–2,000 fJ | 1–10 fJ | **0.1–1 fJ** | 500–2,000× |
| MVM Latency | 10–100 ns | 10–100 ps | **1–10 ps** | 1,000–10,000× |
| Power (70B) | ~700 W | 50–100 W | **10–50 W** | 14–70× |
| Sparse Efficiency | Software | Software | **Native Optical** | ~100% |
| TPA/HSER Support | No | Limited | **Native** | - |
| Rust API | No | No | **Yes** | - |

---

## 🛣️ Roadmap

| Year | Milestone | Deliverables |
|------|-----------|--------------|
| **2026** | Inference & Fine-Tuning | 0.3B–70B models, Rust API v0.1, MLPerf submission |
| **2027** | Training Support | Optical gradients, hybrid training, 7B training demo |
| **2028** | Wafer-Scale | 4,096×4,096 arrays, ONoC, 405B inference |
| **2029–2030** | 16-Bit Precision | Coherent detection, error-tolerant designs |
| **2030+** | Quantum-Photonic | Hybrid quantum-classical architectures |

---

## 📄 License

This specification is released under the **Dust Open Hardware License**.

**Summary:**
- ✅ Free to use for research, education, and commercial purposes
- ✅ Free to modify and distribute derivatives
- ✅ Must attribute Dust LLC as original author
- ✅ Must share modifications under same license
- ✅ Patent grant included for implementers

See [`LICENSE`](./LICENSE) for full terms.

---

## 📞 Contact & Support

| Purpose | Contact |
|---------|---------|
| General Inquiries | `info@dust.llc` |
| Technical Support | `support@dust.llc` |
| Foundry Partnerships | `foundry@dust.llc` |
| Hyperscaler Co-Design | `enterprise@dust.llc` |
| Security Issues | `security@dust.llc` |

**Documentation:** [`https://docs.dust.llc/litecore`](https://docs.dust.llc/litecore)

**Issue Tracker:** [`https://github.com/dust-llc/litecore-spec/issues`](https://github.com/dust-llc/litecore-spec/issues)

---

## 🤝 Contributing

We welcome contributions from the community! Please see [`CONTRIBUTING.md`](./CONTRIBUTING.md) for guidelines.

### Contribution Areas
- 📐 PDK improvements and process optimizations
- 🔧 Software tooling and compiler enhancements
- 🧪 Test procedures and validation methodologies
- 📚 Documentation and educational materials
- 🔬 Research collaborations and publications

### Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](./CODE_OF_CONDUCT.md). Please read it before contributing.

---

## 📚 References

1. Shen, Y., et al. "Deep learning with coherent nanophotonic circuits." *Nature Photonics* 11.7 (2017): 441–446.
2. Harris, N. C., et al. "Machine learning on a programmable photonic processor." *Optica* 5.4 (2018): 446–453.
3. Feldmann, J., et al. "Parallel convolutional processing using an integrated photonic tensor core." *Nature* 589.7840 (2021): 52–58.
4. GlobalFoundries. "45SPCLO+ Silicon Photonics Platform." PDK Documentation, 2026.
5. TSMC. "N3E + Photonics Technology." Process Design Kit, 2026.

---

## ⚠️ Disclaimer

**This specification is provided "as is" without warranty of any kind.** Implementation requires expertise in silicon photonics, semiconductor manufacturing, and optical systems engineering. Dust LLC assumes no liability for damages arising from use of this specification.

Certain features described herein may be subject to export control regulations. Consult legal counsel before international distribution.

---

## 📊 Document Control

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-01-15 | Dust LLC Hardware Team | Initial draft |
| 0.5 | 2026-02-01 | Dust LLC Hardware Team | Review complete |
| **1.0** | **2026-02-28** | **Dust LLC Hardware Team** | **Final release** |

---

**© 2026 Dust LLC. All rights reserved.**

**LiteCore™ is a trademark of Dust LLC.**

**Document Number:** LC-README-001  
**Classification:** Public  
**Distribution:** Unlimited

---

<div align="center">

**🌟 The atomic bit of the photonic era. Purpose-built for quadrillion-scale AI.**

[Documentation](https://docs.dust.llc/litecore) • [GitHub](https://github.com/dust-llc/lite-core-spec) • [Website](https://dust.llc)

</div>