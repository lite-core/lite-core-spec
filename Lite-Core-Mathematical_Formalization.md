# **Appendix G: LiteCore Mathematical Formalization**

## **Coherent Silicon Photonic Complex Multiply-Accumulate Unit Cell: Formal Specification**

**Version 1.0**  
**Date:** February 28, 2026  
**Authors:** Marlon et al.  
**Institution:** Dust LLC  
**Classification:** Public  
**License:** Dust Open Hardware License  

---

## **1. Notation and Definitions**

### **1.1 Sets and Fields**
| Symbol | Definition |
|--------|------------|
| $\mathbb{R}$ | Field of real numbers |
| $\mathbb{C}$ | Field of complex numbers |
| $\mathbb{R}^+$ | Positive real numbers (power, intensity) |
| $\mathbb{N}$ | Natural numbers (indices, counts) |
| $\mathcal{W}$ | Set of valid optical wavelengths $\{\lambda_1, \dots, \lambda_M\}$ |
| $\mathcal{T}$ | Set of TPA tiers $\{t_0, t_1, t_2, t_3\}$ |
| $\mathcal{E}$ | Set of HSER experts $\{e_1, \dots, e_N\}$ |

### **1.2 Optical Variables**
| Symbol | Definition | Unit |
|--------|------------|------|
| $E(z,t)$ | Electric field envelope | $\sqrt{\text{W}}$ |
| $\omega$ | Angular frequency ($2\pi c / \lambda$) | rad/s |
| $\phi$ | Optical phase | rad |
| $P$ | Optical power ($|E|^2$) | W |
| $\lambda$ | Wavelength | nm |
| $n_{\text{eff}}$ | Effective refractive index | dimensionless |

### **1.3 Compute Variables**
| Symbol | Definition | Unit |
|--------|------------|------|
| $\mathbf{x}$ | Input activation vector | dimensionless |
| $\mathbf{W}$ | Weight matrix | dimensionless |
| $\mathbf{y}$ | Output accumulation vector | dimensionless |
| $b$ | Effective bit precision | bits |
| $\epsilon$ | Error tolerance | dimensionless |

---

## **2. Optical Field Propagation**

### **2.1 Waveguide Mode Equation**
Light propagation in the LiteCore silicon waveguide is governed by the Helmholtz equation. For a single-mode SOI waveguide at $\lambda = 1550$ nm:

$$\nabla^2 E(\mathbf{r}) + k_0^2 n^2(\mathbf{r}) E(\mathbf{r}) = 0$$

Where $k_0 = 2\pi/\lambda$. Under the slowly varying envelope approximation (SVEA), the field evolves along the propagation axis $z$ as:

$$E(z) = E(0) e^{i(\omega t - \beta z)} e^{-\alpha z/2}$$

Where:
- $\beta = k_0 n_{\text{eff}}$ is the propagation constant.
- $\alpha$ is the power attenuation coefficient (typical: $\alpha \approx 0.1$ dB/cm $\approx 2.3 \times 10^{-4}$ cm⁻¹).

**Invariant 2.1 (Loss Bound):**  
For any path length $L \leq 1$ cm in the LiteCore mesh:
$$\frac{P_{\text{out}}}{P_{\text{in}}} \geq 10^{-\alpha L / 10} \geq 0.97$$

### **2.2 Coupled Mode Theory (CMT)**
Interaction between waveguides (directional couplers, MRRs) is modeled by CMT. For two coupled waveguides with amplitudes $a_1, a_2$:

$$\frac{d}{dz} \begin{pmatrix} a_1 \\ a_2 \end{pmatrix} = i \begin{pmatrix} \beta_1 & \kappa \\ \kappa & \beta_2 \end{pmatrix} \begin{pmatrix} a_1 \\ a_2 \end{pmatrix}$$

Where $\kappa$ is the coupling coefficient. For a directional coupler of length $L_c$:

$$T_{\text{cross}} = \sin^2(\kappa L_c), \quad T_{\text{bar}} = \cos^2(\kappa L_c)$$

---

## **3. Component Transfer Functions**

### **3.1 Electro-Absorption Modulator (EAM)**
The EAM encodes input activation $x \in [0, 1]$ into optical amplitude. The transmission coefficient $T_{\text{EAM}}$ is a function of applied voltage $V$:

$$T_{\text{EAM}}(V) = \exp\left(-\Gamma \alpha_{\text{mat}}(V) L_{\text{EAM}}\right)$$

Where $\alpha_{\text{mat}}(V)$ is the voltage-dependent absorption coefficient (Franz-Keldysh effect).

**Linearization:**  
For small-signal operation around bias $V_0$:
$$P_{\text{out}} \approx P_{\text{in}} \cdot (k_V \cdot (V - V_0) + C)$$
Where $k_V$ is the modulation efficiency (V⁻¹).

**Invariant 3.1 (Modulation Linearity):**  
$$\left| \frac{\partial^2 T_{\text{EAM}}}{\partial V^2} \right| < \epsilon_{\text{lin}} \quad \forall V \in [V_{\min}, V_{\max}]$$
Typical $\epsilon_{\text{lin}} \leq 0.01$ for 8-bit precision.

### **3.2 Microring Resonator (MRR) Weight Bank**
The MRR performs weighting via resonance tuning. The transmission function $T_{\text{MRR}}(\lambda, \lambda_{\text{res}})$ is Lorentzian:

$$T_{\text{MRR}}(\lambda) = \frac{(\Delta\lambda/2)^2}{(\lambda - \lambda_{\text{res}})^2 + (\Delta\lambda/2)^2}$$

Where $\Delta\lambda = \lambda_{\text{res}} / Q$ is the linewidth.

**Thermo-Optic Tuning:**  
Resonance shift $\Delta \lambda_{\text{res}}$ via heater power $P_h$:
$$\Delta \lambda_{\text{res}} = \lambda_{\text{res}} \cdot \frac{dn_{\text{eff}}}{dT} \cdot \Delta T = \lambda_{\text{res}} \cdot \frac{dn_{\text{eff}}}{dT} \cdot (R_{\text{th}} \cdot P_h)$$

Where $R_{\text{th}}$ is thermal resistance (K/W). For Silicon: $\frac{dn_{\text{eff}}}{dT} \approx 1.8 \times 10^{-4}$ K⁻¹.

**Invariant 3.2 (Weight Precision):**  
For $b$-bit precision, the tuning step $\delta P_h$ must satisfy:
$$\frac{\partial T}{\partial P_h} \cdot \delta P_h \leq 2^{-b}$$

### **3.3 Phase-Change Material (PCM) Non-Volatile Weight**
For cold tiers, PCM refractive index $n_{\text{PCM}}$ depends on crystallinity fraction $c \in [0, 1]$:

$$n_{\text{eff}}(c) = (1-c)n_{\text{amorphous}} + c \cdot n_{\text{crystalline}}$$

Programming energy $E_{\text{prog}}$ for state change:
$$E_{\text{prog}} = \int_0^{t_{\text{pulse}}} V(t) I(t) dt$$

**Invariant 3.3 (PCM Retention):**  
For tier $t \in \{\text{cold}, \text{frozen}\}$, static power $P_{\text{static}} = 0$.

---

## **4. The Core Operation: Complex Multiply-Accumulate (cMAC)**

### **4.1 Problem Statement**
Compute $\mathbf{y} = \mathbf{W}\mathbf{x}$ where $\mathbf{W} \in \mathbb{C}^{N \times M}$, $\mathbf{x} \in \mathbb{C}^M$, $\mathbf{y} \in \mathbb{C}^N$.

### **4.2 Optical Encoding**
Input $x_j$ is encoded into optical field $E_{\text{in}, j}$ at wavelength $\lambda_k$:
$$E_{\text{in}, j} = \sqrt{P_0} \cdot x_j \cdot e^{i\phi_j}$$

