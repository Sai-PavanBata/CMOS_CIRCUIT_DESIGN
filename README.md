# CMOS_CIRCUIT_DESIGN1

---

## VirtualBox Setup

Follow the steps below to load the provided **CMOS VDI file** in **Oracle VirtualBox**.

---

### Step 1: Install 1

* Download **Oracle VirtualBox** from:
  [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)
* Complete the installation using the default options.

---

### Step 2: Create a New Virtual Machine

1. Launch **VirtualBox**
2. Click **New**
3. Enter the following details:

| Option  | Selection                             |
| ------- | ------------------------------------- |
| Type    | Linux                                 |
| Version | Ubuntu 18.04 (Bionic Beaver) – 64-bit |

4. Click **Next**

---

### Step 3: Memory Allocation

* Set the RAM size as required
* **Recommended:** 4096 MB
* Click **Next**

---

### Step 4: Load the CMOS VDI File

1. Choose **Use an existing virtual hard disk file**
2. Click the **folder icon** to browse
3. Locate and select the extracted **CMOS VDI file**
4. Click **Open**
5. Click **Next**, then **Finish**

---
<img width="1920" height="1080" alt="Screenshot 2026-02-17 123709" src="https://github.com/user-attachments/assets/a713df37-cd18-4f2c-b01d-4493c9216ec7" />

---

### Step 5: Launch the Virtual Machine

1. Highlight the newly created virtual machine
2. Click **Start** to boot the system

---


---

# Ngspice SKY130 – Day 1

# Basics of NMOS Drain Current (Id) vs Drain-to-Source Voltage (Vds)

---

# Chapter 1: Introduction to Circuit Design and SPICE Simulations

---

## Lecture 1: Why Do We Need SPICE Simulations?

* Digital circuits are constructed using **PMOS and NMOS transistors** connected together to form logic gates such as:

  * NAND
  * NOR
  * AND
  * OR
  * Inverter

<img width="518" height="735" alt="Screenshot 2026-02-17 120936" src="https://github.com/user-attachments/assets/b2ebbad5-9b67-426c-a7d2-e2e91363bcd0" />

The above inverter will have the following characteristics. We will run SPICE simulations to determine the delay and thus obtain the W/L ratio of the particular transistor.


* Each logic gate exhibits **electrical characteristics** such as:

  * Propagation delay
  * Rise time
  * Fall time

* These characteristics **cannot be accurately estimated analytically** and therefore require **SPICE simulations**.

<img width="1202" height="1079" alt="Screenshot 2026-02-17 121009" src="https://github.com/user-attachments/assets/c6776308-5015-43f9-8547-95c9d28116ec" />


* SPICE simulations help in:

  * Calculating **delay**
  * Determining appropriate **W/L ratios** of transistors
  * Validating circuit performance before fabrication

---

### Role of SPICE in VLSI Physical Design

* **SPICE (Simulation Program with Integrated Circuit Emphasis)** is the backbone of:

  * Timing analysis
  * Clock Tree Synthesis (CTS)
  * Crosstalk and signal integrity analysis

* Without SPICE-based delay estimation:

  * Timing closure is not possible
  * CTS optimisation becomes meaningless
  * Crosstalk analysis has no practical reference

---

## Example: Delay Extraction Using SPICE

* Consider a circuit where **Clock Tree Synthesis** has been performed.
* Buffers are inserted to drive **different capacitive loads** at the output.

<img width="1668" height="570" alt="Screenshot 2026-02-17 121601" src="https://github.com/user-attachments/assets/04244c28-cfaa-4f64-a9c3-142a43f1ea5c" />


* After running SPICE simulations:

  * A **Delay Table** is generated
  * The table consists of:

    * Input slew values
    * Output load capacitance values

* The **delay** is obtained from the **intersection of input slew and output load**.

<img width="1318" height="347" alt="image" src="https://github.com/user-attachments/assets/ff6faab5-f966-4cc0-a2e7-9e95afd48309" />

* Separate delay tables are generated for:

  * Level 1 buffers
  * Level 2 buffers

* These delay values are used as **standard timing inputs** in digital and physical design flows.
### Steps to find buffer delay

1. **Select the buffer cell**
   Use the correct cell (e.g., CBUF1 or CBUF2).

2. **Get input slew**
   Take the input transition time at the buffer input.

3. **Compute output load**
   Sum the wire capacitance and the fanout input capacitances.

4. **Find bounding table entries**

   * Input slew bounds (e.g., 40 ps and 60 ps)
   * Output load bounds (e.g., 50 fF and 70 fF)

5. **Read four corner delays**
   Delays at the 4 combinations of (Slew, load).

6. **Interpolate**

   * First in load direction
   * Then in the slew direction (bilinear interpolation)

7. **Result**
   The interpolated value is the buffer propagation delay.

---

---

## Question 1

**From where do these delay values originate? What is their source? Are these delay models accurate enough to be used in a SPICE model?**

---

## Answer

* The delay values are **derived from SPICE simulations** performed at the **circuit level**.
* During characterisation:

  * A specific **input slew** is applied
  * A defined **output load capacitance** is connected
  * The circuit is simulated using **SPICE**
* The resulting **propagation delay** measured from the simulation is recorded as the delay value.
* To verify the accuracy of the delay model:

  * The **same input slew and output load** are applied again
  * A fresh **SPICE simulation** is run
  * The obtained delay is **compared with the tabulated delay value**
* If both values closely match, the delay model is considered **accurate and valid** for use in SPICE-based timing analysis.

---

---


## Lecture 2

## Introduction to Fundamental Elements in Circuit Design – NMOS

---

### Structure of an NMOS Transistor

<img width="1343" height="509" alt="image" src="https://github.com/user-attachments/assets/2f6e1e2d-1fd1-4f73-a973-2567f8d4958a" />


The figure shows the **cross-sectional structure of an NMOS transistor**.

### 1) NMOS as a 4-Terminal Device

NMOS consists of four terminals:

* **G** — Gate
* **S** — Source
* **D** — Drain
* **B** — Body (Substrate)

---

### 2) Physical Regions

* **P-Substrate**

  * Base semiconductor material
  * Forms the **Body (B)** terminal

* **n⁺ Diffusion Regions**

  * Heavily doped n-type regions
  * Form **Source (S)** and **Drain (D)**

* **Gate Oxide (SiO₂)**

  * Thin insulating layer
  * Electrically isolates the gate from the substrate

* **Poly-Silicon or Metal Gate**

  * Controls channel formation

* **Isolation Oxide**

  * Electrically separates adjacent devices

---

### Working Principle

* When $V_{GS} = 0$:

  * No inversion channel exists
  * No conduction between Source and Drain

* When $V_{GS} > V_{th}$:

  * An **inversion layer (n-channel)** forms
  * Current flows when $V_{DS}$ is applied

* The resulting current is called **Drain Current**, denoted as $I_D$

---

### Body Effect and Threshold Voltage Tuning

Usually, the body is connected to the ground.
However, if a voltage difference exists between Source and Body:

$$
V_{SB} \neq 0
$$

The threshold voltage changes.

