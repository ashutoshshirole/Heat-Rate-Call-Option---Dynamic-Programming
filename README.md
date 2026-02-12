# Heat-Rate-Call-Option---Dynamic-Programming
 This is the operational analog of exercising a heat-rate call option. The plant produces when the spark spread. (headroom) is sufficiently positive to cover variable costs and start-up costs under a minimum run-time constraint.

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