Weight $w_{ij}$ is encoded into MRR transmission $T_{ij}$:
$$T_{ij} = |w_{ij}| \cdot e^{i\theta_{ij}}$$

### **4.3 Interference and Detection**
Fields are summed coherently on the output waveguide:
$$E_{\text{out}, i} = \sum_{j=1}^M T_{ij} E_{\text{in}, j} = \sqrt{P_0} \sum_{j=1}^M w_{ij} x_j$$

Photodetection measures intensity $I_i = |E_{\text{out}, i}|^2$:
$$I_i = P_0 \left| \sum_{j=1}^M w_{ij} x_j \right|^2$$

**Coherent Detection (Homodyne):**  
To recover the complex value (not just magnitude), we mix with a Local Oscillator (LO) $E_{\text{LO}} = \sqrt{P_{\text{LO}}} e^{i\phi_{\text{LO}}}$:

$$I_{\text{mix}} = |E_{\text{out}} + E_{\text{LO}}|^2 = |E_{\text{out}}|^2 + |E_{\text{LO}}|^2 + 2\text{Re}\{E_{\text{out}} E_{\text{LO}}^*\}$$

The interference term isolates the dot product:
$$\text{Re}\{y_i\} \propto I_{\text{mix}} - |E_{\text{out}}|^2 - |E_{\text{LO}}|^2$$

**Theorem 4.1 (Linearity of Optical MAC):**  
Given coherent summation and balanced detection, the output current $i_{\text{out}}$ is linearly proportional to the complex dot product:
$$i_{\text{out}} = \mathcal{R} \cdot 2\sqrt{P_0 P_{\text{LO}}} \cdot \text{Re}\{\mathbf{w}_i \cdot \mathbf{x}\} + \eta$$
Where $\mathcal{R}$ is photodiode responsivity and $\eta$ is noise.

---

## **5. WDM Parallelism Model**

### **5.1 Channel Orthogonality**
For $M$ wavelength channels $\{\lambda_1, \dots, \lambda_M\}$, orthogonality is defined by:
$$\int_{-\infty}^{\infty} E_{\lambda_i}(t) E_{\lambda_j}^*(t) dt = \delta_{ij} \cdot P_i$$

**Channel Spacing Constraint:**  
To prevent inter-channel crosstalk (XT), spacing $\Delta \lambda$ must satisfy:
$$\Delta \lambda \geq \Delta \lambda_{\text{symbol}} + \Delta \lambda_{\text{guard}}$$
Where $\Delta \lambda_{\text{symbol}} \approx \lambda^2 \cdot R_{\text{symbol}} / c$.

### **5.2 Capacity Equation**
Total throughput $\mathcal{C}$ for a LiteCore array of size $N \times N$ with $M$ WDM channels:
$$\mathcal{C} = N \cdot M \cdot f_{\text{clock}} \quad [\text{MACs/s}]$$

**Invariant 5.1 (WDM Capacity):**  
For $N=1024, M=64, f_{\text{clock}}=10 \text{ GHz}$:
$$\mathcal{C} \geq 6.5 \times 10^{14} \text{ MACs/s}$$

---

## **6. Noise and Error Model**

### **6.1 Noise Sources**
Total noise variance $\sigma_{\text{total}}^2$ is the sum of independent noise sources:

1.  **Shot Noise:** $\sigma_{\text{shot}}^2 = 2q(I_{\text{sig}} + I_{\text{LO}})B$
2.  **Thermal Noise:** $\sigma_{\text{thermal}}^2 = \frac{4k_B T}{R_L} B$
3.  **Relative Intensity Noise (RIN):** $\sigma_{\text{RIN}}^2 = \text{RIN} \cdot I_{\text{sig}}^2 B$
4.  **Quantization Noise:** $\sigma_{\text{ADC}}^2 = \frac{\Delta_{\text{ADC}}^2}{12}$