---

### Body Effect

* Increasing $V_{SB}$ (reverse-biasing source-body junction):

  * Increases depletion width
  * Requires a higher gate voltage for inversion
  * Therefore increases $V_{th}$

---

### Threshold Voltage with Body Effect

$$
V_{th} = V_{th0} + \gamma \left( \sqrt{2\phi_F + V_{SB}} - \sqrt{2\phi_F} \right)
$$

Where:

* $V_{th0}$ = Threshold voltage when $V_{SB} = 0$
* $\gamma$ = Body-effect coefficient
* $\phi_F$ = Fermi potential
* $V_{SB}$ = Source-to-body voltage

---

### Threshold Voltage
<img width="1352" height="498" alt="image" src="https://github.com/user-attachments/assets/f90da67d-f583-4833-873a-5ef4da14a07e" />


## NMOS Threshold Voltage – Physical Understanding

---

### 1. Importance of Threshold Voltage

* **Threshold Voltage ($V_T$)** is the **most critical parameter** in NMOS modeling.
* It determines:

  * When the NMOS **turns ON**
  * Channel formation
  * Accuracy of **SPICE simulations**
* In SPICE, $V_T$ is not a constant but a **model parameter**, expressed as a function:

```math
V_T = f(x, y, z)
```

* Advanced SPICE models use complex equations, but analysis starts with the **basic threshold voltage model**.

---

### 2. Initial Condition: $V_{GS} = 0$

* Gate, Source, Drain, and Body are all connected to **GND**.
* NMOS structure consists of:

  * **n⁺ Source**
  * **n⁺ Drain**
  * **p-type Substrate (Body)**

### Junction Behaviour

* Source–Body (S–B) and Drain–Body (D–B) form **PN junction diodes**.
* At $0\text{ V}$ bias:

  * Both junctions are **OFF**
  * No conduction path exists

### Electrical State

* Source–Drain resistance is **very high**
* NMOS behaves as an **open switch**
* Drain current:

```math
I_D \approx 0
```

---

### 3. Applying a Small Positive Gate Voltage ($V_{GS} > 0$)

* Gate, oxide, and substrate form a **MOS capacitor**.
* Gate is treated as a **metal plate**.
* Oxide ($SiO_2$) acts as a **dielectric**.

### Charge Redistribution

* Positive voltage on the gate causes:

  * Accumulation of **positive charge on gate**
  * Repulsion of **holes** (majority carriers) from substrate surface
* Holes move deeper into the substrate.

---

### 4. Depletion Region Formation

* Removal of holes near the surface leaves behind:

  * **Fixed negatively charged acceptor ions**
* This creates a **depletion region** below the gate oxide.

### Key Concept

* Depletion region contains:

  * No mobile carriers
  * Only an immobile negative charge
* Still **no conduction channel** exists.

---

### 5. Toward Threshold Voltage 

* Increasing $V_{GS}$ further:

  * Expands depletion region
  * At a critical voltage, the surface **inverts** to n-type
* This voltage is called **Threshold Voltage ($V_T$)**.

```math
V_{GS} = V_T \Rightarrow \text{Onset of inversion}
```

* Inversion layer forms a **conductive channel** between Source and Drain.

---


## Lecture 3: Strong Inversion and Threshold Voltage

---

<img width="1365" height="654" alt="Screenshot 2026-02-17 151838" src="https://github.com/user-attachments/assets/bff35db2-a072-4900-b699-845d41f2cf6c" />

### 1️. Recap: Small Positive $V_{GS}$

* Applying small positive $V_{GS}$:

  * Repels holes (majority carriers in p-substrate)
  * Leaves behind fixed negative acceptor ions
* A **depletion region** forms under the gate.
* Behavior similar to a **reverse-biased PN junction diode**.

---

### 2️. Increasing Gate Voltage

* As $V_{GS}$ increases:

  * More holes are repelled
  * Depletion width increases
* Eventually, the surface concentration of electrons increases.

---

### 3️. Strong Inversion

At a critical gate voltage:

* Surface of p-substrate becomes **n-type**
* A thin **inversion layer** forms.
* This is called **Strong Inversion**.

### Definition

```math
V_{GS} = V_T
```

* The gate voltage at which strong inversion occurs is called
  **Threshold Voltage ($V_T$)**.

---

### 4️. After Threshold ($V_{GS} > V_T$)

* Depletion region width remains approximately constant.
* Additional gate voltage:

  * Does NOT significantly increase depletion width.
  * Increases **channel charge density**.
* Electrons are attracted from a heavily doped **n⁺ source**.
* Channel width increases.

### Result

* A **continuous n-channel** forms between Source and Drain.
* Now current can flow if $V_{DS}$ is applied.

---

### 5️. Region of Operation

* Before inversion → **Cutoff Region**
* After inversion → Channel exists
* Current flows only when:

```math
V_{DS} > 0
```

(Subthreshold current ignored for now.)

---

## 6️. Effect of Body Terminal (Body Effect Introduction)

Consider two cases:

---

### Case 1: $V_{SB} = 0$

* No additional bias between source and body.
* Standard depletion region width.
* Threshold voltage = normal $V_T$.

---
<img width="1307" height="586" alt="Screenshot 2026-02-17 152044" src="https://github.com/user-attachments/assets/1ffa528f-0532-4138-9ecf-55805745b695" />

### Case 2: $V_{SB} > 0$ (Source positive w.r.t Body)

* Source-body PN junction becomes **more reverse biased**.
* Depletion width near the source increases.
* Strong inversion requires a higher gate voltage.

### Key Observation

* For same $V_{GS}$:

  * Inversion happens earlier when $V_{SB} = 0$
  * Inversion is delayed when $V_{SB} > 0$

---

### 7️. Body Effect Concept

Increasing $V_{SB}$:

* Increases depletion width
* Increases required inversion charge
* Increases Threshold Voltage

```math
V_T \uparrow \quad \text{as} \quad V_{SB} \uparrow
```

This is called the **Body Effect**.

---


* **No standalone parentheses**
* **Proper headings only**
* **Image insertion clearly indicated**
* **All equations GitHub-compatible**
* **Zero formatting ambiguity**

---

## Lecture 4 Threshold Voltage with Positive Substrate Potential (Body Effect)

---

### 1. Objective of This Lecture

* Study the effect of **positive source-to-body voltage ($V_{SB} > 0$)** on:

  * Depletion region
  * Channel formation
  * Threshold voltage
* Understand the **body effect**
* Relate physical behavior to **SPICE model parameters**

---

### 2. Case A: Zero Substrate Bias ($V_{SB} = 0$)



* Source and Body are at the same potential.
* No additional reverse bias across the Source–Body PN junction.
* As $V_{GS}$ increases:

  * Holes are repelled from the surface
  * Depletion region grows under the gate
  * Surface eventually inverts to n-type

```math
V_{GS} = V_{TO} \Rightarrow \text{Strong inversion begins}
```

* $V_{TO}$ is defined as:

  * Threshold voltage when $V_{SB} = 0$
  * A function of device and technology parameters

