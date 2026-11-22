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