### **6.2 Signal-to-Noise Ratio (SNR)**
$$\text{SNR} = \frac{P_{\text{signal}}}{\sigma_{\text{total}}^2}$$

**Effective Number of Bits (ENOB):**
$$b_{\text{eff}} = \frac{\text{SNR}_{\text{dB}} - 1.76}{6.02}$$

**Invariant 6.1 (Precision Floor):**  
For LiteCore Tier 0 (Hot):
$$b_{\text{eff}} \geq 8 \text{ bits} \implies \text{SNR} \geq 49.9 \text{ dB}$$

### **6.3 Bit Error Rate (BER)**
For OOK modulation approximating weight bits:
$$\text{BER} = \frac{1}{2} \text{erfc}\left( \sqrt{\frac{\text{SNR}}{2}} \right)$$

**Target:** $\text{BER} < 10^{-12}$ (correctable by ECC).

---

## **7. Energy and Latency Model**

### **7.1 Energy per MAC**
$$E_{\text{MAC}} = \frac{P_{\text{laser}} + P_{\text{mod}} + P_{\text{tune}} + P_{\text{det}}}{\mathcal{C}}$$

**Breakdown:**
1.  **Laser:** $E_{\text{photon}} = \frac{h c}{\lambda \cdot \eta_{\text{WPE}}}$. For $\eta_{\text{WPE}}=0.2$, $E_{\text{photon}} \approx 0.5 \text{ fJ}$.
2.  **Modulation:** $E_{\text{mod}} = \frac{1}{2} C_{\text{EAM}} V_{\text{drive}}^2$. For $C=10 \text{ fF}, V=1 \text{ V}$, $E=5 \text{ fJ}$ (amortized over $N$ parallel lanes).
3.  **Detection:** $E_{\text{det}} = P_{\text{TIA}} / f_{\text{clock}}$.

**Target:** $E_{\text{MAC}} \leq 1 \text{ fJ}$.

### **7.2 Latency**
$$T_{\text{latency}} = T_{\text{prop}} + T_{\text{mod}} + T_{\text{det}} + T_{\text{ADC}}$$

**Propagation:** $T_{\text{prop}} = \frac{L_{\text{chip}} \cdot n_g}{c} \approx 10 \text{ ps}$ for $L=1 \text{ cm}$.
**Total Target:** $T_{\text{latency}} \leq 100 \text{ ps}$ per pipeline stage.

---

## **8. TPA/HSER Routing Logic**

### **8.1 Tiered Parameter Architecture (TPA)**
Define a tier mapping function $\tau: \text{Weights} \to \mathcal{T}$.
Wavelength band allocation $\mathcal{B}_t \subset \mathcal{W}$ for tier $t$.

**Routing Matrix $\mathbf{R}_{\text{TPA}}$:**
$$\mathbf{R}_{\text{TPA}} = \text{diag}(r_1, \dots, r_M)$$
Where $r_k = 1$ if $\lambda_k \in \mathcal{B}_{\text{active}}$, else $0$.

**Invariant 8.1 (Tier Isolation):**
$$\mathcal{B}_{t_i} \cap \mathcal{B}_{t_j} = \emptyset \quad \forall i \neq j$$

### **8.2 Hierarchical Sparse Expert Routing (HSER)**
Define sparse selection vector $\mathbf{s} \in \{0, 1\}^N$ where $\|\mathbf{s}\|_0 = k$ (top-$k$ experts).

**Optical Gating Matrix $\mathbf{G}_{\text{HSER}}$:**
$$\mathbf{G}_{\text{HSER}} = \text{MZI}(\mathbf{s})$$
Where $\text{MZI}(\cdot)$ configures the switch mesh to pass light only for indices where $s_i=1$.

**Power Model:**
$$P_{\text{HSER}} = P_{\text{base}} + k \cdot P_{\text{expert}}$$
Where $P_{\text{base}}$ is control logic power (constant) and $P_{\text{expert}}$ is optical power per active expert.