---
<img width="1284" height="601" alt="Screenshot 2026-02-17 153737" src="https://github.com/user-attachments/assets/ac817483-0492-40b3-b79e-cd7e42b83210" />

---
### 3. Case B: Positive Substrate Bias ($V_{SB} > 0$)


* Source is at a higher potential compared to the Body.
* The Source–Body PN junction becomes **more reverse biased**.
* This introduces an **additional depletion region near the source**.

---

### 4. Key Physical Observation

<img width="1394" height="685" alt="Screenshot 2026-02-17 153830" src="https://github.com/user-attachments/assets/56d3908f-98b6-4efa-9318-8919dbf6bb27" />


* In both cases, increasing $V_{GS}$:

  * Repels holes from the surface
  * Initially increases depletion width
* Unique behavior when $V_{SB} > 0$:

  * Channel electrons are pulled toward the Source
  * Positive Source potential attracts a negative charge
* This redistribution does not occur when $V_{SB} = 0$

---

## 5. Effect on Surface Inversion


* For the same gate voltage:

  * Surface inversion occurs earlier when $V_{SB} = 0$
  * Surface inversion is delayed when $V_{SB} > 0$
* Reason:

  * Increased depletion width requires additional gate voltage
  * Channel formation near the Source is resisted

---

## 6. Definition of Threshold Voltages

### Threshold Voltage at Zero Body Bias

```math
V_T = V_{TO} \quad \text{when } V_{SB} = 0
```

* $V_{TO}$ is the reference threshold voltage.

---

### Threshold Voltage with Body Bias

```math
V_T = V_{TO} + V_1 \quad \text{when } V_{SB} > 0
```

* $V_1$ is the additional voltage required due to body bias.

---

## 7. Threshold Voltage Equation Including Body Effect

```math
V_T = V_{TO} + \gamma \left( \sqrt{2\phi_F + V_{SB}} - \sqrt{2\phi_F} \right)
```

### Parameter Definitions

* $V_T$ : Threshold voltage with body bias
* $V_{TO}$ : Threshold voltage at zero body bias
* $\gamma$ : Body effect coefficient
* $\phi_F$ : Fermi potential
* $V_{SB}$ : Source-to-Body voltage

---

### 8. Body Effect Coefficient

```math
\gamma = \frac{\sqrt{2 q N_A \varepsilon_{si}}}{C_{ox}}
```
<img width="484" height="283" alt="Screenshot 2026-02-17 153956" src="https://github.com/user-attachments/assets/7180b9b7-bbf3-4eba-8508-e573883b851c" />


All parameters are **technology-dependent** and provided by the **foundry**.

---

### 9. Fermi Potential

```math
\phi_F = -\phi_T \ln\left(\frac{N_A}{n_i}\right)
```

* $\phi_T$ = Thermal voltage
* $n_i$ = Intrinsic carrier concentration

(Treated as a constant here; covered in device physics.)

---

## 10. Key Conclusions

* Increasing $V_{SB}$:

  * Increases depletion width
  * Delays surface inversion
  * Increases threshold voltage
* Summary:

```math
V_{SB} = 0 \Rightarrow V_T = V_{TO}
```

```math
V_{SB} > 0 \Rightarrow V_T > V_{TO}
```

* This phenomenon is called the **Body Effect**.

---
---

# Chapter 2

### Lecture 1: NMOS Resistive Region and Saturation Region of Operation

---

### Regions of NMOS Operation

* NMOS operates in three regions:

  * **Cutoff region**
  * **Resistive (Linear) region**
  * **Saturation region**

---

### Cutoff Region 

* Condition:

```math
V_{GS} < V_T
```

* No inversion channel
* Drain current approximately zero

---

### Strong Inversion Condition

* When gate voltage reaches threshold:

```math
V_{GS} = V_T
```

* Surface of p-substrate inverts to n-type
* Conducting channel forms between Source and Drain

---

### Resistive (Linear) Region of Operation

<img width="822" height="448" alt="Induced charges" src="https://github.com/user-attachments/assets/d53af40a-d202-40b4-9977-959d3a4c204a" />

*(NMOS with uniform channel and small drain–source voltage)*

### Conditions

```math
V_{GS} > V_T
```

```math
V_{DS} < (V_{GS} - V_T)
```

---

### Physical Interpretation

* Channel exists along the entire length
* Applying a small $V_{DS}$:

  * Creates a voltage gradient from Source to Drain
  * Channel remains continuous
* NMOS behaves like a **voltage-controlled resistor**

---

### Channel Charge Behaviour

* Channel charge density is proportional to:

```math
V_{GS} - V_T
```

* Increasing $V_{GS}$:

  * Increases electron concentration
  * Increases channel width
  * Reduces channel resistance

---

### Voltage Distribution Along Channel

<img width="848" height="584" alt="Screenshot 2026-02-17 163811" src="https://github.com/user-attachments/assets/a3ac14af-a3f5-478a-b0b3-387543b06cbd" />

*Channel potential variation along the channel length
* (Channel potential varies: 0 -> $V_{DS}$).
* Define effective channel length as $L$
* Let $x$ be the position along the channel

```math
V(0) = 0 \quad \text{(Source end)}
```

```math
V(L) = V_{DS} \quad \text{(Drain end)}
```

* Channel voltage varies continuously between 0 and $V_{DS}$

---

### Effective Gate-to-Channel Voltage

* At any point $x$:

```math
V_{GC}(x) = V_{GS} - V(x)
```

* Channel charge is **not uniform**
* Drain current must be derived using position-dependent quantities

---

## Saturation Region of Operation

**Insert Image 3 here**
*(Channel pinch-off near drain)*

### Condition

```math
V_{DS} \ge (V_{GS} - V_T)
```

---

### Physical Interpretation

* Channel near the drain gets pinched off
* Increasing $V_{DS}$:

  * Does not significantly increase channel charge
  * Current becomes weakly dependent on $V_{DS}$
* Drain current mainly controlled by $V_{GS}$

---

## Key Comparison

| Parameter     | Resistive Region      | Saturation Region      |
| ------------- | --------------------- | ---------------------- |
| Channel       | Continuous            | Pinched near drain     |
| $V_{DS}$      | Small                 | Large                  |
| $I_D$ control | $V_{GS}$ and $V_{DS}$ | Mostly $V_{GS}$        |
| Behavior      | Acts as resistor      | Acts as current source |

---





## Lecture 2: Drift Current Theory (Resistive Region with Small $V_{DS}$)

---

### 1. Channel Potential Variation with Applied $V_{DS}$

* Consider the NMOS operating in strong inversion with:
<img width="1313" height="587" alt="Screenshot 2026-02-17 182246" src="https://github.com/user-attachments/assets/db88a991-7fe3-477a-bbff-e5c29a7039f4" />

* $V_{GS} = 1.0\ \text{V}$
* $V_T = 0.45\ \text{V}$
* A small drain–source voltage $V_{DS} = 0.05\ \text{V}$
* At the **source end** of the channel:

  * Channel potential $V(x) = 0$
  * Effective gate-to-channel voltage is:

```math
V_{GS} - V(x) = 1.0\ \text{V}
```

