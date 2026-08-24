# Piecewise Signal Generation & Simscape RLC Circuit Transient Analysis

![MATLAB](https://img.shields.io/badge/MATLAB-R2020b%2B-blue.svg)
![Simulink](https://img.shields.io/badge/Simulink-Model-orange.svg)
![Simscape](https://img.shields.io/badge/Simscape-Physical%20Modeling-brightgreen.svg)
![License](https://img.shields.io/badge/License-MIT-lightgrey.svg)

## Overview

This project demonstrates the generation of a custom piecewise-defined voltage signal using **MATLAB/Simulink** and its application as an excitation source to a physical **RLC circuit modeled in Simscape**.

The project combines mathematical signal generation in the Simulink environment with multi-domain physical system modeling in Simscape to investigate the transient response of an RLC network to a continuous but non-smooth excitation waveform.

A key feature of the model is the use of **unit-step windowing** to construct the piecewise signal without explicit conditional or `if-else` logic.

---

## Mathematical Formulation

The synthesized input voltage waveform is defined over three time intervals:

$$v_{in}(t) = \begin{cases} t^2, & 0 \leq t < 3 \\[4pt] 9 + \sin\left(\dfrac{4\pi}{3}t\right), & 3 \leq t < 6 \\[4pt] (t-9)^2, & 6 \leq t \leq 9 \end{cases}$$

The waveform is continuous at the transition points:

$$v_{in}(3^-) = v_{in}(3^+) = 9$$

and

$$v_{in}(6^-) = v_{in}(6^+) = 9$$

However, the first derivative changes abruptly at these transition points, resulting in a continuous waveform with non-smooth slope characteristics.

---

## Piecewise Signal Construction

The complete waveform is synthesized using delayed unit-step functions:

$$v_{in}(t) = t^2\left[u(t)-u(t-3)\right] + \left[9+\sin\left(\frac{4\pi}{3}t\right)\right]\left[u(t-3)-u(t-6)\right] + (t-9)^2\left[u(t-6)-u(t-9)\right]$$

Each functional segment is activated only over its corresponding time interval.

### Interval 1 — $0 \leq t < 3$

$$v_1(t)= t^2\left[u(t)-u(t-3)\right]$$

A Ramp block is passed through a Math Function block configured for squaring to generate the parabolic waveform.

---

### Interval 2 — $3 \leq t < 6$

$$v_2(t)= \left[9+\sin\left(\frac{4\pi}{3}t\right)\right]\left[u(t-3)-u(t-6)\right]$$

A sinusoidal signal is combined with a DC offset of 9 V and windowed between $t=3$ s and $t=6$ s.

---

### Interval 3 — $6 \leq t \leq 9$

$$v_3(t)= (t-9)^2\left[u(t-6)-u(t-9)\right]$$

A shifted ramp is squared to generate the final parabolic segment.

The three windowed components are then summed to reconstruct the complete input waveform.

---

## System Architecture

The simulation consists of two main domains connected through Simulink–Simscape interface blocks.

### 1. Simulink Signal Generation Domain

The input waveform is constructed using:

- **Ramp blocks** for time-dependent signals
- **Math Function blocks** for squaring operations
- **Sine Wave / Signal Generator blocks** for sinusoidal excitation
- **Constant blocks** for DC offsets
- **Step blocks** for delayed unit-step functions
- **Product blocks** for time-windowing
- **Sum blocks** for combining the individual waveform segments

This approach generates the complete piecewise signal without explicit branching or conditional logic.

### 2. Simscape Physical Circuit Domain

The generated signal is applied to a physical RLC network using:

- **Simulink-PS Converter**
- **Controlled Voltage Source**
- **Resistors**
- **Capacitors**
- **Inductor**
- **Voltage Sensor**
- **PS-Simulink Converter**
- **Solver Configuration**
- **Scope**

The Simulink-generated waveform is converted into a physical-domain voltage signal and applied to the RLC network. The resulting circuit response is then measured and transferred back to Simulink for visualization.

---

## Transient Analysis

The excitation waveform is continuous in amplitude but has discontinuities in its first derivative at:

- $t=3$ s
- $t=6$ s

These non-smooth transitions introduce abrupt changes in the excitation slope and can produce pronounced transient behavior in the RLC network.

For the inductor:

$$v_L(t)=L\frac{di_L(t)}{dt}$$

Therefore, changes in the circuit state derivatives can appear as sharp variations in the inductor voltage response.

The simulation provides a practical illustration of how the characteristics of an input waveform influence the transient behavior of a physical energy-storage network.

> **Note:** The magnitude and shape of the transient response depend on the RLC topology, component values, initial conditions, and numerical solver settings.

---

## Results

The simulation allows the input excitation and the resulting circuit response to be observed using the Simulink Scope.

| Signal | Characteristics |
|---|---|
| **Input voltage $v_{in}(t)$** | Continuous piecewise waveform consisting of two parabolic segments and one sinusoidal segment |
| **Transition at $t=3$ s** | Continuous voltage value with an abrupt change in slope |
| **Transition at $t=6$ s** | Continuous voltage value with an abrupt change in slope |
| **RLC response** | Transient behavior around the piecewise transition points |

### Key Engineering Insights

- The piecewise signal is **continuous in value** at the transition points.
- Its first derivative is **not continuous**, creating non-smooth excitation.
- Energy-storage elements such as inductors and capacitors respond dynamically to changes in the excitation.
- The Simulink–Simscape interface provides a convenient way to connect mathematical signal processing with physical circuit simulation.
- Unit-step windowing provides a simple method for implementing piecewise mathematical functions without explicit conditional logic.

---

## Model Architecture

The overall simulation workflow can be summarized as:

```text
Piecewise Mathematical Signal
            │
            ▼
       Simulink Model
            │
            ▼
    Simulink-PS Converter
            │
            ▼
 Controlled Voltage Source
            │
            ▼
        RLC Network
            │
            ▼
      Voltage Sensor
            │
            ▼
    PS-Simulink Converter
            │
            ▼
          Scope
```

---

## Repository Structure

```
.
├── models/
│   └── piecewise_rlc_model.slx
│
├── results/
│   ├── model_architecture.png
│   ├── input_waveform.png
│   └── transient_response.png
│
├── README.md
├── .gitignore
└── LICENSE
```

---

## Prerequisites

The following software is required:

- MATLAB R2020b or newer
- Simulink
- Simscape

## Running the Model

**1. Clone the repository**
```bash
git clone https://github.com/<your-username>/<repository-name>.git
cd <repository-name>
```

**2. Open MATLAB**

Navigate to the repository directory.

**3. Open the Simulink model**
```matlab
open('models/piecewise_rlc_model.slx')
```

**4. Run the simulation**

Click **Run** in the Simulink toolbar.

Alternatively, the simulation can be executed from the MATLAB Command Window:
```matlab
sim('piecewise_rlc_model')
```

**5. Inspect the results**

Use the Scope block to observe the input waveform and measured circuit response.

---

## Possible Extensions

Several extensions can be explored using this model:

- Perform an RLC parameter sweep for $R$, $L$, and $C$.
- Investigate how damping affects the transient response.
- Compare different waveform transition rates.
- Replace abrupt transitions with smooth transition functions.
- Compare continuous but non-smooth signals with fully smooth excitation signals.
- Log $v_{in}(t)$ and $v_L(t)$ and generate publication-quality plots in MATLAB.
- Perform a numerical study of transient magnitude versus circuit parameters.

---

## Skills Demonstrated

- MATLAB
- Simulink
- Simscape
- Mathematical modeling
- Piecewise signal generation
- Unit-step windowing
- RLC circuit modeling
- Transient analysis
- Simulink–Simscape interfacing
- Physical system simulation


---

## 👨‍💻 Author

**Armin Ilat**  
Electrical Engineering Student

**Interests:**  
Electrical Engineering · Scientific Computing · Programming · Digital Communications · Control Systems · Engineering Simulation

### 🔗 Links

- **LinkedIn:** [linkedin.com/in/armin-ilat](https://www.linkedin.com/in/armin-ilat/)
- **GitHub:** [github.com/Armin-Il](https://github.com/Armin-Il)
- **YouTube:** [@VoltVerse-Electrical](https://www.youtube.com/@VoltVerse-Electrical)


## 📄 License

This project is licensed under the [MIT License](LICENSE).