**Invariant 8.2 (Zero-Power Sparsity):**
For inactive experts ($s_i=0$):
$$P_{\text{optical}, i} = 0 \quad \text{and} \quad P_{\text{static}, i} = 0$$

---

## **9. Quantization and Precision Mapping**

### **9.1 Analog-to-Digital Mapping**
Optical power $P$ is mapped to digital integer $D \in [0, 2^b - 1]$:
$$D = \left\lfloor \frac{P - P_{\min}}{P_{\max} - P_{\min}} \cdot (2^b - 1) \right\rceil$$

### **9.2 Quantization Error Bound**
$$\epsilon_q = |P - \hat{P}| \leq \frac{P_{\max} - P_{\min}}{2^{b+1}}$$

**Invariant 9.1 (Accumulation Error):**
For matrix dimension $M$, total accumulation error $\epsilon_{\text{total}}$:
$$\epsilon_{\text{total}} \leq \sqrt{M} \cdot \epsilon_q$$
(Must remain within model tolerance, typically $\epsilon_{\text{total}} < 10^{-3}$).

---

## **10. Verification Properties for Rust Implementation**

The following invariants must be enforced in the `litecore` Rust crate via property-based testing (e.g., `proptest`).

### **10.1 Energy Invariant**
```rust
/// Property: Energy per MAC must not exceed 1 fJ under nominal load
#[test]
fn prop_energy_bound() {
    let config = LiteCoreConfig::default();
    let energy = config.energy_per_mac();
    assert!(energy <= 1.0 * FEMTOJOULE);
}
```

### **10.2 Sparsity Invariant**
```rust
/// Property: Inactive experts consume zero optical power
#[test]
fn prop_sparse_zero_power() {
    let routing = HSERDecision::sparse(experts=[0, 1], total=1024);
    let power = device.measure_power(routing);
    assert!(power <= BASE_CONTROL_POWER + THRESHOLD);
}
```

### **10.3 Tier Isolation Invariant**
```rust
/// Property: Wavelength bands for distinct tiers do not overlap
#[test]
fn prop_tier_isolation() {
    let bands = TierSet::default().wavelength_bands();
    for i in 0..bands.len() {
        for j in i+1..bands.len() {
            assert!(bands[i].intersection(&bands[j]).is_empty());
        }
    }
}
```

### **10.4 Linearity Invariant**
```rust
/// Property: Optical MAC output is linear within precision bounds
#[test]
fn prop_mac_linearity() {
    let x1 = Tensor::random();
    let x2 = Tensor::random();
    let y1 = device.litecore_matmul(&x1, &weights);
    let y2 = device.litecore_matmul(&x2, &weights);
    let y_sum = device.litecore_matmul(&(x1 + x2), &weights);
    
    // Allow for noise tolerance epsilon
    assert!((y_sum - (y1 + y2)).norm() < EPSILON);
}
```

---

## **11. Summary of Formal Bounds**

| Parameter | Symbol | Bound | Unit |
|-----------|--------|-------|------|
| **Energy per MAC** | $E_{\text{MAC}}$ | $\leq 1.0$ | fJ |
| **Latency per MVM** | $T_{\text{MVM}}$ | $\leq 100$ | ps |
| **Effective Precision** | $b_{\text{eff}}$ | $\geq 8$ | bits |
| **SNR** | $\text{SNR}$ | $\geq 50$ | dB |
| **BER** | $\text{BER}$ | $\leq 10^{-12}$ | - |
| **Sparsity Efficiency** | $\eta_{\text{sparse}}$ | $\geq 99.9$ | % |
| **WDM Channels** | $M$ | $\geq 64$ | - |
| **Array Size** | $N$ | $\geq 1024$ | - |

---

**© 2026 Dust LLC. All rights reserved.**  
**Released under Dust Open Hardware License.**