* At the **drain end** of the channel:

  * Channel potential $V(x) = V_{DS} = 0.05\ \text{V}$
  * Effective gate-to-channel voltage becomes:

```math
V_{GS} - V(x) = 0.95\ \text{V}
```

* Therefore, the channel potential is **not constant**.
* A **voltage gradient exists along the channel**, caused purely by the applied $V_{DS}$.
* This gradient is the fundamental reason current can flow from Source to Drain.

---


### 2. Effective Gate Voltage Along the Channel

* When $V_{DS} = 0$, every point along the channel sees the same gate voltage $V_{GS}$.
* When $V_{DS} > 0$, each point along the channel experiences a different effective gate voltage.
* At any position $x$ along the channel:

```math
V_{GC}(x) = V_{GS} - V(x)
```

* Since $V(x)$ varies from 0 at the source to $V_{DS}$ at the drain:

  * The effective gate voltage decreases gradually from source to drain.
* As a result:

  * Channel charge density is **higher near the source**
  * Channel charge density is **lower near the drain**

---

### 3. Induced Channel Charge in Presence of $V_{DS}$

* The inversion charge in the channel depends on:

  * Effective gate voltage
  * Threshold voltage
* At any position $x$, the induced inversion charge per unit area is:

```math
Q_i(x) = C_{ox}\left( V_{GS} - V(x) - V_T \right)
```

* Threshold voltage $V_T$ represents the minimum voltage required to form inversion.
* Only the voltage **above $V_T$** contributes to useful channel charge.
* This expression shows clearly that:

  * Channel charge is **position-dependent**
  * Charge decreases gradually as we move from source to drain

---

### 4. Gate Oxide Capacitance and Technology Parameters

* Gate oxide capacitance per unit area is given by:

```math
C_{ox} = \frac{\varepsilon_{ox}}{t_{ox}}
```

* Where:

  * $\varepsilon_{ox} = 3.97,\varepsilon_0$
  * $t_{ox}$ is the gate oxide thickness
* $t_{ox}$ is:

  * Fixed for a given technology node
  * Provided by the foundry
* Parameters such as $C_{ox}$, $V_T$, and $t_{ox}$:

  * Are **technology constants**
  * Are directly used in SPICE models
* These expressions form the physical foundation before moving to simulations.

---
<img width="1392" height="732" alt="Screenshot 2026-02-17 182345" src="https://github.com/user-attachments/assets/471bce0e-fd26-421c-a0bc-3af251cfe822" />

### 5. Drift Current Concept in NMOS

* From device physics, two current mechanisms exist:

  * Drift current
  * Diffusion current
* In this lecture, the focus is on **drift current**.
* Drift current arises due to:

  * An electric field created by a potential difference
* In the NMOS resistive region:

  * Source is at 0 V
  * Drain is at $V_{DS}$
  * An electric field exists along the channel
* Electrons drift from source to drain under this electric field, producing drain current.

---

### 6. Drain Current Dependence on Channel Charge

* Drain current is fundamentally related to:

  * Amount of charge present in the channel
  * Velocity of charge carriers
  * Channel dimensions
* Conceptually:

```math
I_D \propto (\text{channel charge}) \times (\text{carrier velocity}) \times (\text{channel width})
```

* Channel width $W$ is observed from the **top view** of the MOSFET.
* Channel length $L$ is the distance between source and drain.
* To obtain the final drain current equation:

  * Channel charge expression is combined with carrier velocity
  * Resulting expression is integrated along the channel length
* This derivation leads directly to the **NMOS resistive-region current equation**, which is used in circuit design and delay analysis.


---

## Lecture 3: Drain Current Model for Linear (Resistive) Region of Operation

---

### 1. Objective and Physical Basis of Drain Current Model

* Drain current in NMOS is physically understood as the product of:

  * **Velocity of charge carriers**
  * **Available inversion charge in the channel**
* Goal of this lecture:

  * Develop a **simple, first-order drain current model**
  * Suitable for understanding and later use in **SPICE simulations**
* Although SPICE uses more accurate and complex models, this basic model:

  * Explains the underlying physics
  * Forms the foundation for advanced models

---

### 2. Channel Geometry and Voltage Distribution

<img width="513" height="668" alt="Screenshot 2026-02-17 185514" src="https://github.com/user-attachments/assets/81b6181f-406f-446a-a52b-396f65c7d252" />

*(NMOS cross-section showing effective channel length $L$ and width $W$)

* Channel is the gate-overlap region between source and drain.
* Effective channel length is denoted as $L$.
* Channel width $W$ is visible only in the **top view** of the MOSFET.
* With a small drain–source voltage $V_{DS}$ applied:

  * Source end of channel is at 0 V
  * Drain end of channel is at $V_{DS}$
* Channel potential varies gradually along the length.

Define:

```math
V(x) = \text{Channel voltage at position } x
```

Boundary conditions:

```math
V(0) = 0
```

```math
V(L) = V_{DS}
```

---

### 3. Induced Channel Charge Along the Channel


Inversion charge varying along the channel due to $V(x)$

* Effective gate-to-channel voltage at any point $x$:

```math
V_{GC}(x) = V_{GS} - V(x)
```

* Induced inversion charge per unit area:

```math
Q_i(x) = -C_{ox}\left( V_{GS} - V(x) - V_T \right)
```

* Negative sign indicates electron charge.
* $C_{ox}$ is gate oxide capacitance per unit area:

```math
C_{ox} = \frac{\varepsilon_{ox}}{t_{ox}}
```

* $t_{ox}$ and $\varepsilon_{ox}$ are **technology constants** provided by the foundry.
* Channel charge:

  * Maximum near the source
  * Minimum near the drain

---

### 4. Drift Current Expression and Integration

* Drift current arises due to the electric field along the channel.
* Electron velocity is proportional to the electric field:

```math
v_n(x) = \mu_n \frac{dV}{dx}
```

* Drain current is given by:

```math
I_D = -v_n(x)\, Q_i(x)\, W
```

* Substituting velocity and charge expressions:

```math
I_D\, dx = \mu_n\, C_{ox}\, W \left( V_{GS} - V(x) - V_T \right) dV
```

* Integrate:

  * $x$ from 0 to $L$
  * $V$ from 0 to $V_{DS}$

Resulting expression:

```math
I_D = \mu_n C_{ox}\frac{W}{L}
\left[ (V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2} \right]
```

---

### 5. Linear Region Approximation and Final Model

* Define process transconductance parameter:

```math
k_n' = \mu_n C_{ox}
```

* Drain current equation becomes:

```math
I_D = k_n' \frac{W}{L}
\left[ (V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2} \right]
```

* For **small $V_{DS}$**, such that:

```math
V_{DS} \ll (V_{GS} - V_T)
```

* The quadratic term can be neglected:

```math
\frac{V_{DS}^2}{2} \approx 0
```

* Final **linear-region drain current model**:

```math
I_D = k_n (V_{GS} - V_T)V_{DS}
```

Where:

```math
k_n = k_n' \frac{W}{L}
```

---

## 6. Linear Region Condition and Key Observations

