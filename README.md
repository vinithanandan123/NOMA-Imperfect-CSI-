# NOMA-Imperfect-CSI-
NOMA-Imperfect-CSI/ 
Simulation project for my wireless communications course - looking at how a NOMA (Non-Orthogonal Multiple Access) system holds up when the channel state info (CSI) you're using isn't perfect.

## Why this project

Most of the basic NOMA power allocation stuff you read about assumes you know the channel exactly. That's not realistic - in a real system the receiver is estimating the channel and that estimate has noise in it, so whatever power split you calculated based on it is going to be a little (or a lot) off. I wanted to actually simulate that and see how bad it gets, and whether you can fix it by adjusting the power allocation instead of just eating the loss.

So basically two things I'm comparing:
- fixed power split that never changes no matter how bad the CSI is
- a "robust" split that shifts more power to the weak user as the CSI error grows

## System setup

Two users, one base station, classic NOMA downlink. Base station sends both signals superimposed on top of each other using the same time/frequency:

x = sqrt(P*a1)*s1 + sqrt(P*a2)*s2

a1 and a2 are the power coefficients (add up to 1), and the weak user always gets more power (a1 > a2) so it can decode its own signal treating the other one as noise. Strong user does SIC (successive interference cancellation) to strip out the weak user's signal first, then decodes its own.

Channels are Rayleigh fading, which is the standard assumption for NLOS mobile links.

## Modeling the imperfect CSI part

This is really the core of the project. The actual channel h_i is not what we know - what we have is an estimate h_i_hat, and the error between them:

h_i = h_i_hat + e_i

where e_i is complex Gaussian, mean zero, variance sigma_e^2. sigma_e^2 = 0 means perfect CSI (the ideal case everyone assumes), and I ramp it up from there to see the effect:

0, 0.01, 0.05, 0.10, 0.20, 0.30, 0.50

