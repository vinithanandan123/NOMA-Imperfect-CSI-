# NOMA-Imperfect-CSI-
NOMA-Imperfect-CSI/ 

# NOMA Under Imperfect CSI: Power-Allocation Robustness to Channel-Estimation Error

##Project Overview

This project studies the performance of a **Non-Orthogonal Multiple Access (NOMA)** communication system when the available **Channel State Information (CSI)** is imperfect.

In conventional NOMA systems, power allocation depends on accurate channel information. However, in practical wireless communication systems, channel estimation is affected by noise, interference, mobility, and other environmental factors. These estimation errors can reduce the accuracy of power allocation and degrade system performance.

This project models different levels of CSI estimation error and evaluates their effect on the **sum rate and outage probability**. It also compares **fixed power allocation** with a **robust power-allocation strategy** to determine how performance can be improved under imperfect CSI.

---

##  Objectives

The main objectives of this project are:

1. Model a two-user NOMA communication system.
2. Generate wireless channels using a Rayleigh fading model.
3. Introduce configurable channel-estimation error variance.
4. Simulate NOMA performance under different CSI-error levels.
5. Calculate the Signal-to-Interference-plus-Noise Ratio (SINR).
6. Measure the average system sum rate.
7. Measure the outage probability of each user.
8. Compare fixed and robust power-allocation strategies.
9. Study the degradation caused by increasing CSI errors.
10. Identify the tolerable CSI-error threshold.

---

## 📡 NOMA System Model

A base station communicates with two users using the same time and frequency resources.

```text
                 Base Station
                      |
              NOMA Superposition
                 /          \
                /            \
        Weak User          Strong User
        User 1              User 2
       More Power          Less Power
```

The transmitted NOMA signal is represented as:

$$
x = \sqrt{Pa_1}s_1 + \sqrt{Pa_2}s_2
$$

where:

* \(P\) = Total transmit power
* \(a_1\) = Power allocation coefficient for User 1
* \(a_2\) = Power allocation coefficient for User 2
* \(s_1\), \(s_2\) = User information signals

The power allocation coefficients satisfy:

$$
a_1+a_2=1
$$

More power is normally assigned to the weak user:

$$
a_1>a_2
$$

---

## Imperfect CSI Model

The actual channel is modeled as:

$$
h_i = \hat{h}_i + e_i
$$

where:

* \(h_i\) = Actual channel
* \(\hat{h}_i\) = Estimated channel
* \(e_i\) = Channel estimation error

The channel estimation error is modeled as a complex Gaussian random variable:

$$
e_i \sim \mathcal{CN}(0,\sigma_e^2)
$$

where:

$$
\sigma_e^2
$$

is the **CSI-error variance**.

Different values of CSI-error variance are tested to study the robustness of NOMA.

Example:

```text
CSI Error Variance
0
0.01
0.05
0.10
0.20
0.30
0.50
```

A value of zero represents perfect CSI.

---

##  SINR Calculation

For the weak user, the SINR is calculated as:

$$
SINR_1 =
\frac{Pa_1|h_1|^2}
{Pa_2|h_1|^2+N_0}
$$

For the strong user after successful Successive Interference Cancellation (SIC):

$$
SINR_2 =
\frac{Pa_2|h_2|^2}
{N_0}
$$

where \(N_0\) represents the noise power.

The SINR changes with the actual channel conditions and CSI uncertainty.

---

##  Sum Rate

The achievable data rate of each user is calculated using:

$$
R_i=\log_2(1+SINR_i)
$$

The total system sum rate is:

$$
R_{sum}=R_1+R_2
$$

The average sum rate is obtained using a large number of randomly generated channel realizations.

---

## Outage Probability

Outage occurs when the achievable data rate of a user falls below a predefined target rate.

For a target rate \(R_{target}\):

$$
P_{out} =
\frac{\text{Number of outage events}}
{\text{Total number of simulations}}
$$

The outage probability is calculated separately for both users.

A higher outage probability indicates poorer communication reliability.

---

## Power Allocation

### Fixed Power Allocation

The conventional fixed power allocation used in the simulation is:

$$
a_1=0.8
$$

$$
a_2=0.2
$$

Therefore:

```text
Weak User  → 80% power
Strong User → 20% power
```

The allocation remains unchanged even when CSI becomes inaccurate.

### Robust Power Allocation

In robust allocation, the power coefficients are adjusted according to the CSI-error level.

Example:

| CSI Error Variance | Weak User \(a_1\) | Strong User \(a_2\) |
| -----------------: | ----------------: | ------------------: |
|               0.00 |              0.80 |                0.20 |
|               0.05 |              0.82 |                0.18 |
|               0.10 |              0.84 |                0.16 |
|               0.20 |              0.87 |                0.13 |
|               0.30 |              0.90 |                0.10 |
|               0.50 |              0.92 |                0.08 |

The purpose of robust allocation is to reduce the performance degradation caused by CSI uncertainty.

---

##  Simulation Methodology

The simulation follows these steps:

```text
Start
  ↓
Set SNR and system parameters
  ↓
Generate Rayleigh fading channels
  ↓
Generate CSI estimation errors
  ↓
Create imperfect channel information
  ↓
Select fixed power allocation
  ↓
Calculate SINR and user rates
  ↓
Calculate sum rate and outage
  ↓
Select robust power allocation
  ↓
Calculate SINR and user rates
  ↓
Calculate sum rate and outage
  ↓
Repeat Monte Carlo simulations
  ↓
Change CSI-error variance
  ↓
Change SNR
  ↓
Compare fixed and robust allocation
  ↓
Generate performance graphs
  ↓
Find tolerable CSI-error threshold
  ↓
End
```