* NMOS operates in **resistive (linear) region** when:

```math
V_{GS} > V_T
```

```math
V_{DS} < (V_{GS} - V_T)
```

* In this region:

  * Drain current is **linear with $V_{DS}$**
  * NMOS behaves like a **voltage-controlled resistor**
* Model parameters $k_n'$, $V_T$, $W$, and $L$:

  * Are supplied through **foundry SPICE model files**
* This model is widely used for:

  * Hand analysis
  * Understanding delay
  * Initial circuit design intuition

---

---

## Lecture 4 & Lecture 5

## SPICE Conclusion to Resistive Operation and Pinch-Off Condition

---

### 1. Motivation for SPICE-Based Analysis

* From earlier lectures, we derived a **linear-region drain current equation** valid when:

  * $V_{GS} > V_T$
  * $V_{DS} < (V_{GS} - V_T)$
* Manually calculating $I_D$ for:

  * Multiple values of $V_{GS}$
  * Sweeping $V_{DS}$ from 0 to $(V_{GS} - V_T)$
    is impractical and time-consuming.
  * Practical circuit design therefore relies on **SPICE simulators**, which:

  * Use compact models
  * Automatically sweep voltages
  * Compute drain current accurately using foundry-provided parameters
* Before running SPICE, it is essential to **physically understand** what happens as $V_{DS}$ increases.

---

### 2. Channel Voltage Concept with Increasing $V_{DS}$

<img width="608" height="437" alt="Screenshot 2026-02-17 192816" src="https://github.com/user-attachments/assets/87ff1cc1-944e-48ce-bf55-679cddb2c52e" />

*(NMOS cross-section showing channel and definition of $V(x)$)*

* Channel voltage at any point $x$ is denoted as $V(x)$.
* Effective gate-to-channel voltage at that point is:

```math
V_{GC}(x) = V_{GS} - V(x)
```

* For a fixed $V_{GS} = 1\ \text{V}$ and $V_T = 0.45\ \text{V}$:

  * When $V_{DS}$ is small, $V_{GC}(x)$ is **greater than $V_T$ everywhere**
  * A continuous inversion channel exists from source to drain
* Channel voltage at the drain end is:

```math
V_{GC,\ drain} = V_{GS} - V_{DS}
```

* As long as:

```math
V_{GS} - V_{DS} > V_T
```

The channel remains intact across the full length.

---

### 3. Sweep of $V_{DS}$ and Channel Validity

<img width="476" height="584" alt="Screenshot 2026-02-17 192535" src="https://github.com/user-attachments/assets/68ff2c67-10cb-453e-9fd7-09bf0300d51c" />

*(Table showing $V_{GS}$, $V_{DS}$, $V_{GS}-V_{DS}$ compared to $V_T$)*

* Keep $V_{GS} = 1\ \text{V}$ constant.
* Gradually increase $V_{DS}$ from 0.05 V upward.
* Observe:

  * For small $V_{DS}$, $V_{GS} - V_{DS} > V_T$
  * Channel exists throughout the device
* At the critical point:

```math
V_{DS} = V_{GS} - V_T = 0.55\ \text{V}
```

* Drain-end channel voltage equals threshold voltage:

```math
V_{GS} - V_{DS} = V_T
```

* This marks the **onset of channel degradation at the drain side**.

---

### 4. Physical Meaning of Pinch-Off Initiation


<img width="1315" height="655" alt="Screenshot 2026-02-17 192645" src="https://github.com/user-attachments/assets/4f0f935f-8cb7-4a63-a68f-d61c6cc2d616" />


* Threshold voltage represents the condition for **surface inversion**.
* When $V_{GS} - V_{DS} = V_T$:

  * Drain-end surface is **just at inversion**
  * Any further increase in $V_{DS}$ causes:

    * $V_{GS} - V_{DS} < V_T$ near drain
    * Inversion channel **disappears near the drain**
* This phenomenon is called **pinch-off**.
* Important observation:

  * Channel still exists near the source
  * Channel disappears progressively toward the drain
* Pinch-off does **not** mean current stops flowing.

---

### 5. Transition from Resistive to Saturation Region

<img width="758" height="650" alt="Screenshot 2026-02-17 192952" src="https://github.com/user-attachments/assets/29cb8206-471e-4c35-831a-3da16440dc74" />

<img width="432" height="190" alt="Screenshot 2026-02-17 193550" src="https://github.com/user-attachments/assets/a7f32d57-148c-43ef-ab7e-896f79f8069d" />

*(Pinch-off region clearly visible near drain)*

* Once pinch-off begins:

  * Drain current is no longer linearly dependent on $V_{DS}$
* Saturation condition is defined as:

```math
V_{DS} \ge (V_{GS} - V_T)
```

* Beyond this point:

  * Increasing $V_{DS}$ increases the depletion region near the drain
  * Channel length effectively shortens
  * Drain current becomes **almost constant**
* This operating mode is called the **saturation region**.

---

### 6. Key Conclusions and Link to SPICE Models

* Summary of regions:

  * **Resistive (Linear) Region**:

    ```math
    V_{GS} > V_T,\quad V_{DS} < (V_{GS} - V_T)
    ```
  * **Pinch-Off / Saturation Region**:

    ```math
    V_{DS} \ge (V_{GS} - V_T)
    ```
* SPICE simulators:

  * Automatically detect region transitions
  * Switch between linear and saturation current models
* Foundry-provided model parameters ensure:

  * Accurate current computation
  * Reliable delay and timing analysis
* Next step:

  * Derive the **saturation-region drain current equation**
  * Validate behaviour through SPICE simulations


---


## Lecture 6: Drain Current Model for Saturation Region of Operation

---



### 1. Channel Voltage Behaviour in Saturation

* In the linear region, channel voltage varies as:

```math
V_{GC}(x) = V_{GS} - V(x)
```

* In saturation:

  * Channel voltage can no longer increase beyond $(V_{GS} - V_T)$.
  * The channel voltage effectively becomes **constant**.
* Therefore, instead of using $V_{DS}$ directly, we replace it with:

```math
V_{DS} = V_{GS} - V_T
```

* This substitution reflects the physical fact that:

  * Any additional $V_{DS}$ drops across the **drain depletion region**, not the channel.

---

### 2. Drain Current Equation in Saturation (Ideal Case)

* Starting from the linear-region drain current equation:

```math
I_D = k_n \left[(V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
```

* Substitute:

```math
V_{DS} = V_{GS} - V_T
```

* The drain current simplifies to:

```math
I_D = \frac{k_n}{2}(V_{GS} - V_T)^2
```

* Key observations:

  * Drain current is **independent of $V_{DS}$**
  * Drain current depends only on:

    * $V_{GS}$
    * $V_T$
    * Device parameters ($W/L$)
* This gives the **ideal saturation current model**.

---

### 3. Why Saturation Current Is Not Truly Constant

* The ideal model suggests NMOS behaves like a **perfect current source**.
* In reality:

  * Increasing $V_{DS}$ increases the **drain-side depletion region**.
  * This reduces the **effective channel length**.
