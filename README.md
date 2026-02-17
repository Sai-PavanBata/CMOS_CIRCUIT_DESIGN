# CMOS_CIRCUIT_DESIGN

---

## VirtualBox Setup

Follow the steps below to load the provided **CMOS VDI file** in **Oracle VirtualBox**.

---

### Step 1: Install VirtualBox

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

Perfect—here’s the **lecture-note style version**, structured, concise, and exam-friendly while preserving the **exact meaning**.

---

# Ngspice SKY130 – Day 1

## Basics of NMOS Drain Current (Id) vs Drain-to-Source Voltage (Vds)

---

## Introduction to Circuit Design and SPICE Simulations

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

## Role of SPICE in VLSI Physical Design

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
Here’s the **lecture-note/exam-answer style rewrite**, keeping the meaning exactly the same while making it clearer and more structured.

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


Here is the **lecture-note style explanation**, structured and exam-ready.

---


# Lecture 2

## Introduction to Fundamental Elements in Circuit Design – NMOS

---

## Structure of an NMOS Transistor

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

## Working Principle

* When $V_{GS} = 0$:

  * No inversion channel exists
  * No conduction between Source and Drain

* When $V_{GS} > V_{th}$:

  * An **inversion layer (n-channel)** forms
  * Current flows when $V_{DS}$ is applied

* The resulting current is called **Drain Current**, denoted as $I_D$

---

## Body Effect and Threshold Voltage Tuning

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

## Threshold Voltage
<img width="1352" height="498" alt="image" src="https://github.com/user-attachments/assets/f90da67d-f583-4833-873a-5ef4da14a07e" />


## NMOS Threshold Voltage – Physical Understanding

---

## 1. Importance of Threshold Voltage

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

## 2. Initial Condition: $V_{GS} = 0$

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

## 3. Applying a Small Positive Gate Voltage ($V_{GS} > 0$)

* Gate, oxide, and substrate form a **MOS capacitor**.
* Gate is treated as a **metal plate**.
* Oxide ($SiO_2$) acts as a **dielectric**.

### Charge Redistribution

* Positive voltage on the gate causes:

  * Accumulation of **positive charge on gate**
  * Repulsion of **holes** (majority carriers) from substrate surface
* Holes move deeper into the substrate.

---

## 4. Depletion Region Formation

* Removal of holes near the surface leaves behind:

  * **Fixed negatively charged acceptor ions**
* This creates a **depletion region** below the gate oxide.

### Key Concept

* Depletion region contains:

  * No mobile carriers
  * Only an immobile negative charge
* Still **no conduction channel** exists.

---

## 5. Toward Threshold Voltage (Preview)

* Increasing $V_{GS}$ further:

  * Expands depletion region
  * At a critical voltage, the surface **inverts** to n-type
* This voltage is called **Threshold Voltage ($V_T$)**.

```math
V_{GS} = V_T \Rightarrow \text{Onset of inversion}
```

* Inversion layer forms a **conductive channel** between Source and Drain.

---