(these aren't set in stone, I picked them to get a reasonable spread from "basically nothing" to "pretty bad estimation")

## SINR / rate equations

Weak user:
SINR1 = (P*a1*|h1|^2) / (P*a2*|h1|^2 + N0)

Strong user (after SIC removes user 1's signal):
SINR2 = (P*a2*|h2|^2) / N0

Rate for each user: R_i = log2(1 + SINR_i), and sum rate is just R1 + R2. I average this over a lot of Monte Carlo channel draws (100k) to smooth out the randomness.

Outage: pick a target rate (I used 1 bit/s/Hz), and count how often a user's actual rate falls under that. Outage probability = outage count / total trials. Did this separately per user since they don't behave the same way.

## Power allocation - fixed vs robust

Fixed allocation is dead simple, just:
a1 = 0.8, a2 = 0.2

...and it never moves regardless of how bad the CSI gets, which is obviously not great once the error variance climbs.

For the robust version I bump a1 up as sigma_e^2 increases, roughly like this (values below are what I tested, not derived from some formula - more of a heuristic sweep, might revisit this later with an actual optimization):

| CSI error var | a1 (weak) | a2 (strong) |
|---|---|---|
| 0.00 | 0.80 | 0.20 |
| 0.05 | 0.82 | 0.18 |
| 0.10 | 0.84 | 0.16 |
| 0.20 | 0.87 | 0.13 |
| 0.30 | 0.90 | 0.10 |
| 0.50 | 0.92 | 0.08 |

Idea being: if you're less sure about the channel, hedge by giving the weak user a bit more of a cushion.

## How I actually ran the sim

Okay this part is messier than I'd like to admit. Roughly what happens:

1. set SNR range and basic params
2. generate Rayleigh channels for both users
3. loop over CSI error variances (I moved this loop outside the SNR loop at some point because it was faster, originally had it nested the other way)
4. add the CSI error on top of the channel to get the "estimated" version - actually I think in the code this happens before step 3 even starts, I generate all the error variants up front and just index into them. Need to double check that's not causing reused-noise bugs across SNR values.
5. run fixed allocation on it, log SINR/rate/outage
6. loop over SNR values (yes, inside the error loop, I know it's backwards from how I wrote it above)
7. robust allocation - I originally had this as a totally separate script and only merged it in later, so the way it logs results doesn't quite match the fixed allocation logging format yet
8. Monte Carlo repeats - this is honestly folded into the channel generation step (I generate 100k draws at once instead of looping 100k times), so it's not really its own separate "step" in the code, more just a big array
9. dump to CSV
10. make plots - did this last, after already looking at raw numbers first to sanity check them
11. threshold calc - forgot to make this part of the main loop, currently a separate script that reads the CSV back in

So the actual step order in `src/noma_simulation.py` doesn't cleanly match a 1-10 list, it's closer to: params → generate all channels/errors up front → loop error variance → loop SNR → both allocations → log → (later, separately) plots → (later still) threshold. If you're reading the code and it looks out of order compared to this readme, the code is right, I just haven't gone back to fix this section.

## Parameters used

| Parameter | Value |
|---|---|
| Users | 2 |
| Channel | Rayleigh fading |
| SNR range | 0-30 dB |
| Monte Carlo trials | 100,000 |
| Fixed a1 / a2 | 0.8 / 0.2 |
| CSI error variance | 0 to 0.50 (see list above) |
| Target rate | 1 bit/s/Hz |
| Metrics | sum rate, outage probability |

## Tools

Python (numpy for the channel gen and math, pandas for storing/organizing results, matplotlib for plots). Wrote it in PyCharm and also ran chunks of it in Colab when I wanted to check plots faster without setting up my laptop.

## Project layout

```
NOMA-Imperfect-CSI/
├── README.md
├── requirements.txt
├── src/
│   └── noma_simulation.py
└── results/
    ├── sum_rate.png
    ├── outage_probability.png
    ├── power_allocation.png
    └── results.csv
```

## Running it

Colab:
```
pip install numpy pandas matplotlib
python src/noma_simulation.py
```

Or just open it in PyCharm, install the same 3 packages, hit run, check `results/` for the plots and csv afterward.

## What I expected going in (and mostly got)

As CSI error goes up, the estimated channel gets less reliable, which throws off the power split, which drags SINR down, which drags sum rate down and pushes outage probability up. Fixed allocation should degrade faster than robust since it never adapts. Robust should hold up a bit better but obviously isn't magic - it's still working off the same noisy estimate, just hedging against it.

## Plots I generated

- Sum rate vs SNR (different CSI error curves overlaid)
- Outage probability vs SNR, weak vs strong user
- Sum rate vs CSI error variance
- Fixed vs robust comparison
- How the power coefficients shift as CSI error grows

## Tolerable CSI-error threshold

Wanted to find the max error variance where the sum rate loss stays under some cutoff (I used 10% as a reasonable bar):

Loss(%) = (R_perfect - R_error) / R_perfect * 100

Whatever the largest sigma_e^2 is that still keeps loss under 10%, that's the threshold I'm calling "tolerable." Exact number comes out of the results, didn't want to hardcode a guess here since it depends on the SNR too.

## Fixed vs Robust, quick summary

| | Fixed | Robust |
|---|---|---|
| Power split | constant | adapts to error |
| Accounts for CSI uncertainty | no | yes |
| Simplicity | simple | more moving parts |
| Robustness | lower | higher |
| High-error behavior | degrades hard | degrades slower |
| Point of it | baseline to compare against | trying to actually fix the problem |

## TL;DR

Two-user NOMA sim under Rayleigh fading, with a controllable amount of CSI estimation error layered on top. Compares a fixed 80/20 power split against a power split that adapts as the CSI gets worse, tracking sum rate and outage probability across SNR and error levels. Point is to see how much NOMA performance actually degrades from bad channel estimates, and whether adapting the allocation buys you anything meaningful.