* As effective channel length decreases:

  * Drain current increases slightly with $V_{DS}$.
* This phenomenon is known as **channel length modulation**.

<img width="1319" height="688" alt="Screenshot 2026-02-17 195253" src="https://github.com/user-attachments/assets/6ab861a1-4489-4ae9-8b41-2a1dadd8da02" />


Reduction of effective channel length as $V_{DS}$ increases

---

## 4. Saturation Current with Channel Length Modulation

* To model non-ideal behaviour, introduce channel length modulation factor $\lambda$.
* Modified saturation current equation:

```math
I_D = \frac{k_n}{2}(V_{GS} - V_T)^2(1 + \lambda V_{DS})
```

* Where:

  * $\lambda$ = channel length modulation parameter
  * Supplied by the foundry in SPICE model files
* Effects of $\lambda$:

  * Drain current increases **slightly linearly** with $V_{DS}$
  * Output resistance becomes finite (not infinite)

---
# Chapter 3

## Introduction to SPICE

---

## Lecture 1: Basic SPICE Setup (Linking Device Physics to Simulation)

---

### 1. Context from Saturation Region Operation

* In the previous chapter, NMOS behaviour in the **saturation region** was established.
* Saturation occurs when:

```math
V_{GS} - V_{DS} \le V_T
```

* At this condition:

  * Channel begins to disappear near the drain.
  * Channel voltage no longer follows $V_{GS} - V_{DS}$ everywhere.
* This creates an apparent contradiction:

  * Earlier, channel voltage depended on $V_{DS}$.
  * Now, channel voltage appears fixed.
* Resolution:

  * In saturation, the **channel voltage clamps to $(V_{GS} - V_T)$**.
  * Any extra $V_{DS}$ drops across the drain-side depletion region.

---

### 2. Channel Voltage in Saturation Region

* In linear region:

  * Channel voltage varies along the channel length.
* In saturation region:

  * Channel voltage becomes approximately constant and equal to:

```math
V_{channel} = V_{GS} - V_T
```

* Reason:

  * Drain-end inversion condition is lost.
  * Additional drain voltage no longer modulates channel charge.
* This simplifies current modelling because:

  * Drain current no longer strongly depends on $V_{DS}$.

<img width="1330" height="639" alt="Screenshot 2026-02-17 195233" src="https://github.com/user-attachments/assets/f8c5632a-7347-42f8-8937-7d28c471d7d0" />

Channel pinch-off near the drain and constant channel voltage

---

### 3. Saturation Region Drain Current Model (Ideal)

* Start from the linear-region drain current equation:

```math
I_D = k_n \left[(V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
```

* At saturation boundary, substitute:

```math
V_{DS} = V_{GS} - V_T
```

* Drain current simplifies to:

```math
I_D = \frac{k_n}{2}(V_{GS} - V_T)^2
```

* Observations:

  * Drain current depends only on $V_{GS}$ and $V_T$.
  * Drain current appears independent of $V_{DS}$.
* This gives the **ideal saturation current model**.

---

### 4. Why Ideal Saturation Is Not Fully Accurate

* The ideal model suggests NMOS behaves like a perfect current source.
* In practice:

  * Increasing $V_{DS}$ enlarges the drain-side depletion region.
  * Effective conductive channel length reduces.
* This reduction causes:

  * A small increase in drain current with increasing $V_{DS}$.
* Hence, drain current is **not truly constant** in saturation.

<img width="1278" height="726" alt="Screenshot 2026-02-17 215143" src="https://github.com/user-attachments/assets/878329ac-0ca9-4675-9c5a-4ec377c7e644" />

Reduction of effective channel length with increasing $V_{DS}$

---

### 5. Channel Length Modulation and Improved Model

* To account for non-ideal behaviour, introduce channel length modulation factor $\lambda$.
* Modified saturation current equation:

```math
I_D = \frac{k_n}{2}(V_{GS} - V_T)^2 (1 + \lambda V_{DS})
```

* Where:

  * $\lambda$ models dependency of current on $V_{DS}$.
  * $\lambda$ is provided in foundry SPICE model files.
* Effects:

  * Drain current increases slightly with $V_{DS}$.
  * Output resistance becomes finite.

---

### 6. Motivation for SPICE Simulations

* At this stage, three key models are available:

  1. Threshold voltage model
  2. Linear-region drain current model
  3. Saturation-region drain current model
* Manually evaluating these equations for:

  * Multiple $V_{GS}$
  * Sweeping $V_{DS}$
  * Different technology nodes
    is impractical.
* SPICE simulators:

  * Use these equations internally
  * Take foundry-provided parameters
  * Automatically generate $I_D$ – $V_{DS}$ curves
* Next step:

  * Build a basic SPICE setup
  * Understand required parameters
  * Run simulations for different technology nodes

## Lecture 2

### Circuit Description in SPICE Syntax (NMOS Example)

---

### 1. Objective of This Lecture

* Convert a simple NMOS circuit into a valid SPICE netlist.
* Understand how SPICE interprets a circuit using nodes and components.
* Learn the correct syntax and pin ordering used by SPICE for MOSFETs, resistors, and voltage sources.
* Prepare the circuit description so it can be simulated using a technology (model) file.

---

### 2. Identifying and Defining Nodes


<img width="1286" height="568" alt="Screenshot 2026-02-17 223644" src="https://github.com/user-attachments/assets/c10c0d2d-922c-4db7-8c82-caeecf279c82" />

* In SPICE, a node is a continuous electrical connection with no obstruction in between.
* Any wire segment without a component in between is treated as one node.
* Nodes are identified first before writing the netlist.

For the given circuit:

* Ground node is always named `0` in SPICE.
* The drain supply node is named `VDD`.
* The gate input node is named `IN`.
* The intermediate node between the resistor and the MOSFET drain is named `N1`.

Summary of nodes:

* `0` → Ground (VSS)
* `VDD` → Drain supply node
* `IN` → Gate input node
* `N1` → Drain node of MOSFET and resistor connection

These node names can be chosen freely, but must be used consistently.

---

### 3. MOSFET Declaration in SPICE

* MOSFETs are declared using the letter `M`.
* General syntax:


M<name> <drain> <gate> <source> <bulk> <model> W=<width> L=<length>


* Pin order is fixed and very important:

  * Drain
  * Gate
  * Source
  * Bulk (Body)

<img width="801" height="302" alt="Screenshot 2026-02-17 231023" src="https://github.com/user-attachments/assets/5d2e3b06-1f0f-40ac-b346-cea8f49dfe93" />

For this circuit:

* MOSFET name: `M1`
* Drain node: `N1`
* Gate node: `IN`
* Source node: `0`
* Bulk node: `0`
* Model name: `NMOS`
* Width = 0.8 micron
* Length = 1.2 micron

SPICE line:

```
M1 N1 IN 0 0 NMOS W=0.8u L=1.2u
```

This line fully describes the NMOS device and connects it electrically to the circuit.

---

### 4. Resistor Definition

* Resistors start with the letter `R`.
* Syntax:


R<name> <node1> <node2> <value>


For the circuit:

* Resistor name: `R1`
* Connected between nodes `VDD` and `N1`
* Resistance value: 55 ohms

SPICE line:


R1 VDD N1 55


---

### 5. Voltage Source Definitions

* Voltage sources start with the letter `V`.
* Syntax:


V<name> <positive_node> <negative_node> <value>


Drain supply (VDD):

* Name: `VDD`
* Positive node: `VDD`
* Negative node: `0`
* Voltage: 2.5 V


VDD VDD 0 2.5


Gate input voltage (VIN):

* Name: `VIN`
* Positive node: `IN`
* Negative node: `0`
* Voltage: 2.5 V


VIN IN 0 2.5


---

### 6. Complete SPICE Netlist (So Far)

Putting everything together:


M1 N1 IN 0 0 NMOS W=0.8u L=1.2u
R1 VDD N1 55
VDD VDD 0 2.5
VIN IN 0 2.5


* This netlist fully represents the NMOS circuit topology.
* At this stage, SPICE understands the circuit connectivity but not the device physics.
* The electrical behaviour will only be defined after adding the technology (model) file.

---

## Lecture 3: Circuit Description in SPICE Syntax and Technology Parameters 

---

### 1. Goal of This Lecture

* The objective is to translate a physical NMOS circuit into a SPICE-readable netlist.
* SPICE does not understand schematics or drawings; it only understands text-based descriptions called netlists.
* A valid simulation requires three things:

  * Correct circuit connectivity using nodes
  * Correct device declarations (NMOS, resistors, sources)
  * A technology (model) file that provides device parameters

<img width="621" height="703" alt="Screenshot 2026-02-17 230855" src="https://github.com/user-attachments/assets/1a9ccf29-cbb1-4c8e-82e1-719275678efd" />



---

### 2. Node Identification and Naming

* A node is a continuous electrical connection with no obstruction (no component in between).
* All points connected by plain wires belong to the same node and must share the same node name.
* The ground node is always named `0` in SPICE and is mandatory.

Example node identification in this circuit:


* Node `vdd`: Drain supply node connected to VDD source and NMOS drain
* Node `n1`: Gate node between resistor R1 and NMOS gate
* Node `in`: Input node connected to Vin and resistor R1
* Node `0`: Ground node connected to source and substrate

These node names are user-defined and can be numeric or symbolic, but must be used consistently.

---

### 3. Writing the SPICE Netlist (Circuit Description)

Each component is written as a single line using the format:

* Component name
* Connected nodes
* Component value or model

### NMOS Declaration

* MOSFETs start with the letter `M`
* Order of terminals is strictly: Drain Gate Source Substrate (D G S B)

M1 vdd n1 0 0 nmos W=1.8u L=1.2u

<img width="801" height="302" alt="Screenshot 2026-02-17 231023" src="https://github.com/user-attachments/assets/36baed8c-2b9a-4041-af28-e4d762124ed6" />


Explanation:

* `M1` is the MOSFET name
* `vdd` is the drain
* `n1` is a gate
* `0` is the source
* `0` is substrate (body tied to ground)
* `nmos` is the model name
* `W` and `L` are width and length in microns

### Resistor Declaration

* Resistors start with `R`


R1 in n1 55


* R1 connects node `in` to node `n1`
* Resistance value is 55 ohms

### Voltage Sources

* Voltage sources start with `V`


Vdd vdd 0 2.5
Vin in 0 2.5


* First node is a positive terminal
* The Second node is the negative terminal
* Last value is DC voltage in volts



---

### 4. Role of the Technology (Model) File

* The SPICE engine does not compute NMOS behaviour using geometry alone.
* It requires a model file containing physical and process parameters.
* These parameters come from the foundry and depend on the technology node (1.2 μm, 350 nm, etc.).

Key model parameters include:

* `VTO`: Threshold voltage at VSB = 0
* `gamma`: Body effect coefficient
* `KP` or `kn`: Process transconductance
* `lambda`: Channel length modulation
* `tox`: Oxide thickness
* `u0`: Carrier mobility

These parameters allow SPICE to internally evaluate:

* Threshold voltage equation
* Linear-region drain current
* Saturation-region drain current



---

### 5. Defining NMOS and PMOS Models

Models are defined using the `.MODEL` statement.


.MODEL nmos NMOS (VTO=0.45 GAMMA=0.4 KP=120u LAMBDA=0.02)
.MODEL pmos PMOS (...)


Important rules:

* The model name `nmos` must exactly match the name used in the MOSFET line (`M1 ... nmos`).
* Any mismatch between the model name and the netlist name will cause simulation failure or incorrect results.
* NMOS and PMOS have separate models, even if only NMOS is used in the circuit.

---

### 6. Including the Model File in the Netlist

* Model parameters are usually stored in a separate `.mod` file.
* This file is included in the main netlist using `.include` or `.lib`.
<img width="801" height="716" alt="Screenshot 2026-02-17 231023" src="https://github.com/user-attachments/assets/4ff67588-cc56-4a08-8a6f-cb7b7721a18f" />

Example:


* include xxxx_1um_model.mod
* LIB "xxxx_025um_model.mod" CMOS_MODELS


Structure of a complete SPICE setup:

* Netlist description (devices and connections)
* Model file inclusion
* Simulation commands (to be added next)

Anything starting with `*` is treated as a comment and ignored by SPICE.

## Lecture 4: First SPICE simulation
---

* You start by opening the terminal and attempting to clone the SKY130 workshop repository, but since it already exists, you move into the existing `sky130CircuitDesignWorkshop/design` directory. After listing the files to confirm the setup, you enter the `sky130_fd_pr` folder, which contains the SKY130 technology data used for simulations.
<img width="1920" height="1080" alt="Screenshot 2026-02-23 142911" src="https://github.com/user-attachments/assets/cb00299f-609c-4c44-893c-d68b3a3c63c3" />

* From there, you navigate into the `cells` directory and then into `nfet_01v8`, where the NMOS device models are stored. Listing the contents shows multiple SPICE files corresponding to different process corners such as `tt`, `ss`, `ff`, and others. Opening the typical corner file lets you view the actual model parameters that NGSPICE uses during NMOS simulation, confirming how the device behaviour is defined in SKY130.
---

<img width="1920" height="1080" alt="Screenshot 2026-02-23 143252" src="https://github.com/user-attachments/assets/95c4896b-a993-458c-9ea4-d2bf4b9e3dd2" />

From the `nfet_01v8` folder, you go up to the `models` directory and list the files. This shows `sky130.lib.spice`, which serves as the main entry file that links all NMOS and PMOS models across process corners. Including this one file in a netlist gives access to the complete SKY130 device library. After checking this, you return to the design directory and clear the terminal, confirming that the model setup is complete and ready for simulation.

---
<img width="1920" height="1080" alt="Screenshot 2026-02-23 144507" src="https://github.com/user-attachments/assets/1597b9c9-1195-4b96-ad72-06065c92e568" />

