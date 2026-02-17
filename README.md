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



