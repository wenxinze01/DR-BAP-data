# Data Description and Out-of-Sample Evaluation Guide

This document explains how to read the uploaded data, how to interpret the assignment solution matrix, and how to use the matrix to conduct out-of-sample testing.

The data are associated with the numerical experiments of the manuscript submitted to the International Journal of Production Research. The manuscript studies a robust berth allocation and scheduling problem under uncertain vessel arrival and processing times.

The main purpose of the uploaded data is to allow readers to reproduce the out-of-sample evaluation results reported in the manuscript.

---

## 1. Problem size

Each data file begins with the basic size information of the instance. For example:

```text
Jobs = 20
Machines = 5
Scenarios = 20
```

In this repository:

- `Jobs` denotes the number of vessels.
- `Machines` denotes the number of berths.
- `Scenarios` denotes the number of uncertainty scenarios.

Although the data files use the general terms `Jobs` and `Machines`, they correspond to vessels and berths in the berth allocation and scheduling problem.

For example:

```text
Jobs = 20
Machines = 5
Scenarios = 20
```

means that the instance contains 20 vessels, 5 berths, and 20 out-of-sample testing scenarios.

---

## 2. Data structure

Each instance file contains several groups of data. The main data include:


2. berthing times under scenarios;
3. processing times under scenarios;
4. assignment costs;
5. scenario probabilities;
6. assignment solution matrices.

The data are indexed by vessel, berth, and scenario.

The notation is summarized as follows:

| Notation | Meaning |
|---|---|
| `j` | vessel index |
| `k` | berth index |
| `s` | scenario index |
| `tjk^s` | berthing time of vessel `j` at berth `k` under scenario `s` |
| `xi_{jk}^s` | processing time of vessel `j` at berth `k` under scenario `s` |
| `cjk` | assignment cost of assigning vessel `j` to berth `k` |
| `vjk` | assignment solution matrix |
| `p_s` | scenario probability |

---

## 3. Ready times

The ready time data are written as:

```text
Ready Times (rj) = [
0.00, 0.00, 0.00, ..., 0.00
]
```

`rj` denotes the basic ready time of vessel `j`.

In the current data, the ready time of each vessel is set to zero. Therefore, the effective scenario-dependent berthing availability time is mainly determined by `tjk^s`.

For vessel `j`, berth `k`, and scenario `s`, the effective arrival or berthing availability time is calculated as:

```text
rj[j] + tjk_s[j][k][s]
```

---

## 4. Berthing times

The berthing time data are written as:

```text
Transportation Times (tjk^s)
```

In this repository, `tjk^s` denotes the berthing time of vessel `j` at berth `k` under scenario `s`.

For example:

```text
tjk_11 = [2.53, 2.59, 3.58, 2.45, 2.49, 2.69, 2.34, 3.39, 2.55, 3.22,
          3.12, 3.14, 2.97, 2.96, 2.79, 2.88, 3.07, 2.25, 2.76, 2.52]
```

This means that the berthing time of vessel 1 at berth 1 is given under 20 different scenarios.

If `Scenarios = 20`, then each `tjk` row contains 20 values.

The general interpretation is:

```text
tjk_11 = berthing times of vessel 1 at berth 1 under all scenarios
tjk_12 = berthing times of vessel 1 at berth 2 under all scenarios
tjk_21 = berthing times of vessel 2 at berth 1 under all scenarios
```

and so on.

---

## 5. Processing times

The processing time data are written as:

```text
Service Times (xi_{jk}^s)
```

In this repository, `xi_{jk}^s` denotes the processing time of vessel `j` at berth `k` under scenario `s`.

For example:

```text
xi_11 = [2.01, 1.00, 10.00, 1.00, 2.64, 1.00, 7.38, 4.52, 1.00, 10.00,
         5.86, 1.29, 10.00, 10.00, 10.00, 3.18, 4.01, 5.17, 10.00, 10.00]
```

This means that the processing time of vessel 1 at berth 1 is given under 20 different scenarios.

If `Scenarios = 20`, then each `xi` row contains 20 values.

The general interpretation is:

```text
xi_11 = processing times of vessel 1 at berth 1 under all scenarios
xi_12 = processing times of vessel 1 at berth 2 under all scenarios
xi_21 = processing times of vessel 2 at berth 1 under all scenarios
```

and so on.

---

## 6. Assignment cost matrix

The assignment cost matrix is given in the data file under:

```text
Transportation Costs
```

In the berth allocation and scheduling problem, this matrix represents the operating cost of assigning vessel `j` to berth `k`.

