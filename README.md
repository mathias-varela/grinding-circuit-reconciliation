# Ball Mill Circuit: Mass Balance and Data Reconciliation

Ball mill-hydrocyclone circuit model: Bond energy, mass balance and data reconciliation, using simulated data with a known ground truth.

> **Note:** All the data used in this project is simulated. Ranges are based on published references and stated assumptions (see [Design basis](#design-basis)).

## Problem

In a grinding circuit, unexpected problems can happen. In order to solve them, decisions must be made according to in-field measurements. These measurements are often imprecise, so mass balances rarely close.
A second problem is that when throughput drops, it is unclear whether the cause is ore properties or circuit performance.

To address these issues, this project shows how data reconciliation applied to simulated data can take noisy measurements and adjust them as little as possible, giving more weight to the more reliable data so the mass balance is satisfied.
For validation, the true values are known, so they can be compared with the reconciled data.
Also, the ratio of the operating work index to the Bond work index is used to separate ore properties (hardness) from circuit performance.

## Circuit

This project covers a secondary grinding circuit. The circuit is fed by a SAG mill, which is out of scope because Bond's equation doesn't apply to SAG mills.

In a SABC-A circuit, the SAG mill reduces ore size. The SAG trommel undersize falls into a sump. Pebbles (25-50 mm), which are too large to be broken efficiently and too small to act as grinding media, are rejected by the SAG mill and conveyed to a pebble crusher. There, they are crushed and returned to the SAG mill. 
Returning the crushed pebbles to the SAG mill is what defines a SABC-A circuit.

The SAG trommel undersize already contains fines below the circuit's target size (P80). Therefore, to remove these particles from the circuit and avoid overgrinding, the sump slurry is pumped to hydrocyclones, which act as size classifiers. 
If ore particles satisfy the target size (P80), they mostly report to the cyclone overflow, leaving the circuit and going to flotation. 
On the other hand, particles that don't satisfy the target size report to the underflow and flow by gravity into the ball mill. The ball mill continues the grinding, and discharges into the sump, from where the sump slurry is pumped back to the hydrocyclones. 
This defines a reverse classification circuit (RCC).

## Design basis

This plant is based on Cadia Hill, a SABC-A concentrator described in Wills & Finch (2016), who cite Dunne et al. (2001). I chose SABC-A because Cadia is well documented, and because the ball mill feed doesn't depend on how many pebbles the SAG mill produces. Several Chilean copper plants use SABC-B instead.

Cadia has two identical ball mill lines. This project models one of them, so each line receives half of the SAG mill feed.

Each value in the table is marked as **Literature** (from a published source), **Assumption** (my own choice, with a reason) or **Derived** (calculated from other values in the table).

| Parameter | Nominal | Range considered | Basis | Source / justification |
|---|---|---|---|---|
| Fresh feed per ball mill line | 1,030 t/h | ~870–1,260 t/h | Literature / Derived | Cadia: 2,065 t/h shared by two lines (Wills & Finch, 2016, p. 172). The range comes from keeping mill power constant while Wi changes |
| Ball mill installed power | 9.7 MW | — | Literature | Cadia (Wills & Finch, 2016, p. 172) |
| Ball mill power draw (pinion) | 8.6 MW | approximately constant | Derived | Bond's equation with nominal values, no efficiency factors. It is 88% of the installed power |
| Circuit product size, P80 | 150 µm | 125–180 µm | Literature | Cadia design product (Wills & Finch, 2016, p. 172). The range is used in the sensitivity analysis |
| Fresh feed size, F80 | 2,500 µm | 1,500–3,500 µm | Assumption | Must be below the ~12 mm trommel cut (Wills & Finch, 2016, p. 172). I kept the upper limit below Bond's optimum feed size, so no coarse-feed correction is needed |
| Bond ball mill work index, Wi | 13.5 kWh/t | 11–16 kWh/t | Assumption | Close to quartz (13.57) and granite (15.13) (Wills & Finch, 2016, Table 5.1, p. 113) |
| Circulating load | 300% | 200–350% | Literature | Typical range is 150–350% (Wills & Finch, 2016, p. 170) |
| Cyclone underflow / overflow solids | 75% / 35% w/w | ±3 points | Assumption | Chosen so the cyclone feed density matches Wills & Finch (2016, p. 212) |
| Cyclone feed solids | ~58% w/w | — | Derived | Comes from the water balance. Closed grinding circuits use up to ~60% (Wills & Finch, 2016, p. 212) |
| Ball mill discharge solids | 72% w/w | ±3 points | Assumption | Typical value. No source found yet |
| Cyclone feed pressure | 60 kPa | 50–69 kPa | Literature / Assumption | Usually below 69 kPa in closed grinding circuits (Wills & Finch, 2016, p. 212) |
| Ore specific gravity | 2.7 | — | Assumption | Typical of silicate host rocks |
| Weightometer (SAG feed conveyor) error | 1.5% of full scale (2,500 t/h) | 1–2% of full scale | Literature / Assumption | Belt scale accuracy (Wills & Finch, 2016, p. 56). I assumed a full scale 20% above the SAG design feed, and took the accuracy as two standard deviations |
| Flowmeter and density gauge error | ±1.5% | ±1–2% | Assumption | Wills & Finch (2016, pp. 57–59) describe these instruments but don't give their accuracy |

### Nominal mass balance

| Stream | Solids (t/h) | Water (m³/h) | Solids (% w/w) |
|---|---|---|---|
| Fresh feed (F) | 1,030 | see sump water | — |
| Cyclone feed (C) | 4,120 | 2,943 | 58 |
| Cyclone underflow (U), to ball mill | 3,090 | 1,030 | 75 |
| Cyclone overflow (O), circuit product | 1,030 | 1,913 | 35 |
| Ball mill discharge (M) | 3,090 | 1,202 | 72 |
| Water added at the ball mill feed | — | 172 | — |
| Water entering the sump (fresh feed water + dilution water) | — | 1,741 | — |

### Consistency check

As a quick check, I used Bond's equation with the nominal values and no efficiency factors: W = 10 × 13.5 × (1/√150 − 1/√2,500) ≈ 8.3 kWh/t. At 1,030 t/h, this gives about 8.6 MW at the pinion. Cadia's ball mills have 9.7 MW motors, so the result makes sense: the motor is bigger than the power the mill actually draws. Rowland's efficiency factors will be added later, and they should lower this value a bit.

### Assumptions and limitations
-**Circulating load:** Using a 300% circulating load doesn't affect Bond's energy, since Bond's equation and Rowland's factors don't depend on it. Therefore, it is left out of the sensitivity analysis. Limitation: in real operations circulating load affects the circuit capacity (Wills & Finch, 2016, p. 170).

-**Bond with SAG mill product:** Bond's equation tends to overestimate the specific energy when the ball mill feed is SAG product, since it carries more fines than the feed Bond's equation was calibrated on. Morrell's model, on the other hand, includes ore particle size as a relevant factor. Limitation: this project uses Bond's equation because it is the most widely used model for ball mill modeling, so specific energy values may be somewhat overestimated. (Wills & Finch, 2016, p. 112)

-**Constant power draw:** The ball mill operates at an approximately constant power draw because it has a fixed speed and a stable ball charge. Therefore, throughput is calculated from mill power draw and ore hardness (Wi): with a harder ore, the same power treats fewer tonnes. Limitation: in real operations, power draw changes according to ball and liner wear, and ball addition.

-**One line of two:** Cadia's circuit consists of two parallel ball mills (Wills & Finch, 2016, p. 172). This project models only one of them. It is assumed that the fresh feed is split equally between the two lines. Limitation: in real operations, the feed is rarely split exactly equally between lines, and the feed to each line is not measured directly: it is estimated from the SAG feed weightometer and the cyclone overflow mass flowmeter.  