---

## Simulation Parameters

| Parameter           | Value/Description            |
| ------------------- | ---------------------------- |
| Number of Users     | 2                            |
| Channel Model       | Rayleigh Fading              |
| SNR Range           | 0–30 dB                      |
| Monte Carlo Samples | 100,000                      |
| Fixed Power \(a_1\) | 0.8                          |
| Fixed Power \(a_2\) | 0.2                          |
| CSI Error           | Configurable                 |
| Target Rate         | 1 bit/s/Hz                   |
| Performance Metrics | Sum Rate, Outage Probability |

---

##  Technologies Used

* **Python** — Main simulation language
* **NumPy** — Numerical computation and random channel generation
* **Pandas** — Result storage and analysis
* **Matplotlib** — Performance graphs
* **Google Colab** — Simulation execution
* **GitHub** — Source-code and project management
* **PyCharm** — Development environment

---

##  Project Structure

```text
NOMA-Imperfect-CSI/
│
├── README.md
│
├── requirements.txt
│
├── src/
│   └── noma_simulation.py
│
└── results/
    ├── sum_rate.png
    ├── outage_probability.png
    ├── power_allocation.png
    └── results.csv
```

---

##  How to Run

### Option 1 — Google Colab

Open Google Colab and upload or open the Python source file.

Install the required libraries:

```bash
pip install numpy pandas matplotlib
```

Run:

```bash
python src/noma_simulation.py
```

The simulation generates the required performance results and graphs.

### Option 2 — PyCharm

1. Open the project in PyCharm.
2. Open `src/noma_simulation.py`.
3. Install the required Python packages.
4. Run the Python program.
5. Check the generated graphs and CSV results.

---

##  Expected Results

The simulation is expected to demonstrate the following behavior:

### With increasing CSI error:

```text
CSI Error ↑
     ↓
Channel information becomes inaccurate
     ↓
Power allocation becomes less effective
     ↓
SINR performance decreases
     ↓
Sum Rate decreases
     ↓
Outage Probability increases
```

The fixed power-allocation strategy is expected to experience greater performance degradation as CSI uncertainty increases.

The robust power-allocation strategy is designed to maintain better performance under imperfect CSI.

---

##  Performance Graphs

The project generates the following important graphs:

### 1. Sum Rate vs SNR

Shows how the total NOMA system throughput changes with SNR for different CSI-error levels.

### 2. Outage Probability vs SNR

Shows the reliability of the weak and strong users under different channel conditions.

### 3. Sum Rate vs CSI Error Variance

Shows the degradation in system performance as CSI uncertainty increases.

### 4. Fixed vs Robust Power Allocation

Compares the performance of conventional fixed allocation with the proposed robust allocation.

### 5. Power Allocation vs CSI Error

Shows how the power coefficients are adjusted as CSI uncertainty increases.

---

##Tolerable CSI-Error Threshold

The tolerable CSI-error threshold is determined based on an acceptable performance degradation level.

For example, if a maximum **10% sum-rate degradation** is considered acceptable:

$$
Loss(\%) =
\frac{R_{perfect}-R_{error}}
{R_{perfect}}\times100
$$

The largest CSI-error variance satisfying the selected degradation limit is considered the tolerable CSI-error threshold.

The actual threshold will be obtained from the simulation results.

---

##  Fixed vs Robust Allocation

| Feature                          | Fixed Allocation | Robust Allocation        |
| -------------------------------- | ---------------- | ------------------------ |
| Power allocation                 | Constant         | Adaptive                 |
| CSI uncertainty                  | Not considered   | Considered               |
| Implementation                   | Simple           | More complex             |
| Robustness                       | Lower            | Higher                   |
| Performance under high CSI error | Degrades         | Designed to degrade less |
| Main purpose                     | Baseline         | Improved reliability     |

---

## Expected Outcome

The project aims to demonstrate that imperfect CSI can significantly affect NOMA performance. Increasing channel-estimation error is expected to reduce the achievable sum rate and increase outage probability.

The comparison with robust power allocation will show whether adapting the power distribution according to CSI uncertainty can reduce this degradation.

The final simulation results will be used to identify the **maximum tolerable CSI-error variance** for maintaining acceptable NOMA performance.

---

## Future Enhancements

The project can be extended by:

* Supporting 4, 8, or more NOMA users.
* Including imperfect SIC.
* Implementing optimization-based power allocation.
* Comparing NOMA with OMA.
* Testing different fading channels such as Rician fading.
* Studying user mobility.
* Introducing machine-learning-based power allocation.
* Evaluating energy efficiency.
* Testing the system under different user distances.

---

## Project Type

**Type:** Simulation / Wireless Communication / Machine Learning-Data Simulation

**Topic:** NOMA under Imperfect CSI

**Focus:** Power-allocation robustness to channel-estimation error

**Primary Language:** Python

---

This project provides a simulation-based study of **NOMA power allocation under imperfect CSI**. By introducing configurable channel-estimation errors, the project measures their impact on sum rate and outage probability.

The comparison between fixed and robust power allocation provides an understanding of how NOMA systems can be made more reliable when accurate channel information is not available. The identified CSI-error threshold can be used as an indicator of the practical robustness of the NOMA system.
