# Gas Power Plant Dispatch Valuation using Dynamic Programming based on Heat Rate
 This is the operational analog of exercising a heat-rate call option. The plant produces when the spark spread. (headroom) is sufficiently positive to cover variable costs and start-up costs under a minimum run-time constraint.

### Description:
The objective is to value the intrinsic profit of a gas-fired unit by choosing an optimal ON/OFF schedule given hourly power prices and daily gas prices. This is the operational analog of exercising a heat-rate call option. The plant produces when the spark spread (headroom) is sufficiently positive to cover variable costs and start-up costs under a minimum run-time constraint.

* **Heat rate:** $HR = 10.5 \text{ MMBtu/MWh}$
* **Start-up cost:** $C_{\text{start}} = \$10,000 \text{ per start}$
* **Capacity:** $C = 200 \text{ MW}$
* **Minimum up-time:** $T_{\text{min}} = 16 \text{ hours}$
* **Variable O\&M:** $c_{\text{VOM}} = \$5/\text{MWh}$
* **Gas adder:** $c_{\text{adder}} = \$0.30/\text{MMBtu}$

* $P_{t}^{\text{power}}$: hourly LMP (power price) for hour $t$
* $P_{d}^{\text{gas}}$: daily gas price for date $d$ (constant for 24hrs in a given day)

  #**Concept**:
### Headroom (spark spread):
Headroom is the instantaneous gross margin per MWh if the plant runs in a given hour. It answers if user turns one MWh of fuel into power right now, how many dollars does the user make after paying for fuel and variable O\&M?

For hour $t$ on date $d(t)$:

$$\text{Headroom}_{t} = P_{t}^{\text{power}} - \left(P_{d(t)}^{\text{gas}} + c_{\text{adder}}\right) HR - c_{\text{VOM}} \quad [\$/\text{MWh}].$$

###Hourly profit when ON:
If the unit is **ON**, we usually run it at full capacity because the heat rate is treated as constant (no partial-load efficiency benefit given in problem). So the hourly cashflow is headroom per MWh times MW.

With a constant heat rate and linear revenue, profit is linear in output; if headroom is positive, more output means more profit, so the best choice is the capacity $C$.

$$m_{t} = \text{Headroom}_{t} \cdot C \quad [\$/\text{hour}].$$



### Start-ups and minimum run time:
Its very energy and resource intensive to turn ON the unit for a single hour and back OFFfor free. Starting the plant costs money and operationally you must keep it ON for at least $T_{\text{min}}$ consecutive hours once started.

This increases the complexity in decsision as the unit should only start when the sum of margins over a block of hours is big enough to cover the start cost, and the block must be at least $T_{\text{min}}$ hours long. $T_{\text{min}}$ is 16 hours as per the given data.

If $u_{t} \in \{0, 1\}$ indicates OFF/ON, the number of starts is

$$N_{\text{starts}} = \sum_{t} \max(u_{t} - u_{t-1}, 0), \quad u_{-1} = 0.$$


If the unit starts at hour $t_{\text{s}}$ (i.e., $u_{t_{\text{s}}-1} = 0, u_{t_{\text{s}}} = 1$), then

$$u_{t_{\text{s}}+k} = 1, \quad k = 0, 1, \dots, T_{\text{min}} - 1.$$

### Objective:

Choose the ON/OFF schedule to maximize total profit: operating margin minus start-up costs.

$$\max_{\{u_{t}\}} \Pi = \sum_{t} u_{t} \text{Headroom}_{t} C - N_{\text{starts}} C_{\text{start}}.$$

---
### Modelling - Dynamic Programming (DP)

**Concept.** Because of the minimum up-times, decisions are **path dependent**. Dynamic programming evaluates, at each hour, whether to stay **OFF** or to **start and commit to a block** (of at least $T_{\text{min}}$ hours) and then recurses.

Let

$$S_{k} = \sum_{t=0}^{k-1} m_{t}, \quad \sum_{t=i}^{j} m_{t} = S_{j+1} - S_{i}.$$


Let $dp[i]$ be the **maximum future profit** from hour $i$ onward. Then

$$dp[i] = \max \left( \underbrace{dp[i+1]}_{\text{stay OFF}}, \quad \underbrace{\max_{j \ge i+T_{\text{min}}-1} \left[ (S_{j+1} - S_{i}) - C_{\text{start}} + dp[j+1] \right]}_{\text{start now, run through } j} \right)$$

with boundary $dp[N] = 0$. Reconstruct the schedule by following the recorded choices.

----

## Assumptions

1.  **Intrinsic valuation only:** prices are treated as known (no volatility modeling or discounting within this short horizon).
2.  **Initial state OFF:** $u_{-1} = 0$.
3.  **No min down-time, ramp limits, or outages** (not specified).
4.  **Full-load output when ON** due to constant heat rate.

----
The process flow and methedology is explained above each cell. PLease refer to below explanation.

```python
# --- Cell 1: Load and inspect input data ---

import pandas as pd
from google.colab import files

# Upload both files (you’ll be prompted to choose them from your computer)
uploaded = files.upload()

# Read the Excel sheets
power_df = pd.read_excel('powerPrice.xlsx', sheet_name='powerPrice')
gas_df = pd.read_excel('gasPrice.xlsx', sheet_name='gasPrice')

# Display quick previews
print("Power Price Data:")
display(power_df.head())

print("\nGas Price Data:")
display(gas_df.head())

# Check structure
print("\nPower Price columns:", list(power_df.columns))
print("Gas Price columns:", list(gas_df.columns))
```