The assignment cost is fixed once the assignment solution matrix is determined. It does not change across out-of-sample scenarios.

For a given solution matrix `vjk`, the total assignment cost is calculated as:

```text
total_assignment_cost = sum cjk[j][k] * vjk[j][k]
```

over all vessels `j` and berths `k`.

---

## 7. Scenario probabilities

The scenario probabilities are written as:

```text
Probabilities (p_s)
```

`p_s` denotes the nominal probability of scenario `s`.

In the out-of-sample evaluation, the scenario objective values are first calculated under all testing scenarios. Then the average and percentile metrics are computed from these values.

---

## 8. Assignment solution matrix

The solution of a model is represented by an assignment matrix:

```text
vjk
```

where:

```text
vjk[j][k] = 1
```

means that vessel `j` is assigned to berth `k`, and

```text
vjk[j][k] = 0
```

means that vessel `j` is not assigned to berth `k`.

Each row corresponds to one vessel, and each column corresponds to one berth.

For example:

```text
vjk = [
[0, 1, 0, 0, 0],
[0, 0, 1, 0, 0],
[1, 0, 0, 0, 0],
...
]
```

This means:

- vessel 1 is assigned to berth 2;
- vessel 2 is assigned to berth 3;
- vessel 3 is assigned to berth 1.

Each vessel must be assigned to exactly one berth. Therefore, each row of `vjk` should contain exactly one value equal to 1.

---

## 9. How to use the assignment matrix for out-of-sample testing

The out-of-sample test evaluates a fixed assignment matrix under testing scenarios.

The key idea is:

1. fix the assignment matrix `vjk`;
2. evaluate this assignment under each out-of-sample scenario;
3. sequence vessels at each berth using the ERD rule;
4. compute the objective value under each scenario;
5. calculate average and percentile performance metrics.

The detailed procedure is described below.

---

### Step 1. Read the data

For each instance, read the following data:

```text
rj[j]
tjk_s[j][k][s]
xi_jk_s[j][k][s]
cjk[j][k]
p_s[s]
```

where:

- `rj[j]` is the ready time of vessel `j`;
- `tjk_s[j][k][s]` is the berthing time of vessel `j` at berth `k` under scenario `s`;
- `xi_jk_s[j][k][s]` is the processing time of vessel `j` at berth `k` under scenario `s`;
- `cjk[j][k]` is the assignment cost of assigning vessel `j` to berth `k`;
- `p_s[s]` is the probability of scenario `s`.

---

### Step 2. Input a fixed assignment matrix

A fixed assignment solution matrix `vjk` is used as the input of the out-of-sample test.

For each berth `k`, define the set of vessels assigned to berth `k` as:

```text
S_k = {j | vjk[j][k] = 1}
```

For example, if vessel 1 and vessel 5 are assigned to berth 2, then:

```text
S_2 = {1, 5}
```

---

### Step 3. Compute the fixed assignment cost

The assignment cost is independent of scenarios. It is calculated once for the given solution matrix:

```text
total_assignment_cost = sum cjk[j][k] * vjk[j][k]
```

This cost remains unchanged across all out-of-sample scenarios.

---

### Step 4. Sequence vessels at each berth under each scenario

For each scenario `s` and each berth `k`, the vessels assigned to berth `k` are sequenced using the Earliest Release Date (ERD) rule.

The ERD rule sorts the assigned vessels according to:

```text
rj[j] + tjk_s[j][k][s]
```

That is, the vessel with the smaller effective berthing time is processed earlier.

For berth `k` under scenario `s`, the sorting rule is:

```text
sorted_Sk = sorted(S_k, key = rj[j] + tjk_s[j][k][s])
```

---

### Step 5. Compute start times and completion times

After sequencing the vessels at berth `k` under scenario `s`, the start time and completion time of each vessel are calculated.

For vessel `j`, the start time is:

```text
start_time_j = max(current_time, rj[j] + tjk_s[j][k][s])
```

The completion time is:

```text
completion_time_j = start_time_j + xi_jk_s[j][k][s]
```

where:

- `current_time` is the completion time of the previous vessel on the same berth;
- `rj[j] + tjk_s[j][k][s]` is the effective berthing time of vessel `j`;
- `xi_jk_s[j][k][s]` is the processing time of vessel `j`.

After vessel `j` is processed, update:

```text
current_time = completion_time_j
```

---

### Step 6. Compute berth-level completion value

For berth `k` under scenario `s`, the berth-level completion value is the maximum completion time of all vessels assigned to berth `k`:

```text
C_k^s = max completion time of vessels assigned to berth k
```

