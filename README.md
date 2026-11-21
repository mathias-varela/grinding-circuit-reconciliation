# Ball Mill Circuit: Mass Balance and Data Reconciliation

Ball mill-hydrocyclone circuit model: Bond energy, mass balance and data reconciliation, using simulated data with a known ground truth.

> **Note:** All the data used in this project is simulated. Ranges are based on published references and stated assumptions (see [Design basis](#design-basis)).

## Problem

In a grinding circuit, unexpected problems can happen. In order to solve them, decisions must be made according to in-field measurements. These measurements are often imprecise, so mass balances rarely close.
A second problem is that when throughput drops, it is unclear whether the cause is ore properties or circuit performance.

To address these issues, this project shows how data reconciliation applied to simulated data can take noisy measurements and adjust them as little as possible, giving more weight to the more reliable data so the mass balance is satisfied.
For validation, the true values are known, so they can be compared with the reconciled data.
Also, the ratio of the operating work index to the Bond work index is used to separate ore properties (hardness) from circuit performance.