After running the simulation, NGSPICE prints the operating-point information and lists all active vectors, such as node voltages and branch currents. You can see that `vdd#branch` and `vin#branch` are available, which means the simulator is ready to report supply and input currents. Using `plot -vdd#branch` opens the DC plot window and shows the drain current flowing from VDD for different bias conditions.
<img width="1219" height="917" alt="Screenshot 2026-02-23 144635" src="https://github.com/user-attachments/assets/ba68d1e0-d954-4213-898c-f222e479c3ad" />

The plot displays a family of ID–VDS curves as VDD is swept while Vin is stepped. At low VDS, the curves rise almost linearly; at higher VDS, they flatten, indicating saturation. By clicking any point on the plot, NGSPICE reports the exact voltage and current values at the terminals, allowing you to read the drain current directly at a specific operating point and understand NMOS behaviour numerically.

<img width="1218" height="739" alt="Screenshot 2026-02-23 145120" src="https://github.com/user-attachments/assets/a329ed1e-f2a7-43b6-a735-de8b36332edf" />

---

# NgspiceSky130 - Day 2 - Velocity saturation and basics of CMOS inverter VTC

---
# Chapter 1: SPICE simulation for lower nodes and velocity saturation effect
---
## Lecture-1: SPICE simulation for lower nodes

---
<img width="1271" height="743" alt="Screenshot 2026-02-23 162218" src="https://github.com/user-attachments/assets/0e58a96f-827e-4911-8b76-586c985bf967" />

The figures show NMOS **ID–VDS characteristics** with drain current on the y-axis and drain-to-source voltage on the x-axis for multiple gate voltages. For **W = 1.8 µm and L = 1.2 µm (W/L = 1.5)**, the lowest curve corresponds to **VGS = 0 V**, where the device is OFF, and the drain current is zero. As VGS exceeds the threshold, a channel forms and the drain current begins to increase, producing a family of curves at higher VGS values.
<img width="1246" height="750" alt="Screenshot 2026-02-23 170212" src="https://github.com/user-attachments/assets/3b2d50cf-6c95-4727-8dfa-f29b880cf9ea" />

Each curve clearly splits into two regions. At low VDS, the current increases almost linearly with VDS, indicating the **linear (resistive) region**. When VDS reaches approximately **VGS − VT**, marked in the images by the dashed line, the device enters the **saturation region**, where the channel pinches off, and the drain current flattens, increasing only slightly due to channel-length modulation.
<img width="1582" height="845" alt="Screenshot 2026-02-23 220815" src="https://github.com/user-attachments/assets/7f7458ec-0a1a-472c-9415-1543c73fa5a6" />

The same behaviour is then compared with a scaled device having **W = 0.375 µm and L = 0.25 µm**, while keeping **W/L = 1.5**. Although theory suggests the drain current should remain constant for constant W/L, the plots show lower current levels and greater spacing between curves in the smaller device. This difference, clearly visible in the side-by-side images, highlights short-channel effects and shows why long-channel equations no longer accurately predict behaviour at lower technology nodes.



---

## Lecture 2: Drain current vs gate voltage for long and short channel devices

---

<img width="1587" height="916" alt="Screenshot 2026-02-24 102746" src="https://github.com/user-attachments/assets/d5c7a9b2-6f9e-49ae-bf0e-135267ec8a66" />


From the ID–VDS plots, if we fix the drain voltage at **VDS = 2.5 V** and observe the drain current for different gate voltages, the **long-channel device (L = 1.2 µm)** shows a **quadratic dependence of ID on VGS**. At VGS = 0 V, the drain current is zero because there is no channel. As VGS increases to 0.5 V, 1 V, and beyond the threshold, the drain current increases nonlinearly, matching the square-law behaviour predicted by the long-channel MOSFET equation.

```math
I_D \propto (V_{GS} - V_T)^2
```
<img width="1608" height="912" alt="Screenshot 2026-02-24 103334" src="https://github.com/user-attachments/assets/3a5e4307-0af6-4274-bf39-da1d57c454c8" />

For the **short-channel device (L = 0.25 µm)**, the behaviour initially looks similar at low VGS, where the drain current still follows a quadratic trend. However, as VGS increases further, the ID–VGS curve becomes **more linear rather than quadratic**, even though VDS is kept constant at 2.5 V. This deviation is clearly visible in the plotted curves and is not expected from long-channel theory.

This difference occurs because **short-channel effects**, especially **velocity saturation**, become dominant at shorter channel lengths. In short-channel devices, carrier velocity saturates before the square-law behaviour can fully develop, leading to a drain current that increases almost linearly with gate voltage at higher VGS.
<img width="1725" height="627" alt="Screenshot 2026-02-24 103949" src="https://github.com/user-attachments/assets/37afd246-8c55-4515-aa9f-541e572077d3" />

To clearly highlight this effect, the simulation is modified to keep **VDS fixed at 2.5 V** and sweep **VGS**, generating **ID–VGS curves** for both devices. Comparing these plots side by side confirms that long-channel devices follow quadratic behaviour, while short-channel devices deviate significantly, explaining why long-channel equations fail at advanced technology nodes.



---

## Lecture 3: Velocity saturation at lower and higher electric fields
---

For **long-channel devices (≈ 1.2 µm)**, the drain current shows a clear **quadratic dependence on gate voltage** when the drain voltage is fixed. This follows the long-channel saturation model, where
`Id ∝ (VGS − VT)^2`.
At VGS = 0 V, the device is OFF and Id = 0. As VGS increases beyond the threshold, the drain current increases rapidly in a curved (quadratic) manner, which is clearly visible in the ID–VGS plots.

For **short-channel devices (≈ 0.25 µm and below)**, the behaviour changes at higher gate voltages. At low VGS, the drain current still follows a quadratic trend. However, as VGS increases further, the ID–VGS curve becomes **more linear than quadratic**. This deviation shows that long-channel assumptions break down at smaller technology nodes, indicating the presence of short-channel effects.
![download](https://github.com/user-attachments/assets/85fd8cb2-0896-4b70-9870-e6de1ffab4d7)
![download (1)](https://github.com/user-attachments/assets/07367ca2-0884-466b-9fad-30d74e9a2e5b)


The main cause of this behaviour is **velocity saturation**. At low electric fields, carrier velocity increases linearly with electric field (`vn = μn · E`). Beyond a critical electric field, carrier velocity saturates and becomes almost constant (`vn ≈ vsat`) due to scattering effects. Since drain current depends directly on carrier velocity, once velocity saturates, increasing VGS no longer produces a quadratic increase in current, leading to a nearly linear ID–VGS relationship.
<img width="1178" height="512" alt="Screenshot 2026-02-24 105620" src="https://github.com/user-attachments/assets/2bd25916-7b92-447a-b6c0-c5743d7873fa" />

Because of velocity saturation, short-channel MOSFETs introduce an **additional region of operation** beyond cutoff, linear (resistive), and saturation. This new region is called **velocity saturation**. To model all regions using one compact expression, an effective voltage is defined as
`Vmin = min(VGS − VT, VDS, VDSAT)`.
The drain current is zero when `VGS < VT` (cutoff), and in all other regions it follows a unified expression that smoothly captures both long-channel and velocity-saturated short-channel behaviour.

---