If no vessel is assigned to berth `k`, then:

```text
C_k^s = 0
```

---

### Step 7. Compute scenario-level delay or completion value

For each scenario `s`, the total completion or delay-related value is calculated by summing the berth-level completion values over all berths:

```text
scenario_total_delay = sum C_k^s
```

over all berths `k`.

---

### Step 8. Compute scenario-level objective value

The scenario-level objective value is calculated as:

```text
scenario_objective_value = total_assignment_cost + scenario_total_delay
```

This value is computed for every out-of-sample scenario.

For example, if there are 20 out-of-sample scenarios, then the evaluation produces 20 scenario objective values:

```text
scenario_objective_values = [
value under scenario 1,
value under scenario 2,
...
value under scenario 20
]
```

---

### Step 9. Compute out-of-sample performance metrics

After obtaining all scenario objective values, the following metrics are calculated.

#### Average objective value

```text
Obj/Avg = average of scenario_objective_value over all scenarios
```

This metric measures the average out-of-sample performance of the solution.

#### 85th percentile

```text
PT85 = 85th percentile of scenario_objective_value
```

This metric measures the upper-tail performance of the solution.

#### 95th percentile

```text
PT95 = 95th percentile of scenario_objective_value
```

This metric is used when the 95th percentile is reported.

#### 99th percentile

```text
PT99 = 99th percentile of scenario_objective_value
```

This metric is used to measure extreme out-of-sample performance. A lower PT99 value indicates better protection against adverse scenarios.

---

## 10. Example of the out-of-sample testing logic

For an instance with:

```text
Jobs = 20
Machines = 5
Scenarios = 20
```

the evaluation procedure is:

1. There are 20 vessels and 5 berths.
2. A solution matrix `vjk` assigns each vessel to one berth.
3. For each of the 20 scenarios:
   - select vessels assigned to each berth;
   - sort them by `rj + tjk^s`;
   - compute start times and completion times;
   - obtain the maximum completion time of each berth;
   - sum berth-level completion values;
   - add the fixed assignment cost.
4. After all scenarios are evaluated, compute `Obj/Avg`, `PT85`, `PT95`, and `PT99`.

---

## 11. Relationship with manuscript tables

The folders in this repository are organized according to the tables in the manuscript.

For example:

```text
Table_4_3_instance_data/
Table_4_3_solution_data/
Table_4_3_test_data/
```

These folders correspond to the data used for Table 4.3.

Their meanings are:

- `Table_4_3_instance_data/`: instance data used to construct the problem;
- `Table_4_3_solution_data/`: assignment solution matrices obtained by different models;
- `Table_4_3_test_data/`: out-of-sample scenario data used to evaluate the solutions.

Similarly:

```text
Table_4_4_instance_data/
Table_4_4_solution_data/
Table_4_4_test_data/
```

These folders correspond to the data used for Table 4.4.

Their meanings are:

- `Table_4_4_instance_data/`: instance data used to construct the perturbed-distribution experiments;
- `Table_4_4_solution_data/`: assignment solution matrices obtained by different models;
- `Table_4_4_test_data/`: perturbed out-of-sample scenario data used to evaluate the solutions.

---

## 12. Summary of notation

| Notation | Meaning in this repository |
|---|---|
| `Jobs` | number of vessels |
| `Machines` | number of berths |
| `Scenarios` | number of uncertainty scenarios |
| `rj` | ready time of vessel `j` |
| `tjk^s` | berthing time of vessel `j` at berth `k` under scenario `s` |
| `xi_{jk}^s` | processing time of vessel `j` at berth `k` under scenario `s` |
| `cjk` | assignment cost of vessel `j` to berth `k` |
| `vjk` | assignment solution matrix |
| `S_k` | set of vessels assigned to berth `k` |
| `C_k^s` | berth-level completion value under scenario `s` |
| `Obj/Avg` | average out-of-sample objective value |
| `PT85` | 85th percentile of out-of-sample objective values |
| `PT95` | 95th percentile of out-of-sample objective values |
| `PT99` | 99th percentile of out-of-sample objective values |

---

## 13. Purpose of the data

The purpose of these data is to support the reproducibility of the numerical experiments reported in the manuscript.

Readers can use the uploaded data to:

1. read the instance parameters;
2. input a given assignment solution matrix;
3. evaluate the solution under out-of-sample scenarios;
4. reproduce the average and percentile-based performance metrics reported in the manuscript.

The data are provided for academic and reproducibility purposes.

---

## 14. Contact

For questions about the data, please contact the corresponding author of the manuscript